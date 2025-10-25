![Sigma Rules](https://github.com/user-attachments/assets/4e84d52b-30f9-42e2-8138-2917f7986b09)

# Windows Security Detection Rules

This is a small collection of Sigma rules for Windows threat detection. They focus on activities commonly seen during credential theft and lateral movement. Originally created as a learning project, they’ve turned out to be quite useful for testing detection methods in a lab environment.

# Rules Overview
### lsass_access.yml - LSASS Memory Access

* Detects when a process tries to read LSASS memory - classic sign of credential dumping.
* System processes are excluded to reduce noise in alerts.
* You’ll need Sysmon (Event ID 10) for this one.

### powershell_enc_command.yml - Encoded PowerShell Commands

* Flags PowerShell commands using encoded or obfuscated input.
* Also looks for -NoProfile or -WindowStyle Hidden, which attackers often use.
* Some administrative scripts are excluded to reduce noise, although occasional hits may still occur.

### rdp_success_nonwhitelist.yml - Unusual RDP Logins

* Alerts on successful RDP logins from IPs outside your normal ranges.
* Good way to spot remote access that probably shouldn’t happen.
* The whitelist can be adjusted directly in the YAML file.

# Setup

* Install Sigma CLI first:
```
pip install sigma-cli
```

* Then convert the rules for whatever SIEM you’re using.

### For Splunk:
```
sigma convert -t splunk -p splunk_windows rules/
```

### For Elasticsearch:
```
sigma convert -t lucene -p ecs_windows rules/
```
# Notes

### Each rule has:

* Detection logic with practical exclusions

* ATT&CK mapping

* Quick notes on false positives

* Tips for tuning based on your logs

# Adjusting for Your Environment

* For example, updating the RDP whitelist:
```
selection_whitelist_cidr:
  IpAddress|cidr:
    - 10.0.0.0/8
    - 192.168.0.0/16
```

### You might also want to filter:

* Admin tools touching LSASS

* Automation PowerShell scripts

* Known management IPs

# Testing

### Before you deploy, check your rules:
```
sigma check rules/
```

# Use Cases

* SOC teams: Enhance detection coverage and establish baseline alerts.

* Threat hunters: Investigate and validate potential attack patterns.

* Incident responders: Assess credential access and lateral movement activities.

* Learners/researchers: Experiment with and refine detection logic.

# Requirements

* Sysmon (for LSASS and PowerShell rules)

* Windows Security Logs (for RDP rule)

* A SIEM like Splunk or Elasticsearch

# Final Note

### These rules are intended as practical examples and may require adjustment.
### Always test them first and configure according to your environment.
