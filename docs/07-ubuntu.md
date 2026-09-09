# 07 - Prepare the Ubuntu Integration Machine

## Goal

In this lesson, you will prepare an Ubuntu virtual machine to act as an **external integration system**.

This machine will later communicate with the Windows Server and Active Directory environment.

By the end of the lesson, you should understand:

* why we use a separate integration machine
* how to inspect a Linux system before integration
* how to verify networking
* how to verify DNS
* how to install integration and troubleshooting tools
* how to test reachability to the Domain Controller
* why application integration should be tested layer by layer

---

## Where We Are

Our hospital environment currently contains:

```text
Windows Server
      │
      ├── Active Directory
      ├── DNS
      ├── Users
      ├── Security Groups
      └── SMB Resources
```

Until now, most administration has happened directly on Windows Server.

That changes here.

We will introduce a separate Linux system:

```text
Hospital Infrastructure
        │
        ├── Windows Server
        │      └── Active Directory
        │
        └── Ubuntu
               └── Integration System
```

Ubuntu represents a system or application that needs to communicate with infrastructure it does not own.

---

## Why Use a Separate Machine?

Imagine a hospital purchases a new software product.

The application might need to:

* find users in Active Directory
* authenticate users
* inspect group membership
* connect to existing servers
* communicate through specific network ports

The application is not installed inside the Domain Controller itself.

Instead:

```text
Application Server
       │
       │ requests
       ▼
Existing Hospital Infrastructure
       │
       └── Active Directory
```

Our Ubuntu VM simulates this application-side environment.

---

## Integration Is More Than Installing Software

A common mistake is to think:

> The application is installed, so the integration is complete.

Installation is only one part.

A successful integration may depend on:

```text
Application
     ↓
Operating System
     ↓
Network
     ↓
DNS
     ↓
Firewall / Ports
     ↓
Authentication
     ↓
Directory Service
```

If one layer fails, the application may stop working.

That is why we verify the environment before attempting LDAP integration.

---

## Before You Start

You should already have:

* the Ubuntu VM created
* Windows Server running
* Active Directory configured
* a working virtual network
* the Domain Controller IP address
* both VMs connected to compatible virtual networks

Do not copy IP addresses from this guide.

Use the addresses from your own environment.

---

## Step 1 - Start Ubuntu

Start the Ubuntu VM and sign in.

Open Terminal.

On many Ubuntu desktop systems, you can open Terminal using:

```text
Ctrl + Alt + T
```

The terminal gives us direct access to system and networking tools.

---

## Step 2 - Check the Hostname

Run:

```bash
hostname
```

The hostname identifies the Linux machine.

Example:

```text
ubuntu
```

For a larger environment, you might eventually use a more descriptive name such as:

```text
integration01
```

or:

```text
app01
```

A clear hostname makes troubleshooting easier when multiple machines exist.

---

## Step 3 - Inspect the Operating System

Check the installed Ubuntu version:

```bash
cat /etc/os-release
```

This helps identify:

* operating system
* distribution
* version

Knowing the operating system version can matter when troubleshooting package compatibility or application requirements.

---

## Step 4 - Inspect CPU and Memory

Check CPU information:

```bash
lscpu
```

Check memory:

```bash
free -h
```

Example:

```text
Mem:
total        4.0Gi
available    2.1Gi
```

The `-h` option means:

**human readable**

so values are displayed using units such as MB or GB.

---

## Step 5 - Inspect Storage

Run:

```bash
lsblk
```

This displays block devices such as disks and partitions.

You can also check filesystem usage:

```bash
df -h
```

These commands answer different questions.

`lsblk` helps show:

> What disks exist?

`df -h` helps show:

> How much filesystem space is available?

---

## Why Check Resources Before Integration?

Applications can fail because of infrastructure limitations.

For example:

```text
Application fails
      │
      ├── No disk space?
      ├── Not enough memory?
      ├── Wrong OS version?
      └── Missing dependency?
```

Before blaming the network or Active Directory, understand the machine hosting the application.

---

## Step 6 - Inspect the IP Address

Run:

```bash
ip addr
```

Look for the active network interface.

You may see an IPv4 address similar to:

```text
inet 192.168.100.20/24
```

Your address will probably be different.

The important thing is to identify:

* the interface
* IPv4 address
* subnet

---

## Step 7 - Inspect the Default Route

Run:

```bash
ip route
```

You may see:

```text
default via 192.168.100.1
```

This tells you which gateway Ubuntu uses to reach destinations outside its local subnet.

---

## Step 8 - Test Basic Network Connectivity

Start by testing the default gateway:

```bash
ping -c 4 <gateway-ip>
```

Then test an external IP address:

```bash
ping -c 4 8.8.8.8
```

The `-c 4` option tells Linux to send four ICMP requests and then stop.

If the external IP responds, basic IP connectivity is working.

---

## Step 9 - Test DNS Resolution

Now test a hostname.

For example:

```bash
getent hosts microsoft.com
```

or, if `nslookup` is installed:

