# 02 - SOC-Team-Internals
**Path:** TryHackMe SOC Level 1
**Date Completed:** 2026/05/19
**Status:** Complete

---

## What This Section Covers:
This section introduces us to the blue team and its role within the security department. The security department also houses the Chief Information Security Officer (CISO) who takes load off the CEO and focuses on the security aspect of the business. In addition we are briefly introduced to the GRC and Red teams who specialize in policy compliance and offensive security respectively.
---

## Key Concepts

### Managed Security Services Provider (MSSP)
These are companies that outsource security to smaller companies or companies without a dedicated SOC team. The role of employees in MSSPs are in charge of the security for a variety of companies and as a result are exposed to more attacks and tools on a day to day basis.

### Cyber Incident Response Team (CIRT)
In the event that a breach occurs and cannot be managed by the SOC team, the heavy hitters (CIRT) are called in. This team contains: CSIRT manager, Forensics lead, Threat intel expert, Threat hunting expert, and a Malware analyst.

### SOC L1 Analyst
These are the first line of defense and are the first to be notified about possible breaches. The role of the L1 analyst is to monitor the alerts and then escalate to the L2 Analyst

### SOC L2 Analyst
The role of the L2 analyst is to analyze the escalated alerts and attempt to apply a fix or escalate to the CIRT if a fix cannot be applied

### SOC Engineer
The SOC engineer configures and implements the alert logging systems in the SIEM and IDS
---

## Tools Used
N/A; Conceptual Section

---

## Real-World Application
Attackers infiltrate a bank and a breach happens, leading to the exposure of customer data. The first people to detect this breach are the SOC analysts and more specifically the L1 junior analysts. After analyzing and triaging the alerts they see that the same malicious IP has appeared in other breaches as well. Because the solution falls outside of their capabilities the problem is documented alongside a proposed fix and escalated to L2. The Senior SOC analyst attempts to rectify the problem but again is unable to and calls in the heavy hitting CIRT. The malware analyst is deployed to understand what type of malware was used in the breach and contain it. The forensics lead ensures the malware is still not in the system and exposes other actors still lurking. The threat intel expert and threat hunter go after the threat after understanding how it operates and what it specifically targets. After the containment of the malware the GRC steps in to produce policies and standards to prevent future breaches. 
---

## Connections to My Work
Like the example is section 01-blue team introduction, there is an incident response chain that must be upheld when a breach is detected. These fall within the control types: preventative, deterrent, detective, corrective, compensating, and directive. Preventative and deterrent measures are implemented by the SOC engineers. If these are somehow breached, next in line are the Junior SOC analysts who perform detective duties to triage the alerts and then escalate to Tier 2 analysts for potential corrective measures. In the event that the breach was severe, this is escalated to the CIRT. Eventually after the situation has been resolved, the GRC team steps in to create policies and standards to minimize another occurrence. 
---

## Key Takeaway
The Security hierarchy in a company is split up from CISO who create and manage the different security teams, to the Red and Blue teams where the red team performs offensive duties and the blue team performs defensive duties. The escalation structure follows the route of SOC manager to SOC Analysts/Engineers. If the SOC team is unable to rectify and find a solution, the firefighters or the CIRT is called in to deploy a solution and analyze and eliminate the malware. 
