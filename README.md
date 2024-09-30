---
- hosts: all
  become: yes
  gather_facts: true
  tasks:
#===================================================================================================================================
# Ubuntu
#===================================================================================================================================
  - name: "Download 'attradius-cbb_4.5_amd64.deb' to '/tmp' when OS is Ubuntu"
    ansible.builtin.get_url:
      url: https://mirrors.it.att.com/pub/custom/SD/nasInstall/radius/mfa/attradius-cbb_4.5_amd64.deb
      dest: /tmp
      force: true
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
    tags: 'install'

  - name: "Check '/tmp/attradius-cbb_4.5_amd64.deb' exists when OS is Ubuntu"
    stat:
      path: /tmp/attradius-cbb_4.5_amd64.deb
    register: attradius
    failed_when: 
      - ansible_distribution == 'Ubuntu' and not attradius.stat.exists
    tags: 'install'

  - name: "Install 'attradius-cbb_4.5_amd64.deb' package when OS is Ubuntu"
    ansible.builtin.apt:
      deb: /tmp/attradius-cbb_4.5_amd64.deb
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
    tags: 'install'

  - name: "Remove 'attradius-cbb_4.5_amd64.deb' from '/tmp' when OS is Ubuntu"
    ansible.builtin.file:
      path: /tmp/attradius-cbb_4.5_amd64.deb
      state: absent
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
    tags: 'install'

  - name: "Update /etc/raddb/server file on Ubuntu"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
    tags: 'update_raddb'

#===================================================================================================================================
# RedHat/CentOS 7
#===================================================================================================================================
  - name: "Download 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' to '/tmp' when OS is RedHat7 based"
    ansible.builtin.get_url:
      url: https://mirrors.it.att.com/pub/custom/SD/nasInstall/radius/mfa/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
      dest: /tmp
      force: true
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
    tags: 'install'

  - name: "Check '/tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' exists when OS is RedHat7 based"
    stat:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
    register: authcbb7
    failed_when: 
      - ansible_distribution == 'RedHat' 
      - ansible_distribution_major_version == '7' and not authcbb7.stat.exists
    tags: 'install'

  - name: "Install 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' package when OS is RedHat7 based"
    ansible.builtin.yum:
      name: /tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
      state: present
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
    tags: 'install'

  - name: "Remove 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' from '/tmp' when OS is RedHat"
    ansible.builtin.file:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
      state: absent
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
    tags: 'install'

  - name: "Update /etc/raddb/server file on RedHat7"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
    tags: 'update_raddb'

#===================================================================================================================================
# RedHat/CentOS 8
#===================================================================================================================================
  - name: "Download 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' to '/tmp' when OS is RedHat8 based"
    ansible.builtin.get_url:
      url: https://mirrors.it.att.com/pub/custom/SD/nasInstall/radius/mfa/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
      dest: /tmp
      force: true
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
    tags: 'install'

  - name: "Check '/tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' exists when OS is RedHat8 based"
    stat:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
    register: authcbb8
    failed_when: 
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8' and not authcbb8.stat.exists
    tags: 'install'

  - name: "Install 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' package when OS is RedHat8 based"
    ansible.builtin.yum:
      name: /tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
      state: present
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
    tags: 'install'

  - name: "Remove 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' from '/tmp' when OS is RedHat"
    ansible.builtin.file:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
      state: absent
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
    tags: 'install'

  - name: "Update /etc/raddb/server file on RedHat8"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
    tags: 'update_raddb'
