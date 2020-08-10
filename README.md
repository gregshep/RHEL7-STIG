RHEL 7 DISA STIG
================

[![pipeline status](https://gitlab.com/mindpointgroup/lockdown-enterprise/rhel-7-stig/badges/master/pipeline.svg)](https://gitlab.com/mindpointgroup/lockdown-enterprise/rhel-7-stig/commits/master)

Configure a RHEL 7 system to be DISA STIG compliant. All findings will be audited by default. Non-disruptive CAT I, CAT II, and CAT III findings will be corrected by default. Disruptive finding remediation can be enabled by setting `rhel7stig_disruption_high` to `yes`.

This role is based on RHEL 7 DISA STIG: [Version 2, Rel 4 released on July 26, 2019](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_Red_Hat_Enterprise_Linux_7_V2R4_STIG.zip).

Requirements
------------

RHEL 7 or CentOS 7 - Other versions are not supported.


Dependencies
------------

The following packages must be installed on the controlling host/host where ansible is executed:

- python2-passlib (or just passlib, if using python3)
- python-lxml
- python-xmltodict
- python-jmespath

Package 'python-xmltodict' is required if you enable the OpenSCAP tool installation and run a report. Packages python(2)-passlib and python-jmespath are required for tasks with custom filters or modules. These are all required on the controller host that executes Ansible.


Role Variables
--------------

| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `rhel7stig_oscap_scan` | `no` | Install and run an OpenSCAP report before and after the application of this role        |
| `rhel7stig_report_installfrominternet` | `no` | Whether to install xmltojson from Fedora's Repositories, needed if epel is not configured on CentOS. |
| `rhel7stig_cat1_patch` | `yes` | Correct CAT I findings        |
| `rhel7stig_cat2_patch` | `yes`  | Correct CAT II findings       |
| `rhel7stig_cat3_patch` | `yes`  | Correct CAT III findings      |
| `rhel_07_######` | [see defaults/main.yml](./defaults/main.yml)  | Individual variables to enable/disable each STIG ID. |
| `rhel7stig_gui` | `no` | Whether or not to run tasks related to auditing/patching the desktop environment |
| `rhel7stig_system_is_router` | `no` | Run tasks that disable router functions. |
| `rhel7stig_antivirus_required` | `no` | Run tasks related to Anit-Virus package installation. |
| `rhel7stig_av_package` | [see defaults/main.yml](./defaults/main.yml) | Anti-virus package(s) to install and service to start and enable. |
| `rhel7stig_time_service` | `chronyd` | Set to `ntpd` or `chronyd`. |
| `rhel7stig_time_service_configs` | [see defaults/main.yml](./defaults/main.yml) | Time service packages and service configs. |
| `rhel7stig_firewall_service` | `firewalld` | Set to `firewalld` or `iptables`. |
| `rhel7stig_vsftpd_required` | `no` | If set to `no`, remove `vsftpd`. |
| `rhel7stig_tftp_required` | `no` | If set to `no`, remove `tftp` client and server packages. |
| `rhel7stig_autofs_required` | `no` | If set to `no`, disable `autofs` service. |
| `rhel7stig_kdump_required` | `no` | If set to `no`, disable `kdump` service. |
| `rhel7stig_snmp_community` | `Endgam3Ladyb0g` | SNMP community string that will replace `public` and `private` in `snmpd.conf`. |
| `rhel7stig_bootloader_password` | `Boot1tUp!` | GRUB2 bootloader password. This should be stored in an Ansible Vault. |
| `rhel7stig_boot_superuser` | `root` | Used to set the boot superuser in the GRUB2 config. |
| `rhel7stig_boot_password_config` | [see defaults/main.yml](./defaults/main.yml) | GRUB2 bootloader password configuration. |
| `rhel7stig_aide_cron` | [see defaults/main.yml](./defaults/main.yml) | AIDE Cron settings |
| `rhel7stig_maxlogins` | `10` | Set maximum number of simultaneous system logins (RHEL-07-040000) |
| `rhel7stig_logon_banner` | [see defaults/main.yml](./defaults/main.yml) | Logon banner displayed when logging in to the system. Defaults to nicely formatted standard logon banner. |
| `rhel7stig_password_complexity` | see below for specific settings | Dictionary of password complexity settings |
| `rhel7stig_password_complexity.ucredit` | `-1` | Minimum number of upper-case characters to be set in a new password - expressed as a negative number.  |
| `rhel7stig_password_complexity.lcredit` | `-1` | Minimum number of lower-case characters to be set in a new password - expressed as a negative number.  |
| `rhel7stig_password_complexity.dcredit` | `-1` | Minimum number of numeric characters to be set in a new password - expressed as a negative number.  |
| `rhel7stig_password_complexity.ocredit` | `-1` | Minimum number of special characters to be set in a new password - expressed as a negative number.  |
| `rhel7stig_password_complexity.difok` | `8` | Minimum number of characters in new password that must not be present in the old password.  |
| `rhel7stig_password_complexity.minclass` | `4` | Minimum number of required classes of characters for the new password. (digits, upper, lower, other)  |
| `rhel7stig_password_complexity.maxrepeat` | `3` | Maximum number of allowed same consecutive characters in a new password. |
| `rhel7stig_password_complexity.maxclassrepeat` | `4` | Maximum number of allowed same consecutive characters in the same **class** in the new password. |
| `rhel7stig_password_complexity.minlen` | `15` | Minimum number of characters in a new password. |
| `rhel7stig_shell_session_timeout` | `file: /etc/profile` `timeout: 600` | Dictionary of session timeout setting and file (TMOUT setting can be set in multiple files) |

Example Playbook
----------------

    - hosts: servers
      roles:
          - role: rhel-7-stig
            when:
                - ansible_os_family == 'RedHat'
                - ansible_distribution_major_version | version_compare('7', '=')

