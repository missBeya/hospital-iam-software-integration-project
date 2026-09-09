# 05 - Access Management

## Goal

In this lesson, you will create and manage identities inside Active Directory and use Security Groups to control access.

By the end of the lesson, you should understand:

* user provisioning
* Security Groups
* group membership
* authentication
* authorization
* least privilege
* role-based access
* account disabling
* deprovisioning
* how to verify access decisions

---

## Where We Are

The environment now contains a Domain Controller running Active Directory:

```text
Windows Server
      │
      ├── Active Directory
      ├── DNS
      └── hospital.local
```

We also created Organizational Units to organise hospital identities.

Now we will begin creating the identities themselves.

A simplified structure might look like:

```text
hospital.local
      │
      ├── Doctors
      ├── Nurses
      ├── Pharmacy
      └── IT
```

But organising users is only part of Identity and Access Management.

We must also decide:

> What should each identity be allowed to access?

---

## Identity and Access Management

Identity and Access Management, often shortened to **IAM**, combines two important questions:

### Identity

**Who is the user?**

### Access

**What should that user be allowed to do?**

For example:

```text
Emma Wilson
     │
     ├── Identity: Nurse
     │
     └── Access: Nursing resources
```

Active Directory helps us manage both identity information and access-related group memberships.

---

## Authentication vs Authorization

These concepts must remain separate.

### Authentication

Authentication verifies identity.

```text
Emma enters credentials
        ↓
Active Directory
        ↓
Credentials valid
        ↓
Authenticated
```

### Authorization

Authorization determines what the authenticated identity may access.

```text
Emma
  │
  ├── Authenticated
  │
  └── Member of GG-Nurses
              ↓
       Nursing resource
              ↓
          Access granted
```

A user may successfully authenticate and still receive:

```text
Access Denied
```

That is not necessarily an authentication failure.

It may be an authorization decision.

---

## What Is Provisioning?

**Provisioning** is the process of creating and configuring an identity so that a person or system receives the access required for their role.

A simplified onboarding process could be:

```text
Employee joins hospital
        ↓
Create identity
        ↓
Place in correct OU
        ↓
Assign Security Groups
        ↓
Provide required access
        ↓
User starts work
```

Provisioning should be controlled and repeatable.

---

## Example Hospital Identities

For this project, we can create fictional users such as:

| User            | Role                        |
| --------------- | --------------------------- |
| Emma Wilson     | Nurse                       |
| Mike Smith      | Doctor                      |
| David King      | Pharmacy                    |
| Solomon Prince  | IT                          |
| Marie DelGloria | No clinical access assigned |

These accounts are fictional and exist only for the simulated environment.

---

## Step 1 - Open Active Directory Users and Computers

On the Domain Controller, open:

**Server Manager → Tools → Active Directory Users and Computers**

Expand:

```text
hospital.local
```

You should see the Organizational Units created previously.

---

## Step 2 - Create a User

Open the appropriate OU.

For example:

```text
Nurses
```

Right-click the OU and select:

**New → User**

Create a fictional identity.

Example:

```text
First name: Emma
Last name: Wilson
User logon name: emma.wilson
```

The resulting identity might be represented as:

```text
emma.wilson@hospital.local
```

---

## Step 3 - Configure the Initial Password

Active Directory will ask you to create a password.

For a simulated employee account, you may choose:

**User must change password at next logon**

This allows the administrator to provide a temporary password without keeping that password permanently.

!!! warning
Never use real personal, university or workplace passwords in this project.

Use credentials created specifically for the simulated environment.

---

## Password Policies

Active Directory may enforce password rules.

For example, the environment may require:

* minimum password length
* password complexity
* password history
* account lockout rules

If Active Directory rejects a password, investigate the policy rather than repeatedly guessing.

Password policy is part of identity security.

---

## Step 4 - Create the Remaining Users

Create additional fictional identities.

For example:

