<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>1. Setup Resources in Azure</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/226632af-8eed-470f-995d-a331042e6b75)
- Create the Domain Controller VM (Windows Server 2022) named “DC-1”

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/e3fe660f-8131-4559-92d7-60ac24f550a3)
- Take note of the Resource Group and Virtual Network (Vnet) that get created at this time

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/04ddaef7-71d6-427d-87a4-7be93a959f85)
- Set Domain Controller’s NIC Private IP address to be static

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/ea9a783f-b1e6-4dbb-a3e9-acdf42bcd15b)
- Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created for "DC-1"

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/986eafbd-7ebd-4482-aabb-8d34216dd45e)
- Ensure that both VMs are in the same Vnet by checking the topology with Network Watcher

<br>
<br>

<h2>2. Ensure Connectivity between the client and Domain Controller</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/f38d145e-b676-428c-8703-e2c94fa42573)
- Login into Client-1 with Remote Desktop

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/c5e34753-de22-4a10-b8ac-97437af16a73)
![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/4a2ee6a5-5fa8-4c87-a386-63b1690e8899)
- Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/a455ac4b-1ca4-4d85-b75d-162c502c0304)
- Check back at Client-1 to see if can ping to DC-1's private IP address

<br>
<br>

<h2>3. Install Active Directory</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/b5a921a2-7f66-42c9-a8a9-c281e05ed92c)
![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/e2dc4ddf-48b2-4041-ae32-3833811ebe77)
- Login to DC-1 and install Active Directory Domain Services

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/71274a7a-83fa-458c-b31a-39e1ae46aead)
- Promote as a DC: Setup a new forest as mydomain.com

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/40940c7d-3423-404a-8560-118fb16ead12)
- Restart and then log back into DC-1 as user: mydomain.com\\{username}

<br>
<br>

<h2>4. Create an Admin and Normal User Account in AD</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/cfcd85df-71f0-47ef-a72b-4f9735085db8)
- In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/7c93fd5e-48e2-4171-bd37-d4fcad0d94f1)
- Create a new OU named “_ADMINS”

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/05a4d4f5-4fd8-41c5-8ffe-efac935d8e6c)
- Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/72b9a248-d366-41c6-a211-8e55426769ff)
- Add jane_admin to the “Domain Admins” Security Group

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/9905aac2-cef6-4f6b-b552-2d995596dd7b)
- Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
  - As good practice we should not use our root account for administrative tasks, instead use User jane_admin as your admin account from now on

<br>
<br>

<h2>5. Join Client-1 to your domain (mydomain.com)</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/12e989ea-5ac7-4afc-b42e-8cb74f593dba)
- From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
- From the Azure Portal, restart Client-1

<hr>
<br>

  
![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/e0073945-31f3-4221-bd74-1bab62d61c2d)
- Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/b534da6d-6775-418e-9436-61e7dcb12194)
- Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain

<br>
<br>

<h2>6. Setup Remote Desktop for non-administrative users on Client-1</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/a3bf9ad5-3c7f-4640-afe9-b81b1f043de3)
- Log into Client-1 as mydomain.com\jane_admin and open system properties

<hr>
<br>


![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/a3f3c502-1368-4f0f-943d-07f3ca441614)
- Click “Remote Desktop”
- Allow “domain users” access to remote desktop
- You can now log into Client-1 as a normal, non-administrative user now

<hr>
<br>


<h2>7. Create a bunch of additional users and attempt to log into client-1 with one of the users</h2>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/f99e411e-70d8-44e9-88db-d53d003341c4)
- Login to DC-1 as jane_admin
- Open PowerShell_ise as an administrator
- Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
- Run the script and observe the accounts being created

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/f6a38ccb-6eaa-418a-9547-383b7911d61e)
- When finished, open ADUC and observe the accounts in the appropriate OU

<hr>
<br>

![image](https://github.com/LawrenceDavy/configure-ad/assets/24421979/6eb39a09-43d3-4976-962d-2ad89aa597ed)
- Log into Client-1 with one of the accounts (take note of the password in the script)

