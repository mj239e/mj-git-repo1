---
- name: Fetch Mechanized User and Zone
  hosts: all
  become: yes
  gather_facts: no
  tasks:
    - name: Run kubectl command to fetch keystone config
      command: >
        kubectl -n ucp get secret keystone-nc -o go-template='{{ "{{ index .data \"keystone.nc.json\" }}" }}' | base64 --decode
      register: keystone_output
      changed_when: false

    - name: Extract mechanized user and zone
      set_fact:
        mechanized_user: "{{ keystone_output.stdout | regex_search('\"user_name_attribute\":\"([^\"]+)\"', '\\1') | first }}"
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
