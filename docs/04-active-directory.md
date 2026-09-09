# 04 - Active Directory

## Goal

In this lesson, you will turn the Windows Server into a **Domain Controller** by installing and configuring **Active Directory Domain Services (AD DS)**.

By the end of the lesson, you should understand:

* what Active Directory is
* what a domain is
* what a Domain Controller does
* what Active Directory Domain Services provides
* why DNS is critical to Active Directory
* what a forest is
* what Organizational Units are
* how to verify that Active Directory is working

---

## Where We Are

So far, the environment contains two virtual machines connected through a virtual network:

```text
Host Computer
        │
        └── Virtual Network
                │
                ├── Windows Server
                │      └── Static IP
                │
                └── Ubuntu
```

The Windows Server currently behaves like a normal standalone server.

In this lesson, we will add an identity system:

```text
Windows Server
      │
      ├── Active Directory Domain Services
      ├── DNS
      └── Domain Controller
```

Later, users, applications and other machines will be able to use this server for identity and authentication.

---

## What Is Active Directory?

Active Directory is Microsoft's directory technology for centrally managing identities and resources in an enterprise environment.

Instead of managing every user separately on every computer, an organisation can maintain identities centrally.

For example, a hospital may have:

```text
Active Directory
      │
      ├── Doctors
      ├── Nurses
      ├── Pharmacy Staff
      ├── IT Staff
      └── Service Accounts
```

Applications and computers can then use this directory to determine who a user is and what resources they may access.

---

## What Is Active Directory Domain Services?

**Active Directory Domain Services**, usually shortened to **AD DS**, is the Windows Server role that provides the directory service.

AD DS stores information about objects such as:

* users
* computers
* groups
* Organizational Units
* service accounts
* policies

It also supports authentication and centralized identity management.

---

## Authentication vs Authorization

These two concepts are important throughout this project.

### Authentication

Authentication answers:

> Who are you?

For example:

```text
Username + Password
        ↓
Active Directory
        ↓
Identity verified
```

### Authorization

Authorization answers:

> What are you allowed to access?

For example:

```text
Emma Wilson
     │
     ├── Valid account
     │
     └── Member of Nurses group
              ↓
       Access clinical folder
```

A user can be successfully authenticated but still not be authorized to access a particular resource.

---

## What Is a Domain?

A domain is a logical administrative boundary containing identities and computers managed through Active Directory.

For this simulated environment, we will use:

```text
hospital.local
```

A user may eventually have an identity such as:

```text
emma@hospital.local
```

or may sign in using a domain-qualified username such as:

```text
HOSPITAL\emma
```

!!! note
`hospital.local` is used here only as a simple learning-domain example.

```
Production organisations normally choose domain naming based on their real DNS and identity architecture rather than copying this lab name.
```

---

## What Is a Domain Controller?

A **Domain Controller**, or **DC**, is a server running Active Directory Domain Services for a domain.

It helps provide services such as:

* authentication
* directory queries
* identity management
* security policy
* group membership information
* service discovery through DNS

Our architecture will become:

```text
Ubuntu / Client
       │
       │ Authentication or directory request
       ▼
Windows Server
Domain Controller
       │
       └── Active Directory
```

---

## Why Does DNS Matter?

Active Directory depends heavily on DNS.

Clients do not simply need to know an IP address.

They need to discover services such as Domain Controllers.

A client might ask:

