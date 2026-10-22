# Active Directory Domain in Azure

Independent project: domain controller and domain-joined client running on Azure VMs.

## What was done
- Deployed a Windows Server 2022 domain controller on Azure
- Deployed a Windows 10 client VM in the same Virtual Network
- Configured static private IP and DNS on the domain controller
- Installed Active Directory Domain Services and promoted a new forest (`mydomain.com`)
- Created Organizational Units, admin and standard user accounts
- Domain-joined the client machine
- Configured Remote Desktop access for domain users
- Used PowerShell to generate additional user accounts
- Documented the full process including connectivity checks and verification steps

## Purpose
A personal practice lab for configuring a small Windows domain and verifying connectivity, DNS, user accounts and access. Not a production domain design.

## Tools & technologies used
- Microsoft Azure (Virtual Machines, Virtual Network, Network Watcher)
- Windows Server 2022 (Active Directory Domain Services)
- Windows 10
- Remote Desktop
- PowerShell
- Azure Portal

## AI assistance
None used for the core configuration or primary documentation.

## External script attribution
User-generation script adapted from:  
[https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

---

<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo" width="200"/>
</p>

## 1. Setup Resources in Azure

![Create Domain Controller VM](https://github.com/LawrenceDavy/configure-ad/assets/24421979/226632af-8eed-470f-995d-a331042e6b75)

- Create the Domain Controller VM (Windows Server 2022) named `DC-1`.

![Note Resource Group and VNet](https://github.com/LawrenceDavy/configure-ad/assets/24421979/e3fe660f-8131-4559-92d7-60ac24f550a3)

- Take note of the Resource Group and Virtual Network (VNet) that are created at this time.

![Set static private IP](https://github.com/LawrenceDavy/configure-ad/assets/24421979/04ddaef7-71d6-427d-87a4-7be93a959f85)

- Set the Domain Controller’s NIC Private IP address to static.

![Create Client VM](https://github.com/LawrenceDavy/configure-ad/assets/24421979/ea9a783f-b1e6-4dbb-a3e9-acdf42bcd15b)

- Create the Client VM (Windows 10) named `Client-1`. Use the same Resource Group and VNet that were created for `DC-1`.

![Verify same VNet](https://github.com/LawrenceDavy/configure-ad/assets/24421979/986eafbd-7ebd-4482-aabb-8d34216dd45e)

- Confirm both VMs are in the same VNet by checking the topology with Network Watcher.

---

## 2. Ensure Connectivity between the Client and Domain Controller

![Login to Client-1](https://github.com/LawrenceDavy/configure-ad/assets/24421979/f38d145e-b676-428c-8703-e2c94fa42573)

- Log into `Client-1` with Remote Desktop.

![Enable ICMPv4](https://github.com/LawrenceDavy/configure-ad/assets/24421979/c5e34753-de22-4a10-b8ac-97437af16a73)
![Firewall rule](https://github.com/LawrenceDavy/configure-ad/assets/24421979/4a2ee6a5-5fa8-4c87-a386-63b1690e8899)

- Log into the Domain Controller and enable ICMPv4 on the local Windows Firewall.

![Ping test](https://github.com/LawrenceDavy/configure-ad/assets/24421979/a455ac4b-1ca4-4d85-b75d-162c502c0304)

- From `Client-1`, verify that it can ping the private IP address of `DC-1`.

---

## 3. Install Active Directory

![Install AD DS](https://github.com/LawrenceDavy/configure-ad/assets/24421979/b5a921a2-7f66-42c9-a8a9-c281e05ed92c)
![AD DS installation](https://github.com/LawrenceDavy/configure-ad/assets/24421979/e2dc4ddf-48b2-4041-ae32-3833811ebe77)

- Log into `DC-1` and install Active Directory Domain Services.

![Promote to DC](https://github.com/LawrenceDavy/configure-ad/assets/24421979/71274a7a-83fa-458c-b31a-39e1ae46aead)

- Promote the server as a domain controller and create a new forest: `mydomain.com`.

![Log in as domain admin](https://github.com/LawrenceDavy/configure-ad/assets/24421979/40940c7d-3423-404a-8560-118fb16ead12)

- Restart the server, then log back into `DC-1` as `mydomain.com\{username}`.

---

## 4. Create an Admin and Normal User Account in AD

![Create EMPLOYEES OU](https://github.com/LawrenceDavy/configure-ad/assets/24421979/cfcd85df-71f0-47ef-a72b-4f9735085db8)

- In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called `_EMPLOYEES`.

![Create ADMINS OU](https://github.com/LawrenceDavy/configure-ad/assets/24421979/7c93fd5e-48e2-4171-bd37-d4fcad0d94f1)

- Create a new OU named `_ADMINS`.

![Create Jane Doe account](https://github.com/LawrenceDavy/configure-ad/assets/24421979/05a4d4f5-4fd8-41c5-8ffe-efac935d8e6c)

- Create a new user named “Jane Doe” with the username `jane_admin`.

![Add to Domain Admins](https://github.com/LawrenceDavy/configure-ad/assets/24421979/72b9a248-d366-41c6-a211-8e55426769ff)

- Add `jane_admin` to the Domain Admins security group.

![Log in as jane_admin](https://github.com/LawrenceDavy/configure-ad/assets/24421979/9905aac2-cef6-4f6b-b552-2d995596dd7b)

- Log out of the Remote Desktop session to `DC-1` and log back in as `mydomain.com\jane_admin`.  
  As good practice, avoid using the root administrator account for day-to-day administrative tasks.

---

## 5. Join Client-1 to the Domain (mydomain.com)

![Set DNS on Client-1](https://github.com/LawrenceDavy/configure-ad/assets/24421979/12e989ea-5ac7-4afc-b42e-8cb74f593dba)

- From the Azure Portal, set `Client-1`’s DNS settings to the private IP address of the domain controller.
- Restart `Client-1`.

![Join domain](https://github.com/LawrenceDavy/configure-ad/assets/24421979/e0073945-31f3-4221-bd74-1bab62d61c2d)

- Log into `Client-1` (Remote Desktop) as the original local administrator (`labuser`) and join it to the domain. The computer will restart.

![Verify in ADUC](https://github.com/LawrenceDavy/configure-ad/assets/24421979/b534da6d-6775-418e-9436-61e7dcb12194)

- Log into the Domain Controller and verify that `Client-1` appears in Active Directory Users and Computers inside the Computers container.

---

## 6. Setup Remote Desktop for Non-Administrative Users on Client-1

![System properties](https://github.com/LawrenceDavy/configure-ad/assets/24421979/a3bf9ad5-3c7f-4640-afe9-b81b1f043de3)

- Log into `Client-1` as `mydomain.com\jane_admin` and open System Properties.

![Allow domain users](https://github.com/LawrenceDavy/configure-ad/assets/24421979/a3f3c502-1368-4f0f-943d-07f3ca441614)

- Enable Remote Desktop and allow Domain Users access.  
  You can now log into `Client-1` as a normal, non-administrative domain user.

---

## 7. Create Additional Users with PowerShell and Test Login

![PowerShell ISE](https://github.com/LawrenceDavy/configure-ad/assets/24421979/f99e411e-70d8-44e9-88db-d53d003341c4)

- Log into `DC-1` as `jane_admin`.
- Open PowerShell ISE as Administrator.
- Create a new file and paste the contents of the user-generation script (see attribution at the top of this README).
- Run the script and observe the accounts being created.

![Accounts created](https://github.com/LawrenceDavy/configure-ad/assets/24421979/f6a38ccb-6eaa-418a-9547-383b7911d61e)

- When finished, open Active Directory Users and Computers and confirm the new accounts appear in the appropriate OU.

![Test login](https://github.com/LawrenceDavy/configure-ad/assets/24421979/6eb39a09-43d3-4976-962d-2ad89aa597ed)

- Log into `Client-1` with one of the newly created accounts (note the password generated by the script).

---

## Lessons Learned
- Always set the domain controller’s private IP to static before promoting it to avoid DNS issues after restart.
- Point client DNS to the domain controller before attempting a domain join.
- Using a dedicated domain admin account (`jane_admin`) instead of the built-in administrator is better operational practice.
- Network Watcher is useful for quickly confirming that VMs share the same VNet.

---

**Last verified:** August 2026
