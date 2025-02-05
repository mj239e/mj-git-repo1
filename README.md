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

    - name: Extract mechanized user using jq (Direct JSON Query)
      shell: >-
        echo '{{ keystone_output.stdout }}' | jq -r '.user // "UNKNOWN"'
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