````text
Where is the Domain Controller
for hospital.local?
```

DNS helps provide that information.

That is why a system can have working internet access but still fail to join or use an Active Directory domain if its DNS configuration is incorrect.

---

## Before You Start

Before installing Active Directory, confirm that:

- Windows Server starts correctly
- the server has a stable IP address
- the subnet mask is correct
- the default gateway is correct
- basic network connectivity works
- the server time is reasonably accurate

You can inspect networking with:

~~~powershell
ipconfig /all
````

You can check the hostname with:

```powershell
hostname
```

---

## Step 1 - Open Server Manager

Sign in to Windows Server using the local Administrator account.

Open:

**Server Manager**

Then select:

**Manage → Add Roles and Features**

This wizard allows Windows Server roles to be installed.

---

## Step 2 - Choose the Installation Type

Select:

**Role-based or feature-based installation**

This option is used when adding a server role to the current Windows Server.

Continue to the server selection screen.

---

## Step 3 - Select the Server

Select the current Windows Server from the server pool.

Confirm that you are installing the role on the intended machine.

Then continue.

---

## Step 4 - Install Active Directory Domain Services

From the list of server roles, select:

**Active Directory Domain Services**

Windows may ask to install additional management tools required by AD DS.

Accept the required features.

Then continue through the wizard.

At this point, we are installing the software required to provide Active Directory.

We have **not created the domain yet**.

---

## Step 5 - Complete the AD DS Role Installation

Review the selected options and begin the installation.

When the role installation finishes, Server Manager should display a notification indicating that additional configuration is required.

The server now contains the AD DS software, but it is not yet acting as a Domain Controller.

That happens during **promotion**.

---

## What Does “Promote This Server” Mean?

Installing AD DS and creating a Domain Controller are related but different steps.

```text
Install AD DS role
        ↓
Server gains directory-service software
        ↓
Promote server
        ↓
Server becomes Domain Controller
```

Promotion configures the server to provide Active Directory for a domain.

---

## Step 6 - Promote the Server to a Domain Controller

In Server Manager, select the notification flag.

Choose:

**Promote this server to a domain controller**

The Active Directory Domain Services Configuration Wizard will open.

---

## Step 7 - Create a New Forest

Because this is the first Domain Controller in our environment, select:

**Add a new forest**

Enter the root domain name:

```text
hospital.local
```

---

## What Is a Forest?

A forest is the top-level Active Directory structure that can contain one or more domains.

For this project:

```text
Forest
   │
   └── hospital.local
