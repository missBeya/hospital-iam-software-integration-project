# 03 - Networking

## Goal

In this lesson, you will configure and test the network that connects the Windows Server and Ubuntu virtual machines.

By the end of the lesson, you should understand:

* IP addresses
* subnet masks
* default gateways
* DHCP
* static IP addresses
* NAT
* DNS
* basic connectivity testing

You will also prepare the Windows Server for its future role as a Domain Controller.

---

## Why Networking Matters

Our environment contains separate virtual machines:

```text
Host Computer
        │
        └── Virtual Network
                │
                ├── Windows Server
                │
                └── Ubuntu
```

Later, Ubuntu will need to communicate with services running on Windows Server.

For example:

```text
Ubuntu
   │
   │ LDAP request
   │ TCP 389
   ▼
Windows Server
Active Directory
```

If the network is configured incorrectly, the application may appear to have an authentication or LDAP problem when the real issue is simply connectivity.

This is why networking should be verified before application integration begins.

---

## Key Networking Terms

### IP Address

An IP address identifies a device on a network.

For example:

```text
192.168.100.10
```

Think of it as the network address of a computer.

---

### Subnet Mask

The subnet mask helps determine which devices belong to the same local network.

A common subnet mask is:

```text
255.255.255.0
```

This is also written as:

```text
/24
```

For example:

```text
192.168.100.10/24
192.168.100.20/24
```

normally belong to the same local subnet.

---

### Default Gateway

The default gateway is the device that allows traffic to leave the local network.

For example:

```text
192.168.100.1
```

If a machine needs to communicate with a destination outside its local subnet, it normally sends the traffic to its default gateway.

---

### DNS

DNS stands for **Domain Name System**.

DNS translates names into IP addresses.

For example:

```text
microsoft.com
        ↓
IP address
```

In an Active Directory environment, DNS becomes even more important because computers use DNS to locate services such as Domain Controllers.

---

### DHCP

DHCP stands for **Dynamic Host Configuration Protocol**.

A DHCP server can automatically provide a computer with:

* an IP address
* subnet mask
* default gateway
* DNS configuration

This is convenient for normal client devices.

However, servers often use static addresses because other systems need to know where to find them consistently.

---

### Static IP Address

A static IP address does not change automatically.

A Domain Controller should normally have a predictable address.

For example:

```text
Windows Server
192.168.100.10
```

Other machines can then consistently contact that server.

---

### NAT

NAT stands for **Network Address Translation**.

Many virtualisation platforms use NAT to allow virtual machines to access external networks through the host computer.

A simplified view looks like:

```text
Virtual Machine
      │
      ▼
Virtual NAT Network
      │
      ▼
Host Computer
      │
      ▼
Internet
```

The virtual machine has its own private IP address, while the hypervisor translates traffic when it leaves the virtual network.

---

## Step 1 - Check the Virtual Network

Before configuring IP addresses, verify that both virtual machines use the same virtual networking mode.

Depending on your hypervisor, you may see options such as:

* NAT
* Shared Network
* Bridged
* Host-only

For this project, the important requirement is:

**Windows Server and Ubuntu must be able to communicate with each other.**

!!! note
The exact names of networking modes vary between VMware, VirtualBox, Hyper-V and other hypervisors.

```
Focus on the network architecture rather than a specific product interface.
```

---

## Step 2 - Inspect Windows Server Networking

Open Command Prompt or PowerShell on Windows Server.

Run:

```powershell
ipconfig
```

For more detailed information:

```powershell
ipconfig /all
```

Look for:

* IPv4 Address
* Subnet Mask
* Default Gateway
* DNS Servers
* DHCP Enabled

Example:

```text
IPv4 Address:      192.168.100.20
Subnet Mask:       255.255.255.0
Default Gateway:   192.168.100.1
```

Your values will probably be different.

Do not copy example IP addresses directly into your environment.

---

## Step 3 - Inspect Ubuntu Networking

On Ubuntu, open Terminal.

