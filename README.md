---
- name: Restart Contrail services on Contrail nodes
  hosts: contrail_host
  serial: 1  # Ensures one node at a time
  tasks:

    - name: Restart Contrail Controller service
      command: /usr/sbin/service supervisor-config restart
      register: result
      changed_when: "'supervisor-config restart' in result.stdout"

    - name: Wait for 10 minutes after restarting on a controller node
      pause:
        minutes: 10

- name: Restart Contrail services on Contrail Analytics nodes
  hosts: contrail_analytics_host
  serial: 1  # Ensures one node at a time
  tasks:

    - name: Restart Contrail Analytics service
      command: /usr/sbin/service supervisor-analytics restart
      register: result
      changed_when: "'supervisor-analytics restart' in result.stdout"

    - name: Wait for 10 minutes after restarting on an analytics node
      pause:
        minutes: 10
