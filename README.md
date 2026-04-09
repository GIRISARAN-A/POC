# Linux Security Proof of Concept (POC) Tasks

## Overview

This repository contains a structured set of Linux security Proof of Concept (POC) tasks focused on common system misconfigurations, privilege escalation paths, remote access hardening, firewall security, automation, and log-based intrusion detection.

The project is designed for:

* Cybersecurity students
* Linux administrators
* SOC and blue-team beginners
* Privilege escalation learners
* Security auditing practice

Each task demonstrates:

1. **Vulnerable setup**
2. **Exploitation methodology**
3. **Mitigation and hardening steps**
4. **Security best practices**


## Objectives

The main goal of this project is to provide hands-on exposure to:

* Linux permission misconfigurations
* SSH hardening
* Firewall rule validation
* SUID privilege escalation risks
* Automated security auditing
* Log analysis and intrusion prevention


## Prerequisites

Before running these tasks, ensure the following:

* Ubuntu / Debian-based Linux system
* Basic Linux command-line knowledge
* `sudo` privileges
* OpenSSH server installed
* UFW firewall installed
* Fail2Ban installed
* Bash scripting basics

Recommended tools:

* `nmap`
* `netstat` / `ss`
* `journalctl`
* `grep`
* `awk`
* `find`
* `chmod`
* `chown`

# Task 1: User & Permission Misconfigurations

## Description

Improper file permissions can expose sensitive files to unauthorized users, leading to data leakage and privilege escalation.

## Vulnerable Setup

* Create multiple users
* Create a sensitive file
* Assign overly permissive access such as `777`
* Allow non-owners to read confidential content

## Exploitation

An attacker or low-privileged user can:

* Read `/etc/shadow`-like protected content
* Modify scripts executed by privileged users
* Inject malicious commands

## Mitigation

Apply least privilege:

```bash
chmod 600 sensitive_file
chown root:root sensitive_file
```

Best practices:

* Use `640` or `600` for sensitive files
* Restrict write permissions
* Regularly audit home directories and shared folders

# Task 2: Remote Access & SSH Hardening

## Description

Weak SSH configurations are one of the most common Linux attack vectors.

## Vulnerable Setup

Common insecure settings:

* Root login enabled
* Password authentication enabled
* Weak passwords
* Default SSH port exposed

## Risks

Attackers may:

* Brute force credentials
* Use credential stuffing
* Attempt root compromise remotely

## Hardening Steps

Edit SSH configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Recommended secure settings:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

## Best Practices

* Use SSH keys only
* Change default port if required
* Use Fail2Ban
* Restrict SSH via firewall
* Disable unused users

# Task 3: Firewall & Network Security

## Description

A misconfigured firewall may expose unnecessary services and allow unauthorized access.

## Vulnerable Setup

Examples:

* All ports open
* SSH open to public internet
* Development ports exposed
* Database ports open externally

## Enumeration

Use scanning tools:

```bash
nmap localhost
ss -tulnp
```

## Mitigation with UFW

Enable firewall:

```bash
sudo ufw enable
```

Allow only required services:

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

Deny unwanted access:

```bash
sudo ufw deny 3306
```

## Best Practices

* Follow deny-by-default model
* Expose minimum ports
* Restrict admin services by IP
* Audit firewall rules regularly

# Task 4: SUID & Privilege Escalation

## Description

SUID binaries can be abused to execute commands with elevated privileges.

## Vulnerable Setup

Example insecure SUID assignment:

```bash
chmod u+s vulnerable_binary
```

## Exploitation

An attacker may:

* Spawn root shell
* Abuse GTFOBins-listed binaries
* Execute arbitrary privileged commands

Example enumeration:

```bash
find / -perm -4000 2>/dev/null
```

## Mitigation

Remove unnecessary SUID:

```bash
chmod u-s vulnerable_binary
```

## Best Practices

* Audit SUID binaries frequently
* Remove custom SUID scripts
* Use sudoers rules instead
* Compare binaries with GTFOBins database

# Task 5: Automated Security Auditing & Scripting

## Description

Security automation improves visibility and reduces response time.

## Security Audit Script

Create `security_check.sh`

```bash
#!/bin/bash

echo "Logged in users:"
who

echo "Running services:"
systemctl list-units --type=service --state=running

echo "Open ports:"
ss -tulnp

echo "SUID files:"
find / -perm -4000 2>/dev/null
```

## Run Script

```bash
chmod +x security_check.sh
./security_check.sh
```

## Mitigation Strategy

Automate with cron:

```bash
crontab -e
```

Example:

```text
0 * * * * /path/to/security_check.sh
```

## Best Practices

* Store reports in `/var/log/security/`
* Alert on unexpected services
* Monitor new SUID files
* Check failed logins automatically

# Task 6: Log Analysis & Intrusion Detection

## Description

System logs are critical for identifying brute force attempts, privilege misuse, and persistence mechanisms.

## Log Sources

Important log files:

* `/var/log/auth.log`
* `/var/log/syslog`
* `/var/log/kern.log`
* `journalctl`

## Detect Failed Logins

```bash
grep "Failed password" /var/log/auth.log
```

## Detect Suspicious Activity

```bash
last
lastb
journalctl -xe
```

## Mitigation with Fail2Ban

Install:

```bash
sudo apt install fail2ban
```

Enable:

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Check status:

```bash
sudo fail2ban-client status
```

## Best Practices

* Monitor authentication failures
* Ban repeated offenders
* Centralize logs
* Rotate logs securely
* Use SIEM integration if possible


# Learning Outcomes

After completing this project, you will understand:

* Linux access control weaknesses
* Secure SSH administration
* Firewall attack surface reduction
* SUID-based privilege escalation
* Security automation with Bash
* Basic intrusion detection workflow


# Suggested Repository Structure

```text
linux-security-poc/
├── README.md
└── task6_log_analysis
```

# Disclaimer

This project is strictly for:

* Educational use
* Personal lab environments
* Authorized systems only

Do not run these techniques on systems without explicit permission.




<p align="center">
  <sub>Cybersecurity | Linux | Security Automation | Privilege Escalation Research</sub>
</p>
