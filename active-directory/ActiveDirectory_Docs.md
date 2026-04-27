# TryHackMe - Active Directory Basics

**Platform:** TryHackMe  
**Room:** [Active Directory Basics](https://tryhackme.com/room/activedirectorybasics)  
**Category:** Fundamentals / Identity & Access Management  
**Date Completed:** April 2026  

---

## Overview

This writeup documents my walkthrough of the TryHackMe **Active Directory Basics** room. The lab covers core concepts behind enterprise identity management, including how Windows domains centralize authentication, how objects are structured within Active Directory, and how to manage users, groups, computers, and Group Policy in practice.

**Key Terms & Abbreviations:**

| Term | Definition |
|---|---|
| **AD** | Active Directory - Microsoft's directory service for managing users, computers, and policies |
| **AD DS** | Active Directory Domain Services - maps every domain object to its address, roles, and privileges |
| **DC** | Domain Controller - the server that authenticates users and serves AD policies |
| **OU** | Organizational Unit - a container that groups objects with similar policy requirements |
| **GPO** | Group Policy Object - a collection of settings applied to users or computers within an OU |
| **SYSVOL** | System Volume - a shared directory on each DC used to distribute GPOs across the domain |
| **ACL** | Access Control List - defines what permissions a user or group has over a resource |
| **DNS** | Domain Name System - resolves domain names to IP addresses within the AD environment |
| **Forest** | A collection of one or more domain trees that share a common schema |
| **Trust** | A relationship between domains that allows users in one domain to access resources in another |

**Topics covered:**
- What Active Directory is and why it exists
- Active Directory Domain Services (AD DS)
- Objects: Users, Machines, and Security Groups
- Organizational Units (OUs)
- Managing users, groups, and computers
- Delegation of control
- Group Policy Objects (GPOs)
- GPO Distribution via SYSVOL
- Creating and linking GPOs
- Forests and Domain Trusts

---

## What is Active Directory?

In a small network, each device can be configured individually - creating users manually, setting permissions, and troubleshooting in person. This approach breaks down at scale. **Active Directory (AD)** solves this by centralizing identity and policy management across an entire organization.

A useful analogy: think of a **Windows Domain** as a library. The **Active Directory** is the collection of books, and the **Domain Controller (DC)** is the librarian - authenticating users and serving them the correct policies and permissions when they log in.

**Key benefits:**
- Centralized identity management across all devices
- Standardized enforcement of security policies
- Scalable administration without touching each machine individually

---

## Active Directory Domain Services (AD DS)

**AD DS** acts as the directory that maps every object in the domain to its address, roles, and privileges. When an object (user, machine, etc.) logs into the domain, the flow is:

1. The object authenticates with the Domain Controller
2. AD DS locates the object's record
3. The record defines what roles and privileges that object has been assigned
4. The Domain Controller serves the appropriate policies and access rights

---

## Objects in Active Directory

AD DS manages three main types of **security principals** - objects that can be assigned privileges over resources:

### Users
Users represent people and services within the domain. They can be granted access to resources like files, printers, or applications. A useful analogy: users are like hotel guests with keycards - the keycard grants them access to specific areas (breakfast room, meeting rooms) but not others.

### Machines
Every computer that joins the domain gets a corresponding **machine object**, identifiable by the naming convention `computer_name$`. Like users, machines are security principals and can be assigned permissions.

### Security Groups
Security groups increase manageability by allowing multiple users to inherit the same set of privileges. Key properties:
- Users added to a group automatically inherit all group permissions
- Groups can contain other groups (nesting)
- Groups are also security principals

---

## Organizational Units (OUs)

When the Active Directory Users and Computers console is opened, objects are displayed in a hierarchical structure organized by **Organizational Units (OUs)**.

Key rules about OUs:
- An object can only belong to **one OU at a time**
- OUs group users and machines with similar policy requirements
- Think of an OU as a room in a building - users are physically in that room and cannot be in two rooms simultaneously

Within an OU, users can be assigned to multiple **security groups**, which act like keys granting access to specific locked doors within that room.

> **Summary:** OU = where you physically are in the domain. Security Group = what you're allowed to do.

---

## Simulating User Management in AD

To practice managing users, the lab provides an organisational chart and tasks us with configuring Active Directory to match it.

> **Note:** By default, OUs are protected against accidental deletion. To remove or restructure an OU, **Advanced Features** must be enabled under the View menu, then the deletion protection can be unchecked in the OU's Properties.

After enabling Advanced Features and disabling deletion protection, the OU structure was successfully updated to match the org chart.

---

## Delegation of Control

Rather than requiring Domain Admins to handle every administrative task, AD allows **delegation of control** - granting specific users targeted privileges over specific OUs.

In this lab, **Phillip** from the IT department was granted the ability to reset passwords in the Sales and Marketing OUs, without granting him full Domain Admin rights.

### Testing Delegation via PowerShell

To verify the delegation worked, Phillip's account was used to reset Sophie's password using PowerShell:

```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

The verbose output confirms the operation was performed on `CN=Sophie,OU=Sales,OU=THM,DC=thm,DC=local`. On next login, Sophie was able to authenticate with the updated credentials.

---

## Managing Computers in AD

By default, all computers that join the domain are placed in a generic **Computers** container. This is problematic because servers and workstations require different policies.

Best practice is to segregate devices into three categories:

| Category | Description |
|---|---|
| **Workstations** | Standard user machines for daily work and browsing. Should never host privileged accounts. |
| **Servers** | Provide services to other domain devices. Require stricter policies than workstations. |
| **Domain Controllers** | The most sensitive machines in the domain. Authenticate users and store AD data. |

Creating separate OUs for each category allows Group Policy to apply the correct security baseline to each device type.

---

## Group Policy Management

**Group Policy Objects (GPOs)** are the practical mechanism for applying settings, permissions, and configurations to OUs. This is configured using the **Group Policy Management** tool, which displays the same OU hierarchy defined in ADUC.

Several GPOs exist by default:
- **Default Domain Controllers Policy** - linked only to the Domain Controllers OU
- **Default Domain Policy** - linked to the root domain, inherited by all child OUs
- **RDP Policy** - linked to the root domain

GPOs flow **down** from parent to child by default. This means any OU under `thm.local` will automatically inherit policies linked at the domain level, unless inheritance is explicitly blocked.

### Key Concepts

**Scope** refers to where a GPO is linked in the AD. From the scope view, **Security Filtering** can also be applied to target specific users or devices within an OU rather than everyone.

**The Settings tab** shows the actual configurations inside a GPO. Changes made to a GPO in a domain are universal - they apply to all computers or users that fall within the GPO's scope.

### GPO Inheritance Order

Policies are applied in the following order, with each level able to add or override the previous:

```
1. Local      → policies set directly on the machine
2. Site       → based on physical network location
3. Domain     → applies to all objects (e.g. Default Domain Policy)
4. OU         → applies only to objects inside that specific OU
```

### Blocking Inheritance & Enforcement

| Scenario | Result |
|---|---|
| GPO linked to domain | Affects all OUs |
| GPO linked to OU | Affects only that OU |
| Block Inheritance on OU | Stops parent GPOs (unless Enforced) |
| GPO set to Enforced | Cannot be blocked by child OUs |

---

## GPO Distribution

For a computer or user to receive updated GPO settings, the GPO is distributed via **SYSVOL** - a shared directory that exists on every Domain Controller. All users and computers in the domain can access SYSVOL over the network to periodically sync their GPO settings.

SYSVOL is stored locally on each DC at:

```
C:\Windows\SYSVOL\sysvol\
```

### How Distribution Works

```
Admin creates/edits a GPO
        ↓
GPO saved to SYSVOL on the DC
        ↓
SYSVOL replicates to all other DCs
        ↓
Computers and users poll SYSVOL on their refresh interval
        ↓
Version number checked → if changed, pull and apply the new GPO
```

### Polling & Refresh Intervals

Computers and users do not receive push notifications when a GPO changes - they pull on a scheduled timer:

| Trigger | Who | When |
|---|---|---|
| Startup | Computer | Every boot |
| Login | User | Every login |
| Background refresh | Both | Every ~90 minutes (+/- 30 min random offset) |
| `gpupdate /force` | Both | Manually on demand |

The random offset prevents all machines from hitting SYSVOL simultaneously, distributing the load naturally across the network.

### Version Number Check

Windows does not re-download every GPO on every check. It compares the **version number** stored in AD against what was last applied:

```
Stored version: 5
SYSVOL version: 5  →  No update needed
SYSVOL version: 6  →  Pull and apply the updated GPO
```

This means only computers and users whose linked GPO has actually changed will pull an update - machines in unrelated OUs are unaffected.

### Forcing an Immediate Update

```cmd
gpupdate /force
```

This bypasses the polling timer and forces the machine to immediately pull and apply the latest GPOs from SYSVOL.

---

## Creating GPOs

### Task 1 - Block Non-IT Users from Accessing the Control Panel

A GPO called **Restrict Control Panel Access** was created and configured under:

```
User Configuration → Policies → Administrative Templates → Control Panel
→ Prohibit access to Control Panel and PC settings → Enabled
```

Because this setting lives under **User Configuration**, it only affects users - not computers. The GPO was then dragged and dropped onto the relevant OUs (Sales, Marketing, etc.) to link it, excluding the IT OU.

### Task 2 - Auto Lock Screen After 5 Minutes of Inactivity

A GPO called **Auto Lock Screen** was created and configured under:

```
Computer Configuration → Policies → Windows Settings → Security Settings
→ Local Policies → Security Options
→ Interactive logon: Machine inactivity limit → 300 seconds
```

Because this applies to all computers in the domain (Workstations, Servers, and Domain Controllers), the GPO was linked to the **root domain** (`thm.local`) rather than individual OUs. Since Workstations, Servers, and Domain Controllers are all child OUs of the root, they inherit this policy automatically.

> **Note:** OUs like Sales or Marketing that contain only users will simply ignore the Computer Configuration portion of a GPO - it has no effect on user-only containers.

### The GPO Workflow

```
1. Create the GPO in Group Policy Management
        ↓
2. Right-click → Edit (opens Group Policy Management Editor for that GPO)
        ↓
3. Configure settings inside the editor (auto-saved into the GPO)
        ↓
4. Close the editor
        ↓
5. Drag the GPO onto the target OU to link it
```

> Creating and editing a GPO is only half the job - a GPO must be **linked** to an OU before it has any effect. A GPO sitting in "Group Policy Objects" without a link does nothing.

---

## Domain Trees and Enterprise Administration

As organizations grow, a single domain may become insufficient. **Domain Trees** allow for the creation of child domains under a root domain (e.g., `uk.thm.local` under `thm.local`), each managed with more specialized control.

Within a tree structure:
- **Domain Admins** can only operate within their own domain
- **Enterprise Admins** hold administrative privileges across all domains in the enterprise
- Each domain retains its own Domain Admin alongside the Enterprise Admin

This separation reduces the risk of human error and supports large, geographically distributed organizations.

---

## Forests and Trust Relationships

> *Note: Diagrams illustrating forest structure and trust relationships in this section were adopted from the [TryHackMe](https://tryhackme.com) website.*

### Forests

A **Forest** is formed when multiple domain trees are joined together. This typically occurs during company mergers or acquisitions where two organizations maintain separate domain trees with different namespaces. Though they share a common AD schema, each tree retains its own structure and identity within the forest.

### Trust Relationships

**Trust relationships** allow users in one domain to access resources in another. Trusts come in two forms:

| Trust Type | Description |
|---|---|
| **One-way trust** | Domain A trusts Domain B - users in B can access resources in A, but not vice versa |
| **Two-way trust** | Both domains trust each other - users in either domain can access resources in the other |

For example, if `DomainAAA` trusts `DomainBBB`, a user authenticated in BBB can be authorized to access files or services hosted in AAA. Trust relationships are what make cross-domain resource sharing possible in large enterprise environments.

---

## Key Takeaways

- **Active Directory** solves the scalability problem of managing users and devices across large networks by centralizing identity and policy management
- **AD DS** maps every domain object to its address, roles, and privileges
- **OUs** organize objects structurally; **Security Groups** define what those objects can access
- **Delegation of Control** reduces reliance on Domain Admins by granting targeted privileges to specific users
- **PowerShell** (`Set-ADAccountPassword`, `Disable-ADAccount`) is the primary tool for programmatic identity management in AD environments
- Separating computers into **Workstations**, **Servers**, and **Domain Controllers** OUs enables proper Group Policy application per device type
- **GPOs must be linked to an OU** to have any effect - creating a GPO alone does nothing
- **SYSVOL** is the distribution mechanism for GPOs; clients poll it on a ~90 minute interval and only apply changes when the version number increments
- **Domain Trees** and **Forests** extend AD to support large, multi-domain enterprise environments
- **Trust relationships** enable cross-domain resource access, forming the backbone of inter-domain collaboration

---

## Tools & Technologies Used

- Windows Server (Domain Controller)
- Active Directory Users and Computers (ADUC)
- Active Directory Domain Services (AD DS)
- Group Policy Management
- Group Policy Management Editor
- PowerShell - `Set-ADAccountPassword`, `gpupdate /force`
- Delegation of Control Wizard
- SYSVOL

---

*Part of an ongoing series of self-directed cybersecurity labs. All work performed in an isolated virtual environment.*