# Hospital Identity & Access Integration Project

A hands-on project that simulates how software can be integrated into an existing hospital IT environment.

> This is a simulated learning environment. No real hospital systems, patient data, or production credentials are used.

## Overview

The project explores how identity, networking, access control, and application integration work together inside an enterprise environment.

It turns concepts such as Active Directory, LDAP, DNS, authentication, authorisation, service accounts, and troubleshooting into practical experience.

## Architecture

~~~text
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
~~~

The project is not tied to a specific host operating system or virtualisation product.

A compatible hypervisor can be used on Windows, macOS, or Linux.

## Current Progress

- Windows Server 2022 environment created
- Active Directory Domain Services configured
- `hospital.local` domain created
- Hospital users and organisational units created
- Security Groups configured
- DNS and network connectivity tested
- SMB and NTFS access control implemented
- Authorised and unauthorised access tested
- Ubuntu integration machine configured
- LDAP connectivity tested
- Dedicated application service account created
- Active Directory users queried from Linux
- Troubleshooting scenarios documented

## Project Roadmap

Future stages will introduce:

- application-level integration
- SQL and database integration
- PowerShell automation
- user provisioning and deprovisioning
- LDAPS and TLS
- client workstation integration
- additional troubleshooting scenarios

## Learning Approach

The project is designed around understanding rather than simply copying commands.

Each stage focuses on:

1. what is being built
2. why it is needed
3. how it is configured
4. how to verify it
5. what can go wrong
6. how to troubleshoot it
7. what should be understood afterwards

The environment will continue to evolve as additional enterprise integration scenarios are added.
