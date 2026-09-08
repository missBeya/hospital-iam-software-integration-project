# 01 - Virtualisation Setup

## Goal

In this lesson, you will create the virtual environment that will host our simulated hospital infrastructure.

By the end, you will have two virtual machines:

- a Windows Server 2022 VM
- an Ubuntu integration VM

These machines will later communicate with each other as if they were separate computers inside a hospital network.

---

## Before You Start

You need a computer capable of running virtual machines.

The host computer can run:

- Windows
- macOS
- Linux

You will also need a virtualisation platform.

Examples include:

- VMware Fusion
- VMware Workstation
- VirtualBox
- Hyper-V
- another hypervisor capable of running the required guest operating systems

!!! note
    This project was originally built using VMware Fusion, but VMware Fusion is **not required**.

    The screenshots and menus you see may differ depending on your virtualisation software.

    What matters is that you understand the architecture and can create virtual machines that communicate with each other.

---

## What Is Virtualisation?

Normally, one physical computer runs one operating system.

Virtualisation allows one physical computer to run several independent computers at the same time.

Your physical computer is called the **host**.

The software that creates and manages virtual machines is called a **hypervisor**.

The operating systems running inside the virtual machines are called **guest operating systems**.

Our environment will look like this:

~~~text
Physical Computer
        │
        └── Hypervisor
                │
                ├── Windows Server 2022 VM
                │
                └── Ubuntu VM
~~~

Each virtual machine gets its own:

- virtual CPU
- memory
- disk
- network adapter
- operating system

To the operating system inside the VM, these virtual resources behave much like physical hardware.

---

## Key Terms

### Host

The physical computer running the virtualisation software.

### Hypervisor

Software that creates and manages virtual machines.

Examples include VMware, VirtualBox, and Hyper-V.

### Virtual Machine

A software-defined computer running inside the host computer.

### Guest Operating System

The operating system installed inside a virtual machine.

In this project our guest operating systems include Windows Server and Ubuntu.

### ISO Image

An ISO is a file containing installation media for an operating system.

It acts like a virtual installation DVD.

---

## Step 1 - Install a Virtualisation Platform

Choose a hypervisor that is compatible with your host operating system.

Install it using the instructions provided by the vendor.

Once installed, confirm that you can create a new virtual machine.

!!! warning "Architecture compatibility"
    Before downloading operating-system images, check that the guest operating system architecture is compatible with your computer and hypervisor.

    For example, x64 and ARM systems may require different installation images or virtualisation support.

---

## Step 2 - Obtain the Operating System Images

You will need installation media for:

### Windows Server 2022

Download a Windows Server 2022 installation ISO from an official Microsoft source.

This server will later provide:

- Active Directory Domain Services
- DNS
- identity management
- authentication
- access control

### Ubuntu

Download a supported Ubuntu LTS installation image from the official Ubuntu source.

Ubuntu will later act as our integration machine.

We will use it to test:

- network connectivity
- DNS
- LDAP
- application integration

---

## Step 3 - Create the Windows Server VM

Create a new virtual machine and attach the Windows Server ISO.

A reasonable starting configuration is:

| Resource | Suggested Configuration |
|---|---|
| CPU | 2 virtual processors |
| Memory | 4-6 GB RAM |
| Disk | 40-60 GB |
| Network | NAT or shared virtual network |

These values are not strict requirements.

You may adjust them depending on the resources available on your host computer.

Do not allocate all of your physical computer's CPU or memory to the VM.

---

## Step 4 - Create the Ubuntu VM

Create a second virtual machine and attach the Ubuntu ISO.

A reasonable starting configuration is:

| Resource | Suggested Configuration |
|---|---|
| CPU | 2 virtual processors |
| Memory | 4 GB RAM |
| Disk | 25-40 GB |
| Network | Same virtual network as Windows Server |

The important part is the network configuration.

Both virtual machines must eventually be able to communicate with each other.

---

## Why Must They Share a Network?

Later, Ubuntu will send requests to Windows Server.

For example:

~~~text
Ubuntu
   │
   │ LDAP request
   │ TCP 389
   ▼
Windows Server
Active Directory
~~~

If the two VMs cannot reach each other over the network, the application integration will fail.

For now, simply ensure that both VMs use the same virtual networking mode.

We will examine IP addresses, subnets, gateways, DNS, and connectivity in the Networking lesson.

---

## Step 5 - Install the Operating Systems

Start both virtual machines and install their operating systems from the ISO images.

At this stage, the objective is simply to reach a working operating system on each VM.

The Windows Server configuration will be covered in the next lesson.

---

## Step 6 - Check Your Environment

You should now have something similar to:

~~~text
Host Computer
        │
        └── Virtualisation Platform
                │
                ├── Windows Server 2022
                │
                └── Ubuntu
~~~

Confirm that:

- both virtual machines start successfully
- Windows Server reaches its login screen
- Ubuntu reaches its login screen
- both VMs have a virtual network adapter
- neither VM consumes all of the host computer's resources

---

## Optional - Create a Snapshot

If your hypervisor supports snapshots, this is a useful time to create one.

A snapshot records the current state of a virtual machine.

If something breaks later, you may be able to return to this clean state instead of reinstalling the operating system.

A useful snapshot name might be:

~~~text
Clean OS Installation
~~~

Snapshots are useful for labs, but they should not be treated as a replacement for proper backups in production environments.

---

## What You Should Understand

Before moving on, make sure you can explain the following in your own words:

**What is the difference between a host and a guest?**

The host is the physical computer.  
The guest is an operating system running inside a virtual machine.

**What does a hypervisor do?**

It creates and manages virtual machines and allocates resources such as CPU, memory, disks, and networking.

**Why are we using two virtual machines?**

Because we want to simulate separate systems communicating across a network, rather than installing everything on one computer.

**Why must Windows Server and Ubuntu be able to communicate?**

Because Ubuntu will later behave like an external system integrating with services hosted by Windows Server.

---

## Challenge

Without looking at the diagram, try to explain this architecture:

~~~text
Host
  ↓
Hypervisor
  ↓
Windows Server + Ubuntu
  ↓
Network communication
  ↓
Software integration
~~~

If you can explain why each layer exists, you understand the foundation of the project.

---

## Next Step


The virtual infrastructure is ready.

Next, we will configure the Windows Server that will become the hospital Domain Controller.

[Continue to Windows Server →](02-windows-server.md){ .md-button .md-button--primary }