```bash
nslookup microsoft.com
```

This helps separate two different situations.

### Situation A

```text
ping 8.8.8.8
SUCCESS

DNS lookup
SUCCESS
```

Network and DNS appear functional.

### Situation B

```text
ping 8.8.8.8
SUCCESS

DNS lookup
FAIL
```

Basic connectivity works, but DNS requires investigation.

---

## Step 10 - Inspect DNS Configuration

On many modern Ubuntu systems:

```bash
resolvectl status
```

Look for the DNS servers associated with the active interface.

Later, when Ubuntu needs to locate Active Directory resources by domain name, DNS configuration becomes especially important.

---

## External DNS vs Active Directory DNS

There is an important difference.

Public DNS helps resolve names such as:

```text
github.com
ubuntu.com
microsoft.com
```

Active Directory DNS helps locate internal resources such as:

```text
hospital.local
dc01.hospital.local
```

Our Ubuntu integration system will eventually need to understand the hospital domain.

---

## Step 11 - Update the Package Index

Before installing additional tools:

```bash
sudo apt update
```

This does not automatically upgrade every application.

It refreshes Ubuntu's information about the packages available from configured repositories.

---

## What Does `sudo` Mean?

Some system operations require administrative privileges.

`sudo` allows an authorized user to execute a command with elevated privileges.

For example:

```bash
apt install ...
```

may fail for a normal user.

Using:

```bash
sudo apt install ...
```

allows the package manager to make system-level changes.

---

## What Is APT?

APT is Ubuntu's package-management system.

It allows software packages to be:

* installed
* updated
* removed
* searched

For example:

```bash
sudo apt install curl
```

asks APT to install the `curl` package.

---

## Step 12 - Install curl

Install:

```bash
sudo apt install curl
```

`curl` is useful for testing HTTP and HTTPS services.

Later, if an application exposes a web endpoint or API, `curl` provides a quick way to verify whether the service responds.

Verify it:

```bash
curl --version
```

---

## Step 13 - Install Netcat

Install:

```bash
sudo apt install netcat-openbsd
```

Netcat gives us the `nc` command.

It is extremely useful for checking whether a specific TCP port is reachable.

For example:

```bash
nc -zv <server-ip> 389
```

This does not perform an LDAP login.

It asks a more basic question:

> Can Ubuntu establish a network connection to TCP port 389?

---

## Why Port Testing Matters

Imagine:

```text
ping Domain Controller
SUCCESS
```

but:

```text
nc -zv DomainController 389
FAIL
```

That tells us something important.

The machine itself may be reachable, but the LDAP service is not reachable on the required TCP port.

Possible causes include:

* firewall
* service not listening
* wrong port
* wrong IP address
* routing/security configuration

---

## Step 14 - Install LDAP Utilities

Install:

```bash
sudo apt install ldap-utils
```

This provides tools such as:

```text
ldapsearch
```

We will use `ldapsearch` in the next lesson to communicate directly with Active Directory.

Verify:

```bash
ldapsearch -VV
```

At this stage, we only want to confirm that the tool is available.

---

## Step 15 - Verify Installed Packages

You can check whether a command exists using:

```bash
which curl
```

For example:

```bash
which ldapsearch
```

You can also inspect installed packages:

```bash
apt list --installed | grep ldap-utils
```

This is useful when troubleshooting:

> Is the application/tool actually installed?

rather than assuming it is.

---

## Step 16 - Identify the Domain Controller

Before testing integration, record the Domain Controller's:

* hostname
* IP address
* Active Directory domain

For example:

```text
Hostname: DC01
Domain: hospital.local
IP: <your-dc-ip>
```

Do not guess these values.

Verify them from Windows Server.

On the Domain Controller:

```powershell
hostname
```

and:

```powershell
ipconfig
```

---

## Step 17 - Test Ubuntu → Domain Controller Connectivity

From Ubuntu:

```bash
ping -c 4 <domain-controller-ip>
```

This tests basic IP reachability.

For example:

```text
Ubuntu
   │
   │ ICMP
   ▼
Domain Controller
```

If this test fails, there is little value troubleshooting LDAP credentials yet.

First investigate networking.

---

## Step 18 - Test the LDAP Port

Once IP connectivity works:

```bash
nc -zv <domain-controller-ip> 389
```

Expected successful output may resemble:

```text
Connection to <server> 389 port [tcp/ldap] succeeded!
```

Now we have proven something more specific.

```text
Ubuntu
   │
   │ TCP 389
   ▼
Domain Controller
   │
   └── LDAP service reachable
```

This still does not prove that authentication will succeed.

That comes later.

---

## Reachability vs Service Availability

Keep these tests separate.

### Ping

```bash
ping -c 4 <dc-ip>
```

asks:

> Can I reach this host at the IP layer?

### Netcat

```bash
nc -zv <dc-ip> 389
```

asks:

> Can I reach this specific TCP service?

### LDAP Search

```text
ldapsearch ...
```

will later ask:

> Can I actually communicate with the directory service?

