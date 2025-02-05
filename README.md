---
- name: Fetch Mechanized User and Zone
  hosts: all
  become: yes
  gather_facts: no
  tasks:
    - name: Extract mechanized user directly (Final Fix)
      shell: >-
        kubectl -n ucp get secret keystone-etc -o jsonpath='{.data.keystone\.nc\.json}' | base64 -d | jq -r '.. | objects | select(has("user")) | .user'
      register: mechanized_user_result
      changed_when: false
      failed_when: mechanized_user_result.rc != 0

    - name: Set mechanized user and zone
      set_fact:
        mechanized_user: "{{ mechanized_user_result.stdout | trim }}"
        zone: "{{ inventory_hostname.split('.')[0] }}"

    - name: Save output to a file
      lineinfile:
        path: "/home/mj239e/mechanized_users_zones.txt"
        line: "{{ inventory_hostname }} - User: {{ mechanized_user }}, Zone: {{ zone }}"
        create: yes
      delegate_to: localhost
