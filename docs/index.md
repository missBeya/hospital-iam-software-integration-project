# Hospital Identity & Access Integration Project

**Build it. Break it. Troubleshoot it. Understand it.**

Welcome to a beginner-friendly, hands-on project where you will build a small simulated hospital IT environment and gradually integrate a new software application into it.

!!! note "Safe learning environment"
    This project uses fictional users and simulated infrastructure.
    No real hospital systems, patient data, or production credentials are used.

---

## Your Mission

Imagine you have joined the technical implementation team of a hospital.

The hospital needs an IT environment that can:

- manage doctors, nurses, pharmacy staff, and IT users
- authenticate employees
- control access to resources
- support new software applications
- integrate applications with existing identity systems
- be investigated when something goes wrong

You will build that environment step by step.

---

## What You Will Build

~~~text
Host Computer
(Windows, macOS, or Linux)
        │
        └── Virtualization Platform
                │
                ├── Windows Server 2022 VM
                │      │
                │      ├── Active Directory
                │      ├── DNS
                │      ├── Users & OUs
                │      ├── Security Groups
                │      └── SMB Access Control
                │
                └── Ubuntu Integration VM
                       │
                       ├── Network Testing
                       ├── DNS Queries
                       ├── LDAP
                       └── Software Integration
~~~

The exact virtualization software you use may depend on your host operating system.

For example, you might use VMware Fusion on macOS, VMware Workstation on Windows or Linux, or another virtualization platform capable of running the required virtual machines.

The important part of this project is the **architecture and concepts**, not the brand of hypervisor.

---

## What You Will Learn

By working through the project, you will gain hands-on experience with:

- virtualization and virtual machines
- Windows Server 2022
- Active Directory Domain Services
- DNS and IP networking
- Identity and Access Management
- authentication and authorization
- Organizational Units and Security Groups
- SMB and NTFS permissions
- Ubuntu Linux
- LDAP
- service accounts
- PowerShell
- troubleshooting
- enterprise software integration

---

## Learning Path

### Part 1 — Build the Environment

**01. Virtualization Setup**  
Create the virtual machines that will form the hospital environment.

**02. Windows Server**  
Prepare the Windows Server that will become the Domain Controller.

**03. Networking**  
Configure IP addressing, DNS, connectivity, and time synchronization.

### Part 2 — Identity & Access

**04. Active Directory**  
Build the `hospital.local` domain and create the hospital identity structure.

**05. Access Management**  
Create users, Security Groups, and test authorization.

**06. SMB & Permissions**  
Protect hospital resources using group-based access control.

### Part 3 — Integration

**07. Ubuntu Integration Machine**  
Introduce a Linux system that will act as an external integration machine.

**08. LDAP Integration**  
Allow an external system to query identities from Active Directory.

### Coming Next

The project will later expand into:

- application-level integration
- SQL and database integration
- PowerShell automation
- provisioning and deprovisioning
- LDAPS / TLS
- client workstations
- troubleshooting challenges

---

## How This Project Teaches

This is not a "copy commands and hope they work" tutorial.

For each stage, we will ask:

1. **What are we building?**
2. **Why is it needed?**
3. **How do we configure it?**
4. **How do we verify it?**
5. **What could go wrong?**
6. **What did we learn?**

Real problems encountered while building the environment are preserved as troubleshooting exercises.

!!! example "Authentication vs Authorization"
    We configured an SMB clinical folder for nurses.

    Emma was a member of `GG-Nurses` and could access the resource.

    Marie had a valid Active Directory account but was not a member of the required group, so access was denied.

    Both users could have valid identities, but only one was authorized to access the resource.

---

## Ready?

[Start with Virtualization Setup →](01-virtualisation-setup.md){ .md-button .md-button--primary }

---

**Project status:** Work in progress