These tests move progressively higher through the technology stack.

---

## Step 19 - Test Domain Name Resolution

If DNS is configured to use the Active Directory DNS server, try:

```bash
getent hosts hospital.local
```

and potentially:

```bash
getent hosts dc01.hospital.local
```

If IP connectivity works but internal domain names cannot be resolved, investigate DNS.

This is a common integration issue.

---

## Step 20 - Check System Time

Run:

```bash
timedatectl
```

Look for:

* local time
* time zone
* synchronization status

Time matters in enterprise authentication environments.

Large clock differences between systems can cause authentication problems, particularly when protocols such as Kerberos are involved.

---

## Integration Readiness Checklist

At this point, do not ask:

> Does LDAP authentication work?

yet.

First verify the lower layers.

```text
Ubuntu VM running
       ↓
Resources available
       ↓
IP configured
       ↓
Gateway working
       ↓
DNS working
       ↓
Domain Controller reachable
       ↓
TCP 389 reachable
       ↓
LDAP tools installed
       ↓
Ready for directory integration
```

---

## A Troubleshooting Example

Imagine:

```text
Internet access: YES

ping DC IP: YES

nc DC-IP 389: NO
```

What can we conclude?

We probably do **not** have a general internet problem.

We probably do **not** have complete network isolation from the Domain Controller.

The failure is more specific:

```text
Ubuntu
   │
   X TCP 389
   │
Domain Controller
```

So investigate the service/port layer.

---

## Another Troubleshooting Example

Imagine:

```text
ping DC IP
SUCCESS

getent hosts dc01.hospital.local
FAIL
```

That points toward:

```text
DNS / name resolution
```

rather than basic IP connectivity.

This is why integration engineers test one layer at a time.

---

## What an Implementation Engineer Is Doing Here

Notice that we have not changed Active Directory yet.

Instead, we are examining the environment into which software will be integrated.

We are asking questions such as:

* What OS is running?
* What IP address does the application host have?
* Which DNS server is configured?
* Can it reach the customer infrastructure?
* Are required ports available?
* Are required packages installed?
* Is system time correct?

This type of environmental discovery helps prevent incorrect assumptions during implementation.

---

## Do Not Troubleshoot Credentials Too Early

Suppose an application reports:

```text
Authentication failed
```

It is tempting to immediately reset a password.

But the real failure might be:

```text
Wrong DNS
Blocked TCP 389
Wrong server IP
LDAP service unavailable
Incorrect Base DN
Wrong credentials
```

Therefore troubleshooting should move systematically through the layers.

---

## Our Current Architecture

The environment should now resemble:

```text
Ubuntu Integration Machine
       │
       │ Network available
       │ DNS available
       │ LDAP tools installed
       │ TCP 389 tested
       ▼
Windows Server
       │
       ├── DNS
       └── Active Directory
```

The machines are ready for application-layer integration.

---

## What You Should Understand

Before continuing, make sure you can explain:

**Why do we have Ubuntu?**

It represents an external application or integration system that needs to communicate with existing hospital infrastructure.

**What does `ip addr` tell us?**

It shows network interfaces and IP addresses.

**What does `ip route` tell us?**

It shows how traffic is routed, including the default gateway.

**What does `ping` test?**

Basic IP reachability using ICMP.

**What does `nc -zv <server> 389` test?**

Whether a TCP connection can be established to the LDAP service port.

**What does `ldap-utils` provide?**

Command-line tools for interacting with LDAP services, including `ldapsearch`.

**Why check DNS separately from connectivity?**

Because a host may be reachable by IP while name resolution is failing.

**Why check system time?**

Because enterprise authentication systems may depend on reasonably synchronized clocks.

---

## Checkpoint

Before moving on, confirm that:

* Ubuntu boots successfully
* you can open Terminal
* the hostname is known
* the Ubuntu version is known
* CPU, RAM and disk can be inspected
* Ubuntu has an IP address
* the gateway is known
* external connectivity can be tested
* DNS can be inspected
* `curl` is installed
* `nc` is installed
* `ldapsearch` is installed
* the Domain Controller IP is known
* Ubuntu can test reachability to the Domain Controller
* TCP port 389 can be tested
* system time has been checked

---

## Challenge

Consider this output:

```text
ping 172.16.203.10
SUCCESS

nc -zv 172.16.203.10 389
FAILED
```

Answer:

1. Can Ubuntu reach the Domain Controller at the network layer?
2. Does successful ping prove LDAP works?
3. Which layer appears to have the problem?
4. Should you start changing the LDAP username and password yet?

The answer to question 4 is:

> Not yet.

First determine why the required LDAP TCP port cannot be reached.

---

## Next Step

The Ubuntu integration machine is prepared.

We have verified the operating system, resources, networking, DNS, required tools and the path toward the Domain Controller.

Next, we will move from infrastructure testing to actual directory integration.

Ubuntu will communicate with Active Directory using **LDAP**.

[Continue to LDAP Integration →](08-ldap-integration.md){ .md-button .md-button--primary }