```text
Doctors OU
└── Mike Smith

Nurses OU
└── Emma Wilson

Pharmacy OU
└── David King

IT OU
└── Solomon Prince
```

You may also create another identity that intentionally receives no departmental Security Group.

For example:

```text
Marie DelGloria
```

We will use this account to demonstrate authorization.

---

## Step 5 - Verify Users with PowerShell

Open PowerShell as Administrator.

To list users:

```powershell
Get-ADUser -Filter *
```

To search for one user:

```powershell
Get-ADUser -Identity emma.wilson
```

PowerShell gives us another way to inspect Active Directory without relying only on the GUI.

---

## What Is a Security Group?

A **Security Group** allows multiple identities to be treated as one access-control unit.

Instead of giving permissions directly to every nurse:

```text
Emma → Clinical Folder
Sarah → Clinical Folder
John → Clinical Folder
Anna → Clinical Folder
```

we can create:

```text
GG-Nurses
    │
    ├── Emma
    ├── Sarah
    ├── John
    └── Anna
         │
         ▼
   Clinical Folder
```

We assign the permission to the **group**.

Users receive access by becoming members of that group.

---

## Why Use Groups?

Group-based access is easier to manage and audit.

Imagine 100 nurses need the same resource.

Without groups:

```text
100 users
   ↓
100 individual permission assignments
```

With a group:

```text
100 users
   ↓
GG-Nurses
   ↓
1 permission assignment
```

This is much easier to maintain.

---

## Step 6 - Create Security Groups

In Active Directory Users and Computers, create Security Groups for the hospital roles.

For example:

```text
GG-Doctors
GG-Nurses
GG-Pharmacy
GG-IT
```

The `GG` naming convention can be used to indicate a **Global Group**.

Naming conventions vary between organisations.

What matters is that names remain:

* understandable
* consistent
* meaningful

---

## Step 7 - Add Users to Groups

Add each fictional identity to the group appropriate for their role.

For example:

```text
Emma Wilson
    ↓
GG-Nurses

Mike Smith
    ↓
GG-Doctors

David King
    ↓
GG-Pharmacy

Solomon Prince
    ↓
GG-IT
```

Leave Marie outside these groups for now.

This gives us an account that can authenticate but does not have the same authorization as the clinical users.

---

## Step 8 - Verify Group Membership

Using the GUI:

1. Open the user
2. Select **Properties**
3. Open **Member Of**

You should see the Security Groups assigned to the account.

You can also verify using PowerShell:

```powershell
Get-ADPrincipalGroupMembership emma.wilson
```

This shows the groups associated with Emma's identity.

---

## A Simple Access Model

We can now model access like this:

```text
Emma Wilson
    │
    └── GG-Nurses
             │
             ▼
       Nursing Resource
```

For Mike:

```text
Mike Smith
    │
    └── GG-Doctors
             │
             ▼
        Doctor Resource
```

For Marie:

```text
Marie DelGloria
       │
       ├── Valid account
       └── Not in GG-Nurses
                    │
                    ▼
              Nursing Resource
                    │
                    ▼
               Access Denied
```

This demonstrates the difference between authentication and authorization.

---

## Least Privilege

The **Principle of Least Privilege** means that identities should receive only the access required to perform their responsibilities.

A nurse may need access to nursing resources.

That does not automatically mean the nurse should receive:

* Domain Administrator access
* IT administration access
* pharmacy administration access
* access to every server
* access to every file share

A simplified model is:

```text
User
 ↓
Job requirement
 ↓
Minimum necessary access
```

Not:

```text
User
 ↓
Give everything
```

---

## Why Least Privilege Matters

Excessive permissions increase risk.

If an account is:

* compromised
* misused
* incorrectly configured

the damage is limited when the account only has the permissions required for its role.

Least privilege is therefore both an operational and security principle.

---

## Role-Based Access

Many organisations assign access according to roles.

