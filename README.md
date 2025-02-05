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

    - name: Trim extra spaces and ensure clean output
      set_fact:
        keystone_cleaned: "{{ keystone_output.stdout | trim }}"

    - name: Extract mechanized user using regex (No JSON Parsing)
      set_fact:
        mechanized_user: "{{ keystone_cleaned | regex_search('\"user\":\"([^\"]+)\"', '\\1') | default('UNKNOWN') }}"
        zone: "{{ inventory_hostname.split('.')[0] }}"

    - name: Save output to a file
      lineinfile:
        path: "/home/mj239e/mechanized_users_zones.txt"
        line: "{{ inventory_hostname }} - User: {{ mechanized_user }}, Zone: {{ zone }}"
        create: yes
      delegate_to: localhost
