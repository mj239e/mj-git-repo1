# mj-git-repo1
mj git repo for testing


I have this playbook which installs mfa for my servers including rhel and ubuntu systems
but fails with the following error below when running on only rhel8 and ubuntu22 systems 
I want it to skipp if the system is not rhel 7 and doesn't have to confirm the file 


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
      - ansible_distribution_major_version == '20' or
        ansible_distribution_major_version == '22'
    tags: 'install'

  - name: "Check '/tmp/attradius-cbb_4.5_amd64.deb' exists when OS is Ubuntu"
    stat:
      path: /tmp/attradius-cbb_4.5_amd64.deb
    register: attradius
    failed_when:
      - attradius.stat.exists is false
      - ansible_distribution == 'Ubuntu'
    tags: 'install'

  - name: "Remove 'attradius-cbb_4.5_amd64.deb' from '/tmp' when OS is Ubuntu"
    ansible.builtin.file:
      path: /tmp/attradius-cbb_4.5_amd64.deb
      state: absent
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version == '20' or
        ansible_distribution_major_version == '22'
    tags: 'install'

  - name: "Update /etc/raddb/server file on Ubuntu"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        #AuthGateway.cso.att.com Nost1k 30
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version == '20' or
        ansible_distribution_major_version == '22'
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
      - authcbb7.stat.exists is false
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version != '7'
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
        #AuthGateway.cso.att.com Nost1k 30
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
    tags: 'update_raddb'

#===================================================================================================================================
# RedHat/CentOS 8
#===================================================================================================================================
  - name: "Download 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' to '/tmp' when OS is RedHat8 based"
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
      - authcbb8.stat.exists is false
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version != '8'
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
        #AuthGateway.cso.att.com Nost1k 30
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
    tags: 'update_raddb'





mj239e@njcdtl01mj239e:~/att/repos/mfa$ ansible-playbook mfa_1.4.yaml -i /home/mj239e/att/pam_4.5_inventory 

PLAY [all] ******************************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************************************************
ok: [d1labopstestsrv.infra.labops.att.com]
ok: [srlixjmp04.infra.labops.att.com]

TASK [Download 'attradius-cbb_4.5_amd64.deb' to '/tmp' when OS is Ubuntu] ***************************************************************************************************************************************************************************************************
skipping: [d1labopstestsrv.infra.labops.att.com]
ok: [srlixjmp04.infra.labops.att.com]

TASK [Check '/tmp/attradius-cbb_4.5_amd64.deb' exists when OS is Ubuntu] ****************************************************************************************************************************************************************************************************
ok: [srlixjmp04.infra.labops.att.com]
ok: [d1labopstestsrv.infra.labops.att.com]

TASK [Remove 'attradius-cbb_4.5_amd64.deb' from '/tmp' when OS is Ubuntu] ***************************************************************************************************************************************************************************************************
skipping: [d1labopstestsrv.infra.labops.att.com]
changed: [srlixjmp04.infra.labops.att.com]

TASK [Update /etc/raddb/server file on Ubuntu] ******************************************************************************************************************************************************************************************************************************
skipping: [d1labopstestsrv.infra.labops.att.com]
changed: [srlixjmp04.infra.labops.att.com]

TASK [Download 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' to '/tmp' when OS is RedHat7 based] *****************************************************************************************************************************************************************************
skipping: [srlixjmp04.infra.labops.att.com]
skipping: [d1labopstestsrv.infra.labops.att.com]

TASK [Check '/tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' exists when OS is RedHat7 based] ******************************************************************************************************************************************************************************
ok: [srlixjmp04.infra.labops.att.com]
fatal: [d1labopstestsrv.infra.labops.att.com]: FAILED! => {"changed": false, "failed_when_result": true, "stat": {"exists": false}}

TASK [Remove 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' from '/tmp' when OS is RedHat] ************************************************************************************************************************************************************************************
skipping: [srlixjmp04.infra.labops.att.com]

TASK [Update /etc/raddb/server file on RedHat7] *****************************************************************************************************************************************************************************************************************************
skipping: [srlixjmp04.infra.labops.att.com]

TASK [Download 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' to '/tmp' when OS is RedHat8 based] *****************************************************************************************************************************************************************************
skipping: [srlixjmp04.infra.labops.att.com]

TASK [Check '/tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' exists when OS is RedHat8 based] ******************************************************************************************************************************************************************************
ok: [srlixjmp04.infra.labops.att.com]

TASK [Remove 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' from '/tmp' when OS is RedHat] ************************************************************************************************************************************************************************************
skipping: [srlixjmp04.infra.labops.att.com]

TASK [Update /etc/raddb/server file on RedHat8] *****************************************************************************************************************************************************************************************************************************
skipping: [srlixjmp04.infra.labops.att.com]

PLAY RECAP ******************************************************************************************************************************************************************************************************************************************************************
d1labopstestsrv.infra.labops.att.com : ok=2    changed=0    unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
srlixjmp04.infra.labops.att.com : ok=7    changed=2    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0   