For example:

```text
Role: Nurse
    ↓
GG-Nurses
    ↓
Nursing permissions
```

If Emma later changes role:

```text
Nurse
  ↓
Doctor
```

the administrator should review and change her access rather than simply adding more permissions indefinitely.

---

## Avoid Permission Accumulation

Consider this example:

````text
Emma starts as Nurse
       ↓
Gets Nurses access

Emma moves to Pharmacy
       ↓
Gets Pharmacy access
```

If nobody removes the old group:

~~~text
Emma
 ├── Nurses access
 └── Pharmacy access
````

She may now have more access than her current role requires.

This is sometimes described as **privilege accumulation** or access creep.

Access should therefore be reviewed when roles change.

---

## Step 9 - Modify a User's Access

Suppose a fictional employee transfers from one department to another.

Review:

1. the user's current groups
2. the groups required for the new role
3. groups that are no longer required

Remove unnecessary memberships.

Add only the groups required for the new responsibilities.

Then verify the result.

---

## Joiner, Mover, Leaver

Identity lifecycle management is often described using three stages.

### Joiner

A new employee arrives.

```text
Joiner
  ↓
Create account
  ↓
Assign required access
```

### Mover

An employee changes role.

```text
Mover
  ↓
Review existing access
  ↓
Remove unnecessary permissions
  ↓
Add new required access
```

### Leaver

An employee leaves the organisation.

```text
Leaver
  ↓
Disable access
  ↓
Remove sensitive memberships
  ↓
Complete deprovisioning
```

This lifecycle is extremely important in enterprise IAM.

---

## What Is Deprovisioning?

**Deprovisioning** removes or disables access when an identity should no longer be active.

For example, if a staff member leaves the hospital:

```text
Employee leaves
      ↓
Disable account
      ↓
Remove access
      ↓
Review ownership/resources
      ↓
Account handled according to policy
```

Simply forgetting the account creates a security risk.

---

## Step 10 - Disable a User Account

In Active Directory Users and Computers:

1. locate the user
2. right-click the account
3. select **Disable Account**

A disabled account remains in Active Directory but cannot normally authenticate.

This can be safer than immediately deleting an account because administrators may need to preserve information during an offboarding process.

---

## Disable vs Delete

These actions are different.

### Disable

```text
Account exists
      ↓
Authentication blocked
```

### Delete

```text
Directory object removed
```

In real organisations, offboarding procedures determine when accounts are disabled, retained, archived or deleted.

Do not assume deletion should always be immediate.

---

## Disable an Account with PowerShell

You can disable an account using:

```powershell
Disable-ADAccount -Identity marie.delgloria
```

You can verify whether an account is enabled with:

```powershell
Get-ADUser -Identity marie.delgloria -Properties Enabled
```

---

## Enable an Account

If an account was disabled for testing and needs to be restored:

```powershell
Enable-ADAccount -Identity marie.delgloria
```

Always verify that restoring an account is actually appropriate before enabling it.

---

## Inspect Group Membership with PowerShell

You can inspect a user:

```powershell
Get-ADUser -Identity emma.wilson
```

You can inspect the groups they belong to:

```powershell
Get-ADPrincipalGroupMembership emma.wilson
```

You can inspect members of a group:

```powershell
Get-ADGroupMember -Identity GG-Nurses
```

These commands are useful during troubleshooting.

---

## Troubleshooting Access

Suppose Emma reports:

> I can sign in, but I cannot access the nursing resource.

Do not immediately reset her password.

Work through the problem logically.

### Step 1 - Can she authenticate?

Can Emma sign in successfully?

If yes, her credentials may not be the problem.

### Step 2 - Is the account enabled?

```powershell
Get-ADUser emma.wilson -Properties Enabled
```

### Step 3 - Is she in the required group?

```powershell
Get-ADPrincipalGroupMembership emma.wilson
```

Look for:

```text
GG-Nurses
```

### Step 4 - Does the group have permission?

Even if Emma is in the correct group, the group itself must be authorized to the resource.

### Step 5 - Has the access change taken effect?

Some access changes may require a new logon session or refreshed authentication token before they are reflected everywhere.

---

## A Better Troubleshooting Question

Instead of asking:

> Why can't the user access the folder?

Break the problem into layers:

```text
Does the identity exist?
        ↓
