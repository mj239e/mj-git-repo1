---
- name: Fetch Mechanized User and Zone
  hosts: all
  become: yes
  gather_facts: no
  tasks:
    - name: Extract mechanized user (Logging Output)
      shell: >-
        (kubectl -n ucp get secret keystone-etc -o jsonpath='{.data.keystone\.nc\.json}' | base64 -d | jq -r '.. | objects | select(has("user")) | .user') 2>&1 | tee /tmp/mech_user_output.log
      register: mechanized_user_result
      changed_when: false
      failed_when: mechanized_user_result.rc != 0

    - name: Read log file to debug output
      shell: cat /tmp/mech_user_output.log
      register: command_log
      changed_when: false

    - name: Debug command log output
      debug:
        msg: "{{ command_log.stdout_lines }}"

    - name: Set mechanized user and zone
      set_fact:
        mechanized_user: "{{ mechanized_user_result.stdout | default('UNKNOWN') | trim }}"
        zone: "{{ inventory_hostname.split('.')[0] }}"

    - name: Debug mechanized user and zone before writing to file
      debug:
        msg: "Host: {{ inventory_hostname }}, User: {{ mechanized_user }}, Zone: {{ zone }}"

    - name: Save output to a file
      lineinfile:
        path: "/home/mj239e/mechanized_users_zones.txt"
        line: "{{ inventory_hostname }} - User: {{ mechanized_user }}, Zone: {{ zone }}"
        create: yes
      delegate_to: localhost
