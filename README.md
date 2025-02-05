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

    - name: Trim extra spaces and ensure clean JSON
      set_fact:
        keystone_cleaned: "{{ keystone_output.stdout | trim }}"

    - name: Debug first 500 characters of output (safe check)
      debug:
        msg: "{{ keystone_cleaned[:500] }}"  # Print first 500 chars to confirm it's readable

    - name: Attempt JSON Parsing (Safe Fallback)
      set_fact:
        keystone_json: "{{ keystone_cleaned | from_json | default('{}') }}"
      ignore_errors: yes  # Avoid failure if JSON parsing breaks

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
