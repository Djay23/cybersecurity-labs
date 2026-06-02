# 01 - Blue Team Introduction
**Path:** TryHackMe SOC Level 1
**Date Completed:** 2026-05-28
**Status:** Complete

---

## What This Section Covers:
This section covers the different parts of a Security Operations Center (SOC) team and how they all function together. The security department also houses the Chief Information Security Officer (CISO) who takes load off the CEO and focuses on the security aspect of the business. We are introduced to important security tools like SIEM for alert triage, and IP hunters for searching and documenting malicious IP addresses. In addition we are briefly introduced to the GRC and Red teams who specialize in policy compliance and offensive security respectively, as well as the Cyber Incident Response Team (CIRT) who are called in when a breach exceeds the SOC's capacity.
We are also introduced to the different points of attacks mal-actors tend to focus on: Systems and Humans.

---

## Key Concepts

### Security Information and Event Management (SIEM)
This tool developed and configured by the SOC Engineers allows SOC analysts to monitor alerts for possible incidents, rank these alerts by severity and then respond to incidents that arise in a timely manner.

### Intrusion Detection System (IDS)
This is a tool that actively scans a network for threats or anomalies. Once an anomaly has been detected it creates a log and sends these event logs to the SIEM.

### SOC L1 Analyst
These are the first line of defense and are the first to be notified about possible breaches. The role of the L1 analyst is to monitor and triage the alerts and then escalate to the L2 Analyst.

### SOC L2 Analyst
The role of the L2 analyst is to analyze the escalated alerts and attempt to apply a fix or escalate to the CIRT if a fix cannot be applied.

### SOC Engineer
The role of the SOC Engineer is to create and configure detection software like SIEMs, IDS, and firewalls. These tools are then utilized by SOC Analysts for monitoring and responding to security incidents when they arise.

### SOC Manager
The SOC Manager acts as a contact point to top management, notifying them of issues and how they were handled. They might also be involved in mentoring team members or developing security policies.

### Managed Security Services Provider (MSSP)
These are companies that outsource security to smaller companies or companies without a dedicated SOC team. The employees in MSSPs are in charge of the security for a variety of companies and as a result are exposed to more attacks and tools on a day to day basis.

### Cyber Incident Response Team (CIRT)
In the event that a breach occurs and cannot be managed by the SOC team, the heavy hitters (CIRT) are called in. This team contains: CSIRT manager, Forensics lead, Threat intel expert, Threat hunting expert, and a Malware analyst.

### Social Engineering
Social engineering is the idea of targeting humans through phishing, deepfakes, USB drops or malware downloads. The idea is that instead of trying to crack a potentially impenetrable system and risk detection, human beings are a more susceptible endpoint and can even provide complete access once compromised. 

### Supply Chain attacks
Supply chain attacks refer to the compromise of software or libraries utilized by various users. If an app is breached, a malicious update can be pushed and then downloaded by all users of the software leading to widespread breaches. When a supply chain compromise is detected, the SOC is responsible for identifying which systems downloaded the malicious update and isolating them before lateral movement occurs. 

### Vulnerabilities
Vulnerabilities are security flaws that have potential to be exploited. These range from weak passwords to misconfigured systems. Once a vulnerability is discovered it is assigned a Common Vulnerabilities and Exposure (CVE) number and then a patch is prepared. Misconfigurations on the other hand are unrelated to software but instead are born out of flaws in the system during setup by IT usually. When a CVE is flagged, the SOC works with the engineering team to prioritize patching based on severity and exposure. 

---

## Tools Used
| Tool | Purpose | Notable Commands/Usage |
|------|---------|----------------------|
| SIEM | Log analysis and alert triage | Reviewing Severity and alert message for potential escalation |
| IP Hunter (AbuseIPDB) | Reputation check for IP addresses | Discover malicious IP addresses and add to IPDB |

---

## Real-World Application
Attackers send a phishing email to a teller at the bank who then unknowingly provides her login credentials to a malicious actor. This results in a breach, leading to the exposure of customer data. The first people to detect this breach are the SOC analysts and more specifically the L1 junior analysts. After analyzing and triaging the alerts they see that the same malicious IP has appeared in other breaches as well. Because the solution falls outside of their capabilities the problem is documented alongside a proposed fix and escalated to L2. The Senior SOC analyst attempts to rectify the problem but again is unable to and calls in the heavy hitting CIRT. The malware analyst is deployed to understand what type of malware was used in the breach and contain it. The forensics lead ensures the malware is no longer in the system and exposes other actors still lurking. The threat intel expert and threat hunter go after the threat after understanding how it operates and what it specifically targets. After the containment of the malware the GRC steps in to produce policies and standards to prevent future breaches.

---

## Connections to My Work
There is always a trickle down effect in the event of an incident in regards to the flow of information. This ties in with the Security Control principles in the CompTIA Security+ material where each control type is paired with someone from the team. Preventative and deterrent measures are implemented by the SOC engineers. If these are somehow breached, next in line are the Junior SOC analysts who perform detective duties to triage the alerts and then escalate to Tier 2 analysts for potential corrective measures. In the event that the breach was severe, this is escalated to the CIRT. Eventually after the situation has been resolved, the GRC team steps in to create policies and standards to minimize another occurrence, in addition to anti-phishing campaigns. These are directive control measures applied at the organizational level.

---

## What I Would Do Differently in a Real SOC
In a production SOC environment, the workflow described above would be significantly more structured. Alerts would be triaged against a formal runbook rather than relying solely on analyst judgment. SIEM would also be integrated with a SOAR platform to automate the process of alert enrichment and threat intelligence and possibly automatically block malicious IPs. This step is simplified in the TryHackMe lab by having the analyst perform each step manually.

---

## Key Takeaway
The role of SOC analysts is to monitor networks for anomalies or suspicious activity. When an alert is triggered in the SIEM, the escalation structure follows from L1 analysts triaging and escalating, to L2 analysts investigating and attempting remediation, and finally to the CIRT when the breach exceeds the SOC's capabilities. Above it all, the CISO owns the security program and the GRC team ensures incidents translate into policy. One way attackers can access a system is through social engineering, where people are treated as attack endpoints; once compromised, they grant attackers potentially unrestricted access in a network. 