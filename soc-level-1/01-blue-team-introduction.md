# 01 - Blue Team Introduction
**Path:** TryHackMe SOC Level 1
**Date Completed:** [2026-05-18]
**Status:** Complete

---

## What This Section Covers:
This section covers the different parts of a Security Operations Center (SOC) team and how they all function together. The SOC team is split into four (4): Senior Analyst, SOC Engineer, Incident Responder, SOC Manager. In addition we are introduced to important security tools like SIEM for alert triage, and IP hunters for searching and documenting malicious IP addresses.
---

## Key Concepts

### Security Information and Management (SIEM) 
This tool developed and configured by the SOC Engineers allows SOC analysts to monitor alerts for possible incidents, rank these alerts by severity and then respond to incidents that arise in a timely manner. 

### Intrusion Detection System (IDS)
This is a tool that actively scans a network for threats or anomalies. Once an anomaly has been detected it creates a log and sends these event logs to the SIEM.

### SOC Engineer
The role of the SOC Engineer is to create and configure detection software like SIEMs, IDS, and firewalls. These tools are then utilized by SOC Analysts for monitoring and responding to security incidents when they arise.


### Senior Analyst
Senior Analysts work with Junior analysts and are next in the escalation chain for detected incidents. Their main roles include the monitoring of anomaly detection tools and searching for a change in status quo, prompting a response.

### Incident Responders
The Incident Responders act as the firefighters in the team and diagnose the situation, minimize damage and eventually recover affected systems.

### SOC Manager
The SOC Manager acts as a contact point to Top management, notifying them of issues and how they were handled. They might also be involved in mentoring team members or developing security policies.

---

## Tools Used
| Tool | Purpose | Notable Commands/Usage |
|------|---------|----------------------|
| SIEM | Log analysis and alert triage | Reviewing Severity and alert message for potential escalation |
| IP Hunter (AbuseIPDB) | Reputation check for IP addresses | Discover malicious IP addresses and add to IPDB |

---

## Real-World Application
An analyst notices a SIEM alert showing repeated failed login attempts from an external IP at 3am, and then a successful login attempt later. They check AbuseIPDB and confirm the IP has been involved in previous cyber attacks. The findings are documented and then escalated to a senior Analyst with a recommended action. The Senior Analyst reviews the findings and confirms malicious activity, then coordinating with the Incident Response team to block the IP at the firewall level. The breach is then investigated to find out whether the successful login resulted in an lateral movement.
---

## Connections to My Work
There is always a trickle down effect in the event of an incident in regards to the flow of information. This ties in with the Security Control principles in the CompTIA Security+ material where each control type is paired with someone from the team. For example in the event of an SIEM alert, the SOC engineers and Senior analysts work together for detection. After detection the subsequent control type/response is correction and this is where incident responders step in to mitigate or eradicate a breach. In the event that a breach cannot be eradicated, compensating control types must be implemented by incident responders to minmize damage before a complete patch can be developed.

---

## What I Would Do Differently in a Real SOC
In a production SOC environment, the workflow described above would be significantly more structed. Alerts would be triaged against a formal runbook rather than relying solely on analyst jdugement. SIEM would also be integrated with a SOAR platform to automate the process of alert encrichment and threat intelligence and possibly automatically block malicious IP. This step is simplified in the TryHackMe lab by having the analyst perform each step manually.

## Key Takeaway
The role of SOC analysts is to monitor networks for anomalies or suspicious activity. When an alert is triggered in the SIEM, their role then switches to escalating to a specialized incident response team for investigation and mitigation.