```

Our environment only needs one domain, so the architecture remains simple.

In larger organisations, Active Directory architectures can contain multiple domains.

---

## Step 8 - Configure Domain Controller Options

The wizard will display Domain Controller options.

For this environment, the server can provide:

* Domain Name System (DNS)
* Global Catalog services

You will also be asked to create a:

**Directory Services Restore Mode (DSRM) password**

DSRM is used for special Active Directory recovery and maintenance scenarios.

Use a strong lab-specific password and store it safely.

!!! warning
Do not reuse personal, university or workplace passwords in the lab.

---

## Step 9 - Review the DNS Warning

During a new lab-domain installation, the wizard may display DNS-related warnings.

Read the warning rather than automatically assuming the installation has failed.

The new Domain Controller will install and configure DNS services required for the Active Directory environment.

Continue after reviewing the configuration.

---

## Step 10 - Review the NetBIOS Domain Name

Windows will generate a NetBIOS domain name.

For example:

```text
HOSPITAL
```

This allows usernames to be represented in a format such as:

```text
HOSPITAL\emma
```

The DNS-style domain name remains:

```text
hospital.local
```

These are two ways of referring to the domain in different contexts.

---

## Step 11 - Complete the Prerequisite Check

Before installation continues, Windows performs prerequisite checks.

The wizard checks whether the server is ready to become a Domain Controller.

Review any warnings or errors.

If the prerequisite check passes, continue with the installation.

---

## Step 12 - Restart the Server

During promotion, Windows configures:

* Active Directory Domain Services
* the new domain
* the directory database
* DNS integration
* Domain Controller services

The server will restart.

After the restart, the machine is no longer simply a standalone Windows Server.

It is now a **Domain Controller** for the Active Directory domain.

---

## Step 13 - Sign In to the Domain

After restart, examine the sign-in screen.

You should now see the domain identity context.

For example:

```text
HOSPITAL\Administrator
```

This is different from signing into a purely local machine account.

---

## Step 14 - Verify Active Directory

Open:

**Server Manager → Tools**

You should now see administrative tools such as:

* Active Directory Users and Computers
* Active Directory Administrative Center
* DNS
* Active Directory Domains and Trusts
* Active Directory Sites and Services

Open:

**Active Directory Users and Computers**

You should see the domain:

```text
hospital.local
```

This confirms that the Active Directory structure exists.

---

## Step 15 - Verify Active Directory with PowerShell

PowerShell provides another way to inspect the environment.

Open PowerShell as Administrator.

Run:

```powershell
Get-ADDomain
```

This returns information about the Active Directory domain.

You can also run:

```powershell
Get-ADForest
```

to inspect the forest.

To inspect the Domain Controller:

```powershell
Get-ADDomainController
```

---

## Why Use Both GUI and PowerShell?

Enterprise administrators often use both.

### GUI tools

Useful for:

* learning the structure
* visual inspection
* occasional administration
* exploring object properties

### PowerShell

Useful for:

* automation
* repeatable tasks
* bulk operations
* troubleshooting
* scripting

Throughout this project, we will use both approaches.

---

## Step 16 - Open DNS Manager

From Server Manager, open:

**Tools → DNS**

You should see DNS zones associated with the Active Directory environment.

The domain:

```text
hospital.local
```

should now be represented in DNS.

This is an important moment in the project because Active Directory and DNS are now working together.

---

## Step 17 - Test Domain DNS

Open PowerShell or Command Prompt.

Try:

```powershell
nslookup hospital.local
```

You can also inspect the configured DNS server:

```powershell
ipconfig /all
```

After promotion, the Domain Controller will normally use its own DNS service for Active Directory name resolution.

---

## Organizational Units

Active Directory contains many types of objects.

One particularly useful structure is the **Organizational Unit**, or **OU**.

OUs allow administrators to organise directory objects logically.

For example:

```text
hospital.local
      │
      ├── Doctors
      ├── Nurses
      ├── Pharmacy
      └── IT
```

An OU is primarily an **administrative container**.

It is not the same thing as a Security Group.

---

## OU vs Security Group

This distinction is extremely important.

### Organizational Unit

Used primarily to organise and administer objects.

Example:

```text
OU: Nurses
    ├── Emma Wilson
    └── Sarah Jones
```

### Security Group

Used to assign permissions or represent access membership.

Example:

```text
Security Group: GG-Nurses
       │
       ├── Emma Wilson
       └── Sarah Jones
              ↓
       Clinical Folder Access
```

So:

```text
OU
↓
Organisation / Administration

Security Group
↓
Access / Authorization
```

We will explore Security Groups properly in the next lesson.

---

## Step 18 - Create the Hospital OUs

Open:

**Active Directory Users and Computers**

Right-click the domain:

```text
hospital.local
```

Choose:

**New → Organizational Unit**

Create example OUs such as:

```text
Doctors
Nurses
Pharmacy
IT
```

Your structure may look like:

```text
hospital.local
      │
      ├── Doctors
      ├── Nurses
      ├── Pharmacy
      └── IT
```

These OUs will later contain the simulated hospital identities.

---

## Why Organise Identities?

Imagine managing thousands of identities in one flat list.

That quickly becomes difficult.

A directory structure makes it easier to understand where objects belong and where administrative rules may be applied.

For example:

```text
Hospital
   │
   ├── Clinical Staff
   │      ├── Doctors
   │      └── Nurses
   │
   └── Technical Staff
          └── IT
```

The exact structure will depend on an organisation's requirements.

---

## How Active Directory Stores Identities

An Active Directory user is more than a username.

A directory object can contain information such as:

* first name
* surname
* username
* email address
* department
* group memberships
* password-related attributes
* account status

Later, applications can query some of this information using protocols such as LDAP.

That is how this Active Directory lesson connects to the future integration stage.

---

## Active Directory and LDAP

LDAP stands for **Lightweight Directory Access Protocol**.

Active Directory supports LDAP so that systems can communicate with the directory.

Later our Ubuntu integration machine will be able to make requests such as:

```text
Ubuntu Application
       │
       │ LDAP
       │ TCP 389
       ▼