Is the account enabled?
        ↓
Can the user authenticate?
        ↓
Is the user in the correct group?
        ↓
Does that group have permission?
        ↓
Can the resource be reached?
```

This makes troubleshooting much more systematic.

---

## Why Not Give Permissions Directly to Users?

Direct user permissions may work technically.

But they become difficult to manage at scale.

For example:

````text
Emma → permission
Mike → permission
David → permission
Solomon → permission
```

becomes harder to understand later.

A group model is clearer:

~~~text
Users
  ↓
Security Groups
  ↓
Permissions
  ↓
Resources
````

This creates separation between:

**Who the users are**

and:

**What access their roles receive**

---

## Identity vs Group vs Permission

Keep these three layers separate.

```text
Identity
Emma Wilson
     ↓
Group
GG-Nurses
     ↓
Permission
Read / Modify
     ↓
Resource
Clinical Folder
```

This model will become very important in the next lesson when we configure SMB and NTFS permissions.

---

## Service Accounts

Not every Active Directory identity represents a human.

Applications and services may also require identities.

These are commonly called **service accounts**.

A service account may later allow our Ubuntu application to communicate with Active Directory.

For example:

```text
Ubuntu Application
        │
        │ Uses service identity
        ▼
Active Directory
        │
        └── Directory query
```

Service accounts should also follow least privilege.

An application that only needs to read directory information should not automatically receive Domain Administrator privileges.

---

## What You Should Understand

Before continuing, make sure you can explain:

**What is provisioning?**

Creating and configuring an identity with the access required for its role.

**What is authorization?**

Determining what an authenticated identity is allowed to access.

**Why use Security Groups?**

They allow permissions to be managed through reusable access groups instead of assigning access individually to every user.

**What is least privilege?**

Giving an identity only the permissions needed to perform its responsibilities.

**What is deprovisioning?**

Removing or disabling access when it is no longer required.

**What is the Joiner-Mover-Leaver lifecycle?**

A model for managing access when users join an organisation, change roles or leave.

**Why might an account be disabled instead of immediately deleted?**

Because organisations may need to block access while temporarily preserving the directory object for operational, security or policy reasons.

---

## Checkpoint

Before continuing, confirm that:

* fictional Active Directory users exist
* users are organised appropriately
* Security Groups exist
* users have been added to the correct groups
* group membership can be checked
* you understand authentication vs authorization
* you understand least privilege
* you can disable an account
* you understand provisioning and deprovisioning
* you understand Joiner, Mover and Leaver processes

---

## Challenge

Consider these two users:

```text
Emma Wilson
Account: Enabled
Password: Valid
Group: GG-Nurses

Marie DelGloria
Account: Enabled
Password: Valid
Group: None
```

Both attempt to access a resource configured for `GG-Nurses`.

Emma receives:

```text
Access Granted
```

Marie receives:

```text
Access Denied
```

Answer these questions:

1. Did both users authenticate successfully?
2. Why did Marie receive Access Denied?
3. Is this an authentication problem or an authorization problem?
4. What would you check before adding Marie to `GG-Nurses`?

The important answer to question 4 is:

> Confirm that Marie's actual job role requires that access.

Never fix an access problem by granting permissions before verifying that the user should have them.

---

## Next Step

We now have identities and Security Groups.

Next, we will connect those groups to a real resource by creating an SMB file share and applying group-based permissions.

[Continue to SMB & Permissions →](06-smb.md){ .md-button .md-button--primary }

