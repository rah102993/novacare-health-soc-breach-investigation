# NovaCare Health SOC Breach Investigation

## Project Overview

This project documents a simulated Security Operations Center (SOC) investigation conducted within a controlled virtual lab environment.

The investigation focused on suspicious SSH authentication activity targeting a Linux endpoint. Using Wazuh SIEM and native Linux logs, I investigated repeated authentication failures, identified the source of the activity, confirmed a subsequent successful SSH login, and examined privileged activity performed after access was obtained.

The objective was not simply to generate security alerts, but to follow an evidence-driven SOC investigation process: identify suspicious activity, validate alerts against endpoint logs, correlate events, map observed behavior to the MITRE ATT&CK framework, and recommend appropriate security controls.

> **Important:** All activity documented in this repository was performed in an isolated lab environment for cybersecurity training and defensive security analysis.

---

## Lab Environment

| Component | Role |
|---|---|
| Kali Linux | Simulated external threat source |
| Ubuntu Linux | Monitored endpoint |
| Wazuh | SIEM, endpoint monitoring and alert analysis |
| pfSense | Network gateway/firewall |
| VirtualBox | Virtualized lab environment |
| MITRE ATT&CK | Technique and tactic mapping |

### Key Lab Addresses

- **Kali Linux:** `192.168.10.100`
- **Ubuntu Endpoint:** `192.168.10.121`
- **Wazuh Agent:** `MediCore-Linux01`
- **Network:** `192.168.10.0/24`

The Ubuntu endpoint received its address through DHCP during the lab. Earlier addresses changed following VM/network restarts, so the investigation uses `192.168.10.121` as the endpoint address for the documented attack period.

---

## Investigation Objectives

The investigation sought to determine:

1. Whether the SSH authentication activity represented suspicious behavior.
2. Which system originated the authentication attempts.
3. Whether authentication eventually succeeded.
4. What activity occurred after successful authentication.
5. Whether privileged commands were executed.
6. How Wazuh classified the observed activity.
7. Whether the observed events could be correlated into a defensible incident timeline.
8. What controls could reduce the likelihood or impact of similar activity.

---

## Investigation Summary

Analysis identified repeated failed SSH authentication attempts originating from:

`192.168.10.100`

and targeting the monitored Ubuntu endpoint:

`192.168.10.121`

Wazuh generated authentication-related alerts and subsequently identified the repeated failures as brute-force behavior.

A later SSH event showed a successful password authentication for the `ubuntu` account from the same source IP.

Further investigation identified privileged activity in which the authenticated `ubuntu` user executed:

`sudo whoami`

The command returned:

`root`

Wazuh independently recorded the sudo activity and mapped it to privilege-escalation-related behavior.

The correlation of these events produced the following sequence:

**Repeated authentication failures → Successful SSH authentication → Privileged sudo activity**

---

## Key Findings

### 1. Repeated SSH Authentication Failures

Linux authentication logs showed repeated attempts involving an invalid user from `192.168.10.100`.

Examples included:

- `Invalid user wronguser`
- `Failed password for invalid user`
- `PAM authentication failure`
- Multiple authentication failures from the same source

Wazuh correlated the activity and generated a higher-severity brute-force alert.

### 2. Brute-Force Detection

Wazuh generated:

- **Rule ID:** `2502`
- **Rule Level:** `10`
- **MITRE ATT&CK:** `T1110`
- **Tactic:** Credential Access
- **Technique:** Brute Force

This demonstrated that individual authentication failures could be correlated into a more significant security event.

### 3. Successful SSH Authentication

A later event recorded:

`Accepted password for ubuntu from 192.168.10.100`

This established that the same source IP associated with the earlier authentication activity subsequently authenticated successfully to the Ubuntu endpoint.

### 4. Privileged Activity

Following successful authentication, the `ubuntu` account executed:

`sudo whoami`

The result was:

`root`

Wazuh generated an alert for the sudo activity:

- **Rule ID:** `5402`
- **Description:** Successful sudo to ROOT executed
- **MITRE ATT&CK:** `T1548.003`
- **Technique:** Sudo and Sudo Caching
- **Tactics:** Privilege Escalation / Defense Evasion

