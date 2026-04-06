## Overview
This project demonstrates a basic Security Operations Center (SOC) lab using Wazuh for endpoint monitoring and File Integrity Monitoring (FIM).

## Setup
- Wazuh Manager installed on Ubuntu VM
- Windows machine configured as Wazuh agent
- Communication established via TCP (port 1514)


The following activities were simulated and successfully detected by Wazuh:

Advanced Integrity & Security Events:
-File permission changes (e.g., chmod)
-Ownership changes (user/group modifications)
-Unauthorized access attempts to sensitive files
-Hidden file creation (e.g., files starting with .)
-Monitoring of critical system directories (e.g., /etc, /var/log)
-Detection of changes in configuration files
-Detection of suspicious file activity in monitored directories

Security-Relevant Use Cases:
-Detection of tampering with system configuration files
-Identifying potential persistence mechanisms (e.g., attacker modifying startup files)
-Monitoring log file integrity to prevent log wiping
-Detecting unauthorized changes to security-critical files

## Screenshots
See the screenshots folder in "lab-files" branch

## Skills Gained
- SIEM deployment and configuration
- Endpoint monitoring and log analysis
- File integrity monitoring
- Basic SOC operations workflow

## Tools Used
- Wazuh
- Ubuntu
- Windows
- TCP/IP Networking
