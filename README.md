# mj-git-repo1
mj git repo for testing


---
- name: Gather and display system facts
  hosts: all
  gather_facts: yes

  tasks:
    - name: Print all gathered facts
      debug:
        var: ansible_facts

    - name: Print specific fact (e.g., OS distribution)
      debug:
        msg: "The OS distribution is: {{ ansible_facts['ansible_distribution'] }}"

    - name: Print specific fact (e.g., Hostname)
      debug:
        msg: "The hostname is: {{ ansible_facts['ansible_hostname'] }}"
