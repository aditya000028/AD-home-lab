# Active Directory Lab Setup Guide (VMware + Windows Server 2025)

## Overview

This guide walks through setting up a basic Active Directory (AD) lab environment using VMware Workstation and Windows Server 2025. The lab simulates a real-world enterprise network with a Domain Controller and a client machine joined to the domain.

---

## Lab Architecture

- **Domain Controller (DC)**
  - OS: Windows Server 2025
  - Role: Active Directory Domain Services (AD DS) + DNS

- **Client Machine**
  - OS: Windows 11
  - Role: Domain-joined workstation

- **Virtualization Platform**
  - VMware Workstation Pro

---

## Prerequisites

- VMware Workstation installed
- Windows Server 2025 ISO
- Windows 11 ISO
- Minimum system requirements:
  - 8 GB RAM (recommended)
  - 100 GB free disk space

---

## Step 1: Create the Domain Controller VM

1. Open VMware Workstation
2. Click **Create a New Virtual Machine**
3. Select **Typical (recommended)**
4. Choose **Installer disc image file (ISO)** and select Windows Server ISO
5. Configure VM:
   - Name: `DC01`
   - Disk Size: 60 GB
   - Memory: 2–4 GB RAM
7. Finish setup and power on VM

<img width="435" height="417" alt="Screenshot 2026-03-20 183136" src="https://github.com/user-attachments/assets/01943261-5efd-446b-8a22-56d973033412" />

---

## Step 2: Install Windows Server 2025

1. Follow installation wizard
2. Select:
   - **Windows Server 2025 (Desktop Experience)**
3. Complete installation
4. Set Administrator password

<img width="1027" height="773" alt="Screenshot 2026-03-20 185512" src="https://github.com/user-attachments/assets/f720aab4-5719-41f2-a096-6453ba8a9261" />

---

## Step 3: Configure Static IP Address

1. Open:
   - Settings → Network & internet → Ethernet
2. Edit 'IP assignment' setting from 'Automatic (DHCP)' to 'Manual'
3. Select Internet Protocol Version 4 (IPv4)
4. We need to configure a static IP address for the domain controller and not use DHCP. This is because you need to be able to allow your users/clients to always be able to find the domain controller's IP, without DHCP changing it.

    Please configure the following, keeping the static IP in the same subnet. Note that these values may be different than yours:
      - IP Address: `192.168.106.100`
      - Subnet Mask: `255.255.255.0`
      - Default Gateway: `192.168.106.2`
      - Preferred DNS: `192.168.106.100` (same as server)

<img width="1172" height="917" alt="Screenshot 2026-03-20 191625" src="https://github.com/user-attachments/assets/3e9adb1b-aa87-4f3a-8b04-bd5de1cc0c57" />

---

## Step 4: Rename the Server

Note that we cannot change the name of the server once we promote it to the domain controller, so we must rename the server now.

1. Open:
   - Settings → System → About
2. Click **Rename this PC**
3. Set name:
   - `DC01`
4. Restart the server

<img width="1181" height="920" alt="Screenshot 2026-03-20 191831" src="https://github.com/user-attachments/assets/a387569c-ca6c-453a-b84a-4086cecbc518" />

---

## Step 5: Install Active Directory Domain Services

1. Open **Server Manager**
2. Click **Add Roles and Features**
3. Continue through the wizard and select Role-based installation
4. Choose server (DC01)
5. Select 'Active Directory Domain Services'
   - Note that you may get a dialog to install additional features that are requried for AD DS. Please select Add Features
6. Click Next → Install

<img width="1153" height="662" alt="Screenshot 2026-03-20 193201" src="https://github.com/user-attachments/assets/acc85195-37ea-4d24-a4ac-3c78d57ff9a3" />

---

## Step 6: Promote Server to Domain Controller

1. In Server Manager, click:
   - **Promote this server to a domain controller**
2. Select:
   - **Add a new forest**
3. Enter a root domain name. I chose the following:
   - `homelab.local`
4. Set:
   - Directory Services Restore Mode (DSRM) password
5. Continue with defaults
6. Click Install

<img width="1408" height="686" alt="Screenshot 2026-03-20 194341" src="https://github.com/user-attachments/assets/67dc134d-2c78-407d-97be-a5ec66d58987" />

---

## Step 7: Restart and Verify Domain Setup

1. System will reboot automatically
2. Log in using:
   - `HOMELAB\Administrator`
3. Open:
   - Active Directory Users and Computers
4. Verify domain is created

<img width="765" height="538" alt="Screenshot 2026-03-20 195652" src="https://github.com/user-attachments/assets/6a00861a-f2d7-4cf6-8454-b06c29f45d49" />

---

## Step 8: Create Client VM

1. Create another VM in VMware
2. Use Windows 11 ISO
3. Name it `Client01`
4. Create a password for the virtual trusted platform module (TPM) for the VM
5. Configure:
   - RAM: 2–4 GB
   - Disk: 64 GB
6. Complete installation of Windows 11 by following the wizard
7. Optionally install the VMware Tools to improve VM performance. This can be done by right clicking the VM in VMware and then clicking install VMware tools.

<img width="420" height="405" alt="Screenshot 2026-03-22 161215" src="https://github.com/user-attachments/assets/14162200-4778-4d4e-b4e7-f437e9449afa" />

---

## Step 9: Configure Client Network Settings

1. Open network settings on the client machine
2. Similar to Step 3 but with a slight difference, configure networking settings to the following:
   - IP Address: `192.168.106.101`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `192.168.106.2`
   - Preferred DNS: `192.168.106.100`

   This ensures the client is using our previously set up server as our DNS

## Step 10: Join Client to Domain

1. Open:
   - Settings → System → About
2. Click:
   - Domain or workgroup → Change
3. Rename computer name to `Client01`
4. Click:
   - "Domain" in the "Member of" section
5. Enter:
   - `homelab.local`
6. Enter domain admin credentials
7. Restart when prompted

<img width="402" height="460" alt="Screenshot 2026-03-22 162823" src="https://github.com/user-attachments/assets/36b36104-6925-4bb7-b37c-acb232081fa1" />

---

## Step 11: Log into Domain Server Account

1. On login screen:
   - Select **Other user**
2. Enter:
   - `HOMELAB\Administrator` or a domain user
3. Log in successfully

## Key Functionalities & Administrative Capabilities

With Active Directory Domain Services (AD DS) configured, the Domain Administrator gains centralized control over users, devices, and security policies across the network. This enables a wide range of enterprise-level IT operations. Some unlocked operations now include:

- User & Identity Management
  - Create, modify, and delete user accounts
  - Reset passwords and unlock locked accounts
  - Enforce password policies (complexity, expiration)
  - Organize users into groups for role-based access control (RBAC)
  - Manage Organizational Units (OUs) to structure users and departments
- Device & Computer Management
  - Join computers to the domain
  - Manage and organize devices within OUs
- Group Policy Management (GPO)
  - Enforce security policies across all machines, such as password requirements and account lockout policies
  - Configure desktop restrictions, software installation policies
- DNS & Network Services
  - Manage DNS records for domain name resolution
  - Troubleshoot network-related issues affecting domain connectivity
- File Sharing & Access Control
  - Create shared folders on servers
  - Implement least privilege access control

