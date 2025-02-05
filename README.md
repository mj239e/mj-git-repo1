---
- name: Fetch Mechanized User and Zone
  hosts: all
  become: yes
  gather_facts: no
  tasks:
    - name: Run kubectl command to fetch keystone config
      shell: >-
        kubectl -n ucp get secret keystone-etc -o jsonpath='{.data.keystone\.nc\.json}' | base64 -d
      register: keystone_output
      changed_when: false
      failed_when: keystone_output.rc != 0

    - name: Ensure keystone output is valid JSON
      set_fact:
        keystone_valid_json: "{{ keystone_output.stdout is search('^{.*}$') }}"

    - name: Fail if JSON is invalid
      fail:
        msg: "Keystone output is not valid JSON! Check manually."
      when: not keystone_valid_json

    - name: Clean JSON output to remove extra spaces and newlines
      set_fact:
        keystone_cleaned: "{{ keystone_output.stdout | regex_replace('\\n', '') | regex_replace('\\t', '') }}"

    - name: Parse JSON Data
      set_fact:
        keystone_json: "{{ keystone_cleaned | from_json }}"

    - name: Extract mechanized user and zone
      set_fact:
        mechanized_user: "{{ keystone_json.user | default('UNKNOWN') }}"
        zone: "{{ inventory_hostname.split('.')[0] }}"

    - name: Save output to a file
      lineinfile:
        path: "/home/mj239e/mechanized_users_zones.txt"
        line: "{{ inventory_hostname }} - User: {{ mechanized_user }}, Zone: {{ zone }}"
        create: yes
      delegate_to: localhost
