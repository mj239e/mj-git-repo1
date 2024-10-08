---
- hosts: all
  become: yes
  gather_facts: true
  tasks:

#===================================================================================================================================
# Ubuntu 20 & 22 - Installation
#===================================================================================================================================
  - name: "Download 'attradius-cbb_4.5_amd64.deb' to '/tmp' when OS is Ubuntu 20 or 22"
    ansible.builtin.get_url:
      url: https://mirrors.it.att.com/pub/custom/SD/nasInstall/radius/mfa/attradius-cbb_4.5_amd64.deb
      dest: /tmp
      force: true
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['ubuntu20', 'ubuntu22', 'install']

  - name: "Check '/tmp/attradius-cbb_4.5_amd64.deb' exists when OS is Ubuntu 20 or 22"
    stat:
      path: /tmp/attradius-cbb_4.5_amd64.deb
    register: attradius
    failed_when:
      - ansible_distribution == 'Ubuntu' and not attradius.stat.exists
    when:
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['ubuntu20', 'ubuntu22', 'install']

  - name: "Install 'attradius-cbb_4.5_amd64.deb' package when OS is Ubuntu 20 or 22"
    ansible.builtin.apt:
      deb: /tmp/attradius-cbb_4.5_amd64.deb
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['ubuntu20', 'ubuntu22', 'install']

  - name: "Remove 'attradius-cbb_4.5_amd64.deb' from '/tmp' when OS is Ubuntu 20 or 22"
    ansible.builtin.file:
      path: /tmp/attradius-cbb_4.5_amd64.deb
      state: absent
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['ubuntu20', 'ubuntu22', 'cleanup']

  - name: "Update /etc/raddb/server file on Ubuntu 20 or 22"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['ubuntu20', 'ubuntu22', 'update_raddb']

#===================================================================================================================================
# Ubuntu 20 & 22 - Removal
#===================================================================================================================================
  - name: "Purge 'attradius-cbb_4.5_amd64.deb' package on Ubuntu 20 or 22"
    command: dpkg --purge attradius-cbb
    when:
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_major_version in ['20', '22']
      - "'remove' in ansible_run_tags"  # Only run if remove tag is used
    tags: ['ubuntu20', 'ubuntu22', 'remove']

#===================================================================================================================================
# RedHat/CentOS 7 - Installation with rpm command and GPG check skipped
#===================================================================================================================================
  - name: "Download 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' to '/tmp' when OS is RedHat7 based"
    ansible.builtin.get_url:
      url: https://mirrors.it.att.com/pub/custom/SD/nasInstall/radius/mfa/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
      dest: /tmp
      force: true
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel7', 'install']

  - name: "Check '/tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' exists when OS is RedHat7 based"
    stat:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
    register: authcbb7
    failed_when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7' and not authcbb7.stat.exists
    when:
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel7', 'install']

  - name: "Install 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' package using RPM command and skip GPG check"
    command: rpm -Uvh --nosignature /tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel7', 'install']

  - name: "Remove 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' from '/tmp' when OS is RedHat7"
    ansible.builtin.file:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm
      state: absent
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel7', 'cleanup']

  - name: "Update /etc/raddb/server file on RedHat7"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel7', 'update_raddb']

#===================================================================================================================================
# RedHat/CentOS 7 - Removal
#===================================================================================================================================
  - name: "Remove 'pam_radius_auth_cbb-1.4.5-1.el7.x86_64.rpm' package on RedHat7"
    command: rpm -e pam_radius_auth_cbb
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '7'
      - "'remove' in ansible_run_tags"  # Only run if remove tag is used
    tags: ['rhel7', 'remove']

#===================================================================================================================================
# RedHat/CentOS 8 - Installation
#===================================================================================================================================
  - name: "Download 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' to '/tmp' when OS is RedHat8 based"
    ansible.builtin.get_url:
      url: https://mirrors.it.att.com/pub/custom/SD/nasInstall/radius/mfa/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
      dest: /tmp
      force: true
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel8', 'install']

  - name: "Check '/tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' exists when OS is RedHat8 based"
    stat:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
    register: authcbb8
    failed_when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8' and not authcbb8.stat.exists
    when:
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel8', 'install']

  - name: "Install 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' using RPM command with signature disabled"
    command: rpm -Uvh --nosignature /tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel8', 'install']

  - name: "Remove 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' from '/tmp' when OS is RedHat8"
    ansible.builtin.file:
      path: /tmp/pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm
      state: absent
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel8', 'cleanup']

  - name: "Update /etc/raddb/server file on RedHat8"
    copy:
      dest: /etc/raddb/server
      content: |
        # Autoconfigured by ping time
        authgtwy-cbb-uat.is.tci.att.com Nost1k 30
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
      - "'remove' not in ansible_run_tags"  # Skip if remove tag is used
    tags: ['rhel8', 'update_raddb']

#===================================================================================================================================
# RedHat/CentOS 8 - Removal
#===================================================================================================================================
  - name: "Remove 'pam_radius_auth_cbb-1.4.5-1.el8.x86_64.rpm' package on RedHat8"
    command: rpm -e pam_radius_auth_cbb
    when:
      - ansible_distribution == 'RedHat'
      - ansible_distribution_major_version == '8'
      - "'remove' in ansible_run_tags"  # Only run if remove tag is used
    tags: ['rhel8', 'remove']
