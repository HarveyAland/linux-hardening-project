Linux Hardening Project (CentOS 9)# Linux Hardening Project (CentOS 9)

## Overview
This project demonstrates the hardening of a CentOS 9 virtual machine following security best practices. It includes real-world sysadmin tasks such as service minimization, SSH configuration, PAM tuning, GRUB protection, malware scanning, and integrity checks using AIDE. It was designed for a single-user environment with a non-production use case.

## Goals
- Raise the Lynis hardening index from 66 to 80
- Apply multiple layers of security across authentication, services, permissions, and boot process
- Build a reusable and presentable Linux project for GitHub
- Include real command outputs, before/after audits, and screenshots for evidence

## System Info
- **OS**: CentOS 9 Stream
- **User**: haland (non-root)
- **Audit Tool**: Lynis 3.1.4

## File Tree

linux-hardening-project/
├── configs/
│ ├── 40_custom.txt
│ ├── dnf_automatic_before.txt
│ ├── dnf_automatic_after.txt
│ ├── dnf_automatic_status.txt
│ ├── firewalld_rules_before.txt
│ ├── firewalld_rules_after.txt
│ ├── grub_config_before.txt
│ ├── installed_packages_before.txt
│ ├── installed_packages_after.txt
│ ├── services_running_before.txt
│ ├── services_running_after.txt
│ ├── sshd_config_before.txt
│ ├── sshd_config_after.txt
│ ├── sshd_config_final.txt
│ ├── sudo_users_after.txt
│ ├── users_before.txt
│ └── users_after.txt
├── reports/
│ ├── aide_initial_check.txt
│ ├── lynis_before.txt
│ ├── lynis_final.txt
│ ├── rkhunter_scan.txt
│ └── suid_sgid_audit.txt
└── screenshots/
├── finding_and_removing_unused_accounts.png
├── grub_root_login_for_editing_boot.png
├── haland_only_has_sudo_rights.png
├── in_grub_editor_password_prompt.png
├── lynis_before_hardening.png
├── lynis_after_hardening.png
├── making_changes_to_ssh_and_firewall.png
├── services_running_after_cleanup.png
├── ssh_success_2222.png
└── testing_root_entry_on_boot.png

## Key Security Enhancements

### 🔐 SSH Configuration Hardening
- Changed SSH port
- Set `PermitRootLogin no`
- Set `MaxSessions 2`
- Disabled `X11Forwarding` and `AllowAgentForwarding`
- Configured `LogLevel VERBOSE`, `MaxAuthTries`, and `AllowTcpForwarding`
- **Files:** `sshd_config_before.txt`, `sshd_config_after.txt`, `sshd_config_final.txt`
  
![SSH config changes](screenshots/making_changes_to_ssh_and_firewall.png)
![SSH success](screenshots/ssh_success_2222.png)

### 🔒 PAM and Password Policy
- Enforced strong password complexity in `/etc/security/pwquality.conf`
- Tuned `/etc/pam.d/system-auth` to add retry options and password aging
- Locked down root access only after user `haland` was verified to have sudo rights

![Only haland has sudo access](screenshots/haland_only_has_sudo_rights.png)

### 🛡 GRUB Bootloader Protection
- Added hashed password using `grub2-mkpasswd-pbkdf2`
- Modified `/etc/grub.d/40_custom`
- Rebuilt GRUB config
- Tested GRUB password prompt and verified password protection

![GRUB root login](screenshots/grub_root_login_for_editing_boot.png)  
![GRUB edit prompt](screenshots/in_grub_editor_password_prompt.png)  
![Root entry on boot](screenshots/testing_root_entry_on_boot.png)

### 🧱 Service and Package Minimization
- Removed unused packages: `telnet`, `ftp`, `rsh`, etc.
- Stopped and disabled services like Avahi and Colord
- Captured services before/after

![Services after cleanup](screenshots/services_running_after_cleanup.png)

### 🧾 Login Banners
- Added banners to `/etc/issue` and `/etc/issue.net`
- Even though Lynis still flags them, manual verification confirmed presence

### 🕵️ Malware Scanner & Integrity Checks
- Installed `rkhunter`, performed full scan, and configured cron job for daily runs
- Installed and configured `aide`, built baseline database, and saved result
- **Files:** `rkhunter_scan.txt`, `aide_initial_check.txt`


### 🔍 File Permissions and Audits
- Set sticky bit on `/tmp` and `/var/tmp`
- Audited and saved SUID/SGID results
- Removed or locked down unnecessary user accounts
- Verified only one sudoer account exists
- **Files:** `suid_sgid_audit.txt`, `users_before.txt`, `users_after.txt`

![Unused account removal](screenshots/finding_and_removing_unused_accounts.png)

### 🚫 Disabled Kernel Protocols
- Blacklisted unused protocols: `dccp`, `sctp`, `rds`, `tipc`
- Configured `/etc/modprobe.d/disable_protocols.conf`

### 🔧 Miscellaneous Improvements
- Updated `/etc/login.defs` with password aging rules
- Set `fs.suid_dumpable = 0` in `/etc/sysctl.conf`
- Verified hostname was correctly set
- Hardened compilers by ensuring `gcc` was not installed
- Disabled USB storage modules using `/etc/modprobe.d/` to prevent removable device use
- Updated firewall rules to match SSH port change and enhance security

## Final Lynis Hardening Result
![Lynis before hardening](screenshots/lynis_before_hardening.png)  
![Lynis after hardening](screenshots/lynis_after_hardening.png)
- All key suggestions addressed or intentionally excluded due to scope
**Score:** 66 ➝ 80 ✅

## Known Limitations
Some Lynis suggestions were skipped or flagged incorrectly:
- **Separate partitions** for `/home`, `/tmp`, `/var` were skipped as this is a single-VM lab
- **External log host** not used due to scope
- **Login banner** shows in config, but Lynis still flags it — screenshot proves presence
- Auditd rule set could not be verified properly due to service limitations in CentOS Stream


##Root Lockout Recovery
During hardening, a configuration error in PAM temporarily prevented login and sudo access. Recovery involved booting into single-user mode, manually editing /etc/shadow and /etc/sudoers, and creating a temporary rescueuser with root access. Once haland was restored and reverified with sudo privileges, the temporary user was safely removed. This experience demonstrates real-world troubleshooting and recovery skills in a locked-out Linux environment.

## Conclusion
- All key suggestions addressed or intentionally excluded due to scopeThis project shows realistic Linux system hardening under practical constraints. The system achieved a secure configuration, reflected in the improved Lynis score and supported with clear documentation and screenshots. The final state demonstrates a responsible balance between strict hardening and usability on a non-production system.
