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
        msg: "The OS distribution is: {{ ansible_facts['distribution'] }}"

    - name: Print specific fact (e.g., Hostname)
      debug:
        msg: "The hostname is: {{ ansible_facts['hostname'] }}"







      TASK [Print specific fact (e.g., OS distribution)] **************************************************************************************************************************************************************************************************************************************************************************************************
fatal: [rdmlixjmp01.infra.labops.att.com]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'dict object' has no attribute 'ansible_distribution'\n\nThe error appears to be in '/home/mj239e/mfa/gather_facts.yaml': line 11, column 7, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n    - name: Print specific fact (e.g., OS distribution)\n      ^ here\n"}
