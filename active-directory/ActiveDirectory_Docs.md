# TryHackMe - Active Directory Basics

**Platform:** TryHackMe  
**Room:** [Active Directory Basics](https://tryhackme.com/room/activedirectorybasics)  
**Category:** Fundamentals / Identity & Access Management  
**Date Completed:** April 2026  

---

## Overview

This writeup documents my walkthrough of the TryHackMe **Active Directory Basics** room. The lab covers core concepts behind enterprise identity management, including how Windows domains centralize authentication, how objects are structured within Active Directory, and how to manage users, groups, computers, and Group Policy in practice.

**Topics covered:**
- What Active Directory is and why it exists
- Active Directory Domain Services (AD DS)
- Objects: Users, Machines, and Security Groups
- Organizational Units (OUs)
- Managing users, groups, and computers
- Delegation of control
- Group Policy Objects (GPOs)
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

![Active Directory Users and Computers console showing OU hierarchy](img_01_page2.png)

Key rules about OUs:
- An object can only belong to **one OU at a time**
- OUs group users and machines with similar policy requirements
- Think of an OU as a room in a building - users are physically in that room and cannot be in two rooms simultaneously

Within an OU, users can be assigned to multiple **security groups**, which act like keys granting access to specific locked doors within that room.

> **Summary:** OU = where you physically are in the domain. Security Group = what you're allowed to do.

---

## Simulating User Management in AD

To practice managing users, the lab provides an organisational chart and tasks us with configuring Active Directory to match it.

The target org chart:

![Organisational chart showing Management, Sales, Marketing, and IT departments](img_03_page3.png)

After restructuring the OUs to match the chart, the AD hierarchy reflects the correct department structure:

![Updated AD structure matching the organisational chart](img_02_page3.png)

> **Note:** By default, OUs are protected against accidental deletion. To remove or restructure an OU, **Advanced Features** must be enabled under the View menu, then the deletion protection can be unchecked in the OU's Properties.

Attempting to delete a protected OU produces this error:

![Error message when attempting to delete a protected OU](img_04_page4.png)

After enabling Advanced Features and disabling deletion protection, the OU structure was successfully updated to match the org chart.

---

## Delegation of Control

Rather than requiring Domain Admins to handle every administrative task, AD allows **delegation of control** - granting specific users targeted privileges over specific OUs.

In this lab, **Phillip** from the IT department was granted the ability to reset passwords in the Sales and Marketing OUs, without granting him full Domain Admin rights.

![Delegation of Control Wizard - selecting Phillip as the delegated user](img_06_page4.png)

![Completed delegation - Phillip now has password reset rights over Sales and Marketing OUs](img_07_page4.png)

### Testing Delegation via PowerShell

To verify the delegation worked, Phillip's account was used to reset Sophie's password using PowerShell:

```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

![PowerShell output confirming successful password reset for Sophie](img_08_page5.png)

The verbose output confirms the operation was performed on `CN=Sophie,OU=Sales,OU=THM,DC=thm,DC=local`. On next login, Sophie was able to authenticate with the updated credentials.

---

## Managing Computers in AD

By default, all computers that join the domain are placed in a generic **Computers** container. This is problematic because servers and workstations require different policies.

![Computers container showing all domain machines grouped together by default](img_09_page5.png)

Best practice is to segregate devices into three categories:

| Category | Description |
|---|---|
| **Workstations** | Standard user machines for daily work and browsing. Should never host privileged accounts. |
| **Servers** | Provide services to other domain devices. Require stricter policies than workstations. |
| **Domain Controllers** | The most sensitive machines in the domain. Authenticate users and store AD data. |

Creating separate OUs for each category allows Group Policy to apply the correct security baseline to each device type.

---

## Key Takeaways

- **Active Directory** solves the scalability problem of managing users and devices across large networks by centralizing identity and policy management
- **AD DS** maps every domain object to its address, roles, and privileges
- **OUs** organize objects structurally; **Security Groups** define what those objects can access
- **Delegation of Control** reduces reliance on Domain Admins by granting targeted privileges to specific users
- **PowerShell** (`Set-ADAccountPassword`, `Disable-ADAccount`) is the primary tool for programmatic identity management in AD environments
- Separating computers into **Workstations**, **Servers**, and **Domain Controllers** OUs enables proper Group Policy application per device type

---

## Tools & Technologies Used

- Windows Server (Domain Controller)
- Active Directory Users and Computers (ADUC)
- Active Directory Domain Services (AD DS)
- PowerShell - `Set-ADAccountPassword`
- Delegation of Control Wizard
- Group Policy (GPO concepts)

---

*Part of an ongoing series of self-directed cybersecurity labs. All work performed in an isolated virtual environment.*