Run:

```bash
ip addr
```

This displays the network interfaces and their IP addresses.

You can also check the routing table:

```bash
ip route
```

You may see something similar to:

```text
default via 192.168.100.1
```

This identifies the default gateway.

To inspect DNS configuration on many modern Ubuntu systems, use:

```bash
resolvectl status
```

---

## Step 4 - Compare the Two Machines

Record the important information from both machines.

For example:

| Setting | Windows Server | Ubuntu         |
| ------- | -------------- | -------------- |
| IPv4    | 192.168.100.10 | 192.168.100.20 |
| Subnet  | /24            | /24            |
| Gateway | 192.168.100.1  | 192.168.100.1  |
| Network | 192.168.100.0  | 192.168.100.0  |

If both machines are on the same subnet, they should normally be able to communicate directly.

---

## Step 5 - Test the Default Gateway

From Windows Server:

```powershell
ping <gateway-ip>
```

For example:

```powershell
ping 192.168.100.1
```

From Ubuntu:

```bash
ping -c 4 <gateway-ip>
```

If the gateway responds, the local network path is working.

!!! note
A failed ping does not always prove that a device is unreachable.

```
Firewalls can block ICMP traffic while other services continue to work.

Ping is one diagnostic tool, not a complete connectivity test.
```

---

## Step 6 - Test Internet Connectivity

An important troubleshooting technique is to separate **IP connectivity** from **DNS resolution**.

First, test an external IP address.

Windows:

```powershell
ping 8.8.8.8
```

Ubuntu:

```bash
ping -c 4 8.8.8.8
```

If this works, external IP connectivity exists.

Now test a hostname.

Windows:

```powershell
nslookup microsoft.com
```

Ubuntu:

```bash
nslookup microsoft.com
```

If the IP test works but the hostname lookup fails, the problem is likely related to DNS rather than basic network connectivity.

---

## Step 7 - Give the Windows Server a Static IP

A Domain Controller should have a stable network address.

Before changing anything, record the settings received through DHCP:

* current IP address
* subnet mask
* default gateway
* DNS server

Then select an appropriate unused address inside the same subnet.

For example:

```text
IP address:      192.168.100.10
Subnet mask:     255.255.255.0
Default gateway: 192.168.100.1
```

!!! warning
These are example addresses only.

```
Your virtual network may use a completely different range.

Always inspect your existing network before selecting a static IP address.
```

On Windows Server, open:

**Server Manager → Local Server → Ethernet**

Then open the network adapter properties.

Select:

**Internet Protocol Version 4 (TCP/IPv4)**

and configure the chosen static address.

---

## Why Does the Server Need a Static Address?

Imagine Ubuntu is configured to contact:

```text
192.168.100.10
```

If DHCP later changes the Windows Server address to:

```text
192.168.100.35
```

Ubuntu may continue trying to contact the old address.

The integration would fail.

A static IP gives important infrastructure a predictable location.

---

## Step 8 - Verify the Static Configuration

After changing the address, run:

```powershell
ipconfig /all
```

Confirm that:

* the expected IPv4 address appears
* the subnet mask is correct
* the gateway is correct

Then test the gateway again:

```powershell
ping <gateway-ip>
```

And test external connectivity:

```powershell
ping 8.8.8.8
```

---

## Step 9 - Understand DNS in Active Directory

DNS is particularly important in this project.

Normal internet DNS may resolve names such as:

```text
microsoft.com
ubuntu.com
github.com
```

Active Directory DNS will later also resolve internal names such as:

```text
hospital.local
dc01.hospital.local
```

After the server becomes a Domain Controller with DNS installed, domain members should use the Active Directory DNS server so they can locate domain services.

A simplified architecture will eventually look like:

```text
Ubuntu / Client
      │
      │ DNS query
      ▼
Windows Server
Active Directory DNS
      │
      ▼
Domain services
```

We will configure this further during the Active Directory stage.

---

## Step 10 - Test Communication Between the VMs

