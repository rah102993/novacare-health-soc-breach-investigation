# Investigation Evidence

This directory contains selected evidence collected during the NovaCare Health SOC breach investigation.

The evidence follows the investigation from endpoint monitoring and network validation through SSH authentication analysis, successful remote access, privileged command execution, and MITRE ATT&CK classification.

## Evidence Index

| ID | Evidence | Purpose |
|---|---|---|
| E01 | Wazuh Endpoint Monitoring | Confirms that an Ubuntu endpoint was actively monitored by Wazuh |
| E02 | Kali-to-Ubuntu Connectivity | Confirms network connectivity from the simulated threat source to the monitored Ubuntu endpoint |
| E03 | SSH Authentication Failures and Log Correlation | Shows repeated failed SSH authentication attempts from 192.168.10.100 and subsequent successful authentication in native Ubuntu logs |
| E04 | Wazuh SSH Failure Detection | Shows Wazuh detection of failed SSH authentication activity and associated MITRE ATT&CK classification |
| E05 | Successful SSH Authentication | Confirms successful SSH authentication from 192.168.10.100 to the monitored endpoint |
| E06 | Wazuh Successful Authentication Classification | Shows Wazuh rule 5715 and MITRE ATT&CK T1078/T1021 mapping for the successful SSH authentication event |
| E07 | Privileged Command Execution | Shows sudo execution of `/usr/bin/whoami` as root following remote authentication |
| E08 | Wazuh Sudo/Root Detection | Shows Wazuh detection of successful sudo-to-root activity using rule 5402 |
| E09 | MITRE ATT&CK Event Overview | Shows MITRE ATT&CK-mapped security events associated with the monitored endpoint |
