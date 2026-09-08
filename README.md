# Hospital Identity & Access Integration Project

**Live learning site:** [Open the guided documentation](https://missbeya.github.io/hospital-iam-software-integration-project/)

A beginner-friendly, hands-on project that simulates a hospital IT environment to explore identity management, access control, networking, Windows Server, Active Directory, Linux integration, LDAP, PowerShell, troubleshooting, and software integration.

> This is a simulated learning environment. No real hospital systems, patient data, or production credentials are used.

---

## Project Goal

The goal of this project is to understand how a new software application can be integrated into an existing enterprise environment.

The project starts by building a small hospital infrastructure and gradually introduces:

- virtualisation
- networking
- identity management
- authentication
- authorisation
- access control
- Linux integration
- LDAP
- automation
- software integration
- troubleshooting

---

## Scenario

A fictional hospital has employees such as doctors, nurses, pharmacy staff, and IT staff.

We are building the infrastructure required to:

- manage hospital identities
- organise users and departments
- authenticate users
- control access to resources
- integrate a new software application
- allow external systems to query Active Directory
- automate repetitive administration tasks
- troubleshoot common implementation issues

---

## Current Architecture

```text
Host Computer
(Windows, macOS, or Linux)
        │
        └── Virtualisation Platform
                │
                ├── Windows Server 2022 VM
                │      ├── Active Directory Domain Services
                │      ├── DNS
                │      ├── Domain: hospital.local
                │      ├── Domain Controller: HOSP-DC01
                │      ├── Users / OUs
                │      ├── Security Groups
                │      └── SMB shared resources
                │
                └── Ubuntu Integration VM
                       ├── Network connectivity testing
                       ├── DNS testing
                       ├── LDAP connectivity
                       ├── Service-account authentication
                       └── Active Directory queries
The project is not tied to a specific host operating system or virtualisation product.
The original environment was built using VMware Fusion, but students can use another compatible virtualisation platform such as VMware Workstation, VirtualBox, Hyper-V, or an equivalent tool.
What Has Been Implemented
So far, the project includes:
Windows Server 2022 virtual machine
Active Directory forest and domain
DNS
hospital organisational units
fictional hospital users
security groups
group-based access control
SMB shared resources
NTFS permissions
authorised and unauthorised access testing
Ubuntu integration machine
network and DNS testing
LDAP connectivity
dedicated service account
Active Directory queries from Linux
Learning Path
Part 1 - Build the Environment
Virtualisation Setup
Windows Server
Networking
Part 2 - Identity and Access
Active Directory
Access Management
SMB and Permissions
Part 3 - Integration
Ubuntu Integration
LDAP Integration
Future Stages
The project will continue with:
application-level integration
SQL and database integration
PowerShell automation
provisioning and deprovisioning
LDAPS / TLS
client workstation integration
additional troubleshooting scenarios
Technologies and Concepts
This project explores:
Windows Server 2022
Active Directory Domain Services
DNS
TCP/IP networking
Identity and Access Management
authentication
authorisation
organisational units
security groups
SMB
NTFS permissions
Ubuntu Linux
LDAP
service accounts
PowerShell
virtual machines
enterprise software integration
troubleshooting
Learning Approach
The project is designed around understanding, not simply copying commands.
Each stage focuses on:
what is being built
why it is needed
how it is configured
how to verify it
what can go wrong
how to troubleshoot it
what should be understood afterwards
Real problems encountered during the build are preserved as troubleshooting exercises.
Documentation
The full guided learning experience is available here:
Hospital Identity & Access Integration Project - Live Documentation
Project Status
This project is actively being developed and will continue to expand as new integration, automation, database, and troubleshooting scenarios are added.
