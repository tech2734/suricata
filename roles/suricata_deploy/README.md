# suricata_deploy
## **Deploy Suricata**

**Role Description**

This role will install Suricata to both RHEL 7 and RHEL 8 servers.  RHEL 7 installs with yum as a package is available, however RHEL 8 pulls the software down and compiles it as that is the only method at the time this role was written.

**Dependencies**

For RHEL 8 compile, there are some dependencies required, the role itself will download and install the required dependencies:

```yaml
---
  - name: install required packages
    yum:
      - diffutils
      - file-devel
      - gcc
      - jansson-devel
      - make
      - nss-devel
      - libyaml-devel
      - libcap-ng-devel
      - libpcap-devel
      - pcre-devel
      - python3
      - python3-pyyaml
      - rust-toolset
      - zlib-devel

```

**Required Variables**

This role determines which tasks to run based on the ansible_distribution_major_version and ansible_os_family variables.  For this reason gather_facts is set to true on the calling playbook.

```yaml
---
current_version: 6.0.3                      #The version of Suricata install

```

**Example Playbook Usage**

```yaml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: import suricata_deploy role
      import_role:
        name: suricata_deploy
```

**Ansible Tower Template Settings**

Limit should have "Prompt on Launch" set for the template.  Include the host name to install to when prompted.