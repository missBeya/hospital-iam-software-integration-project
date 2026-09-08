# Hospital Identity & Access Integration Project

**Live learning site:** [Open the guided documentation](https://missbeya.github.io/hospital-iam-software-integration-project/)

A beginner-friendly hands-on project that simulates a hospital IT environment to explore identity management, access control, networking, Windows Server, Active Directory, Linux integration, LDAP, PowerShell, and troubleshooting.

> This is a simulated learning environment. No real hospital systems or patient data are used.

---

##  Project Goal

The goal of this project is to understand how a new software application can be integrated into an existing enterprise environment.

The project starts by building the hospital infrastructure and gradually introduces identity management, access control, networking, automation, and external application integration.

---

## Scenario

A fictional hospital already has employees such as doctors, nurses, pharmacy staff, and IT staff.

We are building the infrastructure required to:

- manage hospital identities
- organize users and departments
- control access to resources
- integrate a new software application
- allow the application to query Active Directory
- troubleshoot common implementation issues
- automate repetitive administration tasks

---

## Current Architecture

```text
Mac Host
   │
   └── VMware Fusion
        │
        ├── Windows Server 2022
        │    ├── Active Directory Domain Services
        │    ├── DNS
        │    ├── Domain: hospital.local
        │    ├── Domain Controller: HOSP-DC01
        │    ├── Users / OUs
        │    ├── Security Groups
        │    └── SMB shared resources
        │
        └── Ubuntu VM
             ├── Network connectivity testing
             ├── DNS testing
             ├── LDAP connectivity
             └── Active Directory queries
