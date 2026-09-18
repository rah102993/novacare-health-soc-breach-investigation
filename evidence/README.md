# Investigation Evidence

This directory contains selected evidence collected during the NovaCare Health SOC breach investigation.

The screenshots are organized to follow the investigation from environment validation through detection, authentication analysis, privilege escalation, and network-connection review.

## Evidence Index

| ID | Evidence | Purpose |
|---|---|---|
| E01 | Wazuh Endpoint Monitoring | Confirms the monitored Ubuntu endpoint was actively reporting to Wazuh |
| E02 | Kali-to-Ubuntu Connectivity | Confirms network connectivity between the simulated threat source and monitored endpoint |
| E03 | SSH Authentication Failures | Shows repeated failed SSH authentication attempts |
| E04 | Wazuh Brute-Force Detection | Shows Wazuh correlation and MITRE T1110 classification |
| E05 | Successful SSH Authentication | Shows successful authentication from the source system |
| E06 | Linux SSH Log Correlation | Validates the authentication sequence using native Linux logs |
| E07 | Privileged Command Execution | Shows sudo execution following remote authentication |
| E08 | Wazuh Privilege Escalation Alert | Shows Wazuh detection and MITRE T1548.003 mapping |
| E09 | HTTPS Connection Review | Documents investigation of outbound HTTPS traffic |