Now test communication between Windows Server and Ubuntu.

First find Ubuntu's address:

```bash
ip addr
```

From Windows Server:

```powershell
ping <ubuntu-ip>
```

Then find the Windows Server address:

```powershell
ipconfig
```

From Ubuntu:

```bash
ping -c 4 <windows-server-ip>
```

If both machines can reach each other, the basic network path is ready.

---

## A Better Troubleshooting Method

When something does not work, avoid testing everything at once.

Work through the network layer by layer.

### 1. Does the machine have an IP address?

Windows:

```powershell
ipconfig
```

Ubuntu:

```bash
ip addr
```

### 2. Can it reach its gateway?

```text
Machine
   ↓
Gateway
```

### 3. Can it reach another IP address?

```text
Windows Server
      ↓
Ubuntu
```

### 4. Can it reach the internet by IP?

```text
ping 8.8.8.8
```

### 5. Can DNS resolve names?

```text
nslookup microsoft.com
```

### 6. Can the application port be reached?

Later, we may test specific services such as:

```text
LDAP  → TCP 389
LDAPS → TCP 636
SMB   → TCP 445
```

This layered approach helps identify **where** the failure occurs.

---

## Common Problems

| Symptom                                 | Possible Cause                                    |
| --------------------------------------- | ------------------------------------------------- |
| No IP address                           | Network adapter or DHCP problem                   |
| Cannot reach gateway                    | Virtual network or IP configuration problem       |
| Can reach IP but not hostname           | DNS problem                                       |
| Windows and Ubuntu cannot communicate   | Different networks, firewall, or addressing issue |
| Server address changes                  | DHCP still being used                             |
| Internet works but domain services fail | Internal DNS configuration may be incorrect       |
| Ping fails but application works        | ICMP may be blocked by a firewall                 |

---

## DHCP vs Static IP

A useful way to remember the difference:

### DHCP

```text
Device starts
     ↓
Requests configuration
     ↓
DHCP provides IP settings
```

Good for:

* laptops
* desktops
* temporary clients
* general user devices

### Static IP

```text
Administrator chooses address
          ↓
Address remains predictable
```

Commonly used for infrastructure such as:

* servers
* Domain Controllers
* DNS servers
* network appliances

---

## What You Should Understand

Before continuing, make sure you can explain:

**What does an IP address do?**

It identifies a network interface so other systems know where to send traffic.

**What does the subnet mask do?**

It helps determine which portion of an IP address identifies the network and which portion identifies the host.

**What is the default gateway?**

It provides a route toward destinations outside the local subnet.

**What does DHCP do?**

It automatically provides network configuration to clients.

**Why do we give the Domain Controller a static IP?**

Because other systems need a predictable address for services such as DNS and authentication.

**What does DNS do?**

It resolves names into IP addresses and, in Active Directory, helps systems locate domain services.

**What does NAT do?**

It translates network traffic between address spaces, allowing virtual machines on private networks to communicate with external networks through the host or hypervisor.

---

## Checkpoint

Before moving on, confirm that:

* Windows Server has an IP address
* Ubuntu has an IP address
* both machines are on compatible virtual networks
* you know the subnet mask
* you know the default gateway
* Windows Server has a stable IP address
* the machines can test local connectivity
* DNS resolution can be tested
* you understand the difference between DHCP and static addressing

---

## Challenge

Without looking back at the definitions, explain what happens when Ubuntu tries to communicate with the Windows Server.

Use these terms:

* IP address
* subnet
* network adapter
* DNS
* gateway

Then answer this troubleshooting scenario:

```text
ping 8.8.8.8
SUCCESS

nslookup microsoft.com
FAIL
```

Which network service would you investigate first?

**Answer:** DNS.

---

## Next Step

The network foundation is ready.

Next, we will turn Windows Server into the identity system for the simulated hospital by installing and configuring **Active Directory Domain Services**.

[Continue to Active Directory →](04-active-directory.md){ .md-button .md-button--primary }

