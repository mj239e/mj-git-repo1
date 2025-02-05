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

    - name: Debug keystone output (formatted JSON)
      debug:
        msg: "{{ keystone_output.stdout | to_nice_json }}"

    - name: Parse JSON Data
      set_fact:
        keystone_json: "{{ keystone_output.stdout | from_json }}"

    - name: Extract mechanized user and zone
      set_fact:
        mechanized_user: "{{ keystone_json.user | default('UNKNOWN') }}"
        zone: "{{ inventory_hostname.split('.')[0] }}"

    - name: Display results
      debug:
        msg: "User: {{ mechanized_user }}, Zone: {{ zone }}"

    - name: Save output to a file
      lineinfile:
        path: "/home/mj239e/mechanized_users_zones.txt"
        line: "{{ inventory_hostname }} - User: {{ mechanized_user }}, Zone: {{ zone }}"
        create: yes
      delegate_to: localhost