Domain Controller
       │
       ▼
Active Directory
       │
       └── User information
```

This is where our project moves from infrastructure administration toward software integration.

---

## How Authentication Will Eventually Work

A simplified authentication flow may look like:

```text
User
 │
 │ Credentials
 ▼
Application
 │
 │ Authentication request
 ▼
Active Directory
 │
 ├── Valid credentials → Success
 │
 └── Invalid credentials → Failure
```

Authentication only confirms identity.

The application may still need group membership or other information to make authorization decisions.

---

## Troubleshooting Active Directory

When Active Directory does not work, avoid assuming that AD DS itself is broken.

Check the surrounding dependencies.

### Layer 1 - Server

Is Windows Server running?

```powershell
hostname
```

### Layer 2 - Network

Does the server have the correct IP configuration?

```powershell
ipconfig /all
```

### Layer 3 - DNS

Can the domain be resolved?

```powershell
nslookup hospital.local
```

### Layer 4 - Active Directory

Can PowerShell query the domain?

```powershell
Get-ADDomain
```

### Layer 5 - Service or Application

Can the external system reach the required service?

Later we may test ports such as:

```text
DNS   → TCP/UDP 53
LDAP  → TCP 389
LDAPS → TCP 636
SMB   → TCP 445
```

This layered troubleshooting approach prevents us from blaming an application when the actual problem exists lower in the infrastructure.

---

## Time Synchronisation

Accurate time is important in domain environments.

Authentication systems such as Kerberos are sensitive to significant differences between system clocks.

Check the Windows time service with:

```powershell
w32tm /query /status
```

If authentication behaves unexpectedly, time synchronization should be part of the investigation.

---

## What You Should Understand

Before continuing, make sure you can explain:

**What is Active Directory?**

A centralized directory system used to manage identities, computers and other objects in a Windows enterprise environment.

**What is AD DS?**

The Windows Server role that provides Active Directory Domain Services.

**What is a Domain Controller?**

A server running AD DS that provides directory and authentication services for a domain.

**What is a domain?**

A logical Active Directory environment containing centrally managed identities and resources.

**What is a forest?**

The top-level Active Directory structure that can contain one or more domains.

**Why does Active Directory depend on DNS?**

Because clients use DNS to locate domain services, including Domain Controllers.

**What is an OU?**

A container used to organise and administer Active Directory objects.

**Is an OU the same as a Security Group?**

No.

OUs organise objects.

Security Groups are commonly used to assign permissions and represent authorization membership.

---

## Checkpoint

Before moving on, confirm that:

* AD DS is installed
* Windows Server has been promoted to a Domain Controller
* the `hospital.local` domain exists
* DNS is installed and working
* Active Directory Users and Computers opens
* `Get-ADDomain` returns domain information
* `Get-ADForest` returns forest information
* the hospital OUs exist
* you understand the difference between an OU and a Security Group

---

## Challenge

Imagine a new nurse called **Emma Wilson** joins the hospital.

Before creating anything, decide:

1. Where should her Active Directory user object be organised?
2. What determines whether she is allowed to access a clinical folder?
3. Which concept verifies her identity?
4. Which concept controls what she can access?

A possible answer is:

```text
Emma Wilson
    │
    ├── User object → Nurses OU
    │
    ├── Authentication → confirms identity
    │
    └── Security Group membership
                    ↓
                Authorization
                    ↓
             Resource access
```

In the next lesson, you will build exactly this model.

---

## Next Step

The hospital now has a centralized identity system.

Next, we will create users and Security Groups, then use group membership to control access.

[Continue to Access Management →](05-access-management.md){ .md-button .md-button--primary }

