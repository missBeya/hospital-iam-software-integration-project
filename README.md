# Hospital Identity & Access Integration Project

A hands-on project that simulates how software can be integrated into an existing hospital IT environment.

**[View the guided learning site](https://missbeya.github.io/hospital-iam-software-integration-project/)**

> This is a simulated learning environment. No real hospital systems, patient data, or production credentials are used.

## Project Goal

The project explores how identity, networking, access control, and application integration work together inside an enterprise environment.

It is designed to turn concepts such as Active Directory, LDAP, authentication, authorisation, DNS, and service accounts into practical experience.

## Architecture

```text
Host Computer
(Windows, macOS, or Linux)
        │
        └── Virtualisation Platform
                │
                ├── Windows Server 2022
                │      ├── Active Directory
                │      ├── DNS
                │      ├── Users and OUs
                │      ├── Security Groups
                │      └── SMB Access Control
                │
                └── Ubuntu Integration VM
                       ├── Network Testing
                       ├── DNS Testing
                       ├── LDAP
                       └── Active Directory Queries
The project is not tied to a specific host operating system or virtualisation product.
Current Progress
Windows Server 2022 and Active Directory environment
Hospital users, OUs, and Security Groups
DNS and network configuration
SMB and NTFS access-control testing
Ubuntu integration machine
LDAP service-account authentication
Active Directory queries from Linux
Troubleshooting scenarios
Future stages will introduce application integration, SQL, PowerShell automation, provisioning and deprovisioning, and LDAPS/TLS.
Documentation
The complete step-by-step project is available on the:
Hospital Identity & Access Integration Learning Site