This provided evidence of privileged command execution following remote authentication.

---

## MITRE ATT&CK Mapping

| Observed Activity | MITRE Technique | Tactic |
|---|---|---|
| Repeated password attempts | T1110 – Brute Force | Credential Access |
| Successful use of account | T1078 – Valid Accounts | Initial Access / Persistence / Privilege Escalation / Defense Evasion |
| Remote SSH access | T1021 – Remote Services | Lateral Movement |
| Sudo execution | T1548.003 – Sudo and Sudo Caching | Privilege Escalation / Defense Evasion |

MITRE mappings above reflect the Wazuh classifications observed during the investigation. A MITRE classification alone was not treated as proof that every associated tactic occurred; the underlying event evidence was reviewed before drawing conclusions.

---

## Incident Timeline

| Event | Source | Destination | Evidence |
|---|---|---|---|
| Repeated SSH authentication failures | `192.168.10.100` | `192.168.10.121` | Ubuntu SSH logs / Wazuh |
| Brute-force alert generated | Wazuh | `MediCore-Linux01` | Rule 2502 / T1110 |
| Successful SSH authentication | `192.168.10.100` | `192.168.10.121` | `Accepted password for ubuntu` |
| Privileged command executed | Authenticated Ubuntu session | Ubuntu endpoint | `sudo whoami` |
| Privilege-related Wazuh alert | Wazuh | `MediCore-Linux01` | Rule 5402 / T1548.003 |

---

## HTTPS Traffic Investigation

During the investigation, an established HTTPS connection was observed from the Ubuntu endpoint:

`192.168.10.121:50944 → 146.75.17.91:443`

Process inspection associated the connection with Firefox.

The presence of an outbound HTTPS connection alone was **not considered sufficient evidence of data exfiltration**. No conclusion of exfiltration was made without supporting evidence such as abnormal transfer volume, suspicious destination context, packet evidence, or corresponding endpoint activity.

This distinction was important to avoid treating ordinary encrypted web traffic as malicious solely because it used TCP port 443.

---

## Security Recommendations

- Restrict SSH access to authorized administrative systems and networks.
- Prefer SSH key-based authentication over password authentication where operationally appropriate.
- Disable unnecessary accounts and enforce strong account lifecycle management.
- Implement controls against repeated authentication failures, such as rate limiting or automated blocking.
- Apply least privilege to administrative and sudo permissions.
- Monitor successful authentication occurring after repeated failures.
- Correlate authentication, endpoint and privilege events rather than investigating each alert in isolation.
- Maintain centralized security logging and appropriate log-retention policies.
- Regularly review privileged activity.
- Keep operating systems and security tooling patched and updated.

---

## Skills Demonstrated

- SOC alert triage
- SIEM investigation
- Wazuh endpoint monitoring
- Linux authentication log analysis
- SSH security analysis
- Event correlation
- Incident timeline reconstruction
- MITRE ATT&CK mapping
- Privilege escalation analysis
- Network connection investigation
- Evidence-based incident reporting
- Security control recommendations

---

## Tools Used

`Wazuh` `Kali Linux` `Ubuntu Linux` `pfSense` `VirtualBox` `SSH` `Linux Journald` `MITRE ATT&CK`

---

## Evidence

Screenshots supporting the investigation are maintained in the `screenshots` directory.

The evidence includes:

- Wazuh endpoint monitoring
- MITRE ATT&CK dashboard
- SSH authentication failure alerts
- Wazuh brute-force detection
- Successful SSH authentication
- Ubuntu authentication logs
- Successful sudo/root activity
- Wazuh privilege escalation detection
- HTTPS connection investigation

---

## Conclusion

This investigation demonstrated how isolated authentication events can become significantly more meaningful when correlated.

Repeated SSH authentication failures from `192.168.10.100` were followed by successful authentication to the monitored Ubuntu endpoint and subsequent privileged activity. Wazuh provided centralized visibility into these events and MITRE ATT&CK mappings, while native Linux logs provided additional evidence for validating the sequence of activity.

The project reinforced a fundamental SOC principle: **an alert is the beginning of an investigation, not the conclusion.**

Security events should be validated, correlated and interpreted in context before determining what occurred and what response is appropriate.
