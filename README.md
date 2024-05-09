# mj-git-repo1
mj git repo for testing

---
- name: Setup SSH for user pn153s
  hosts: all
  become: yes

  tasks:
    - name: Create .ssh directory
      file:
        path: /home/pn153s/.ssh
        state: directory
        owner: pn153s
        group: root
        mode: '0755'

    - name: Create authorized_keys file
      file:
        path: /home/pn153s/.ssh/authorized_keys
        state: touch
        owner: pn153s
        group: root
        mode: '0750'

    - name: Add public key to authorized_keys
      lineinfile:
        path: /home/pn153s/.ssh/authorized_keys
        line: your_public_key_here
        create: yes
        owner: pn153s
        group: root
        mode: '0750'

