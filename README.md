---
- name: Fetch Mechanized User and Zone
  hosts: control_plane_nodes
  gather_facts: no
  tasks:
    - name: Run kubectl command to fetch keystone config
      command: >-
        kubectl -n ucp -o go-template='{{ index .data "keystone.nc.json" }}' get secret keystone-nc | base64 --decode
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
        path: "/tmp/mechanized_users_zones.txt"
        line: "{{ inventory_hostname }} - User: {{ mechanized_user }}, Zone: {{ zone }}"
        create: yes
      delegate_to: localhost
