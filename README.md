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

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Resources
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in Active Directory
- Join Client-1 to your domain (mydomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create additional users and attempt to log into Client-1 with one of the users, reset passwords, disbale/enable accounts
  
<h2>Deployment and Configuration Steps</h2>
<p>
<h3 align="center">Setup Resources in Azure</h3>

We need to first create the Domain Controller VM (Windows Server 2022) named “DC-1”.


![1](https://github.com/user-attachments/assets/ca24af19-1996-49cb-bef1-54c3defb8bc1)
<p>

![3](https://github.com/user-attachments/assets/899215e3-efc5-4007-a0dc-e0d367c3ee12)
<p>

Next, we are going to create the Client VM (Windows 10) named “Client-1”. We will use the same Resource Group and Vnet that was created in previous steps.

![6](https://github.com/user-attachments/assets/af9ab703-e020-4622-b42d-e9b800650c95)
<p>

Once both of those virtual machines are set up, we need to set the Domain Controller’s NIC Private IP address to be static. We need to go under IP Configurations. 

![9](https://github.com/user-attachments/assets/7928badc-91fa-4027-a18c-5a6222843b19)
<p>
<h3 align="center">Connectivity between the Client and Domain Controller</h3>

<p>
In DC-1, we are going to disable the windows Firewall(for testing connectivity). Next we are gong to set Client-1’s DNS settings to DC-1’s Private IP address. From the Azure Portal, restart Client-1. Login to Client-1. Attempt to ping DC-1’s private IP address. Ensure the ping succeeded. From Client-1, open PowerShell and run ipconfig /all. The output for the DNS settings should show DC-1’s private IP Address.
<p>
Disabling the firewall.

![12](https://github.com/user-attachments/assets/9b398f9d-d55b-407b-8185-440145296b0f)
<p>
Changing the Client-1's DNS setting to DC-1's private IP address. 

![14](https://github.com/user-attachments/assets/4b29fcea-c949-4382-b815-183df4a43d27)

<p>
Restarting Client-1.

![15](https://github.com/user-attachments/assets/45fedf8a-f008-4248-aa6e-2aec2a5a9bd7)

Ping from Client-1. 

![18](https://github.com/user-attachments/assets/de8915a4-7bfc-48fb-a40d-f237670b3bf3)

![19](https://github.com/user-attachments/assets/f9921084-d411-4717-bfd6-f08a2f87c131)
<p>

<h3 align="center">Install Active Directory</h3>

We need to login to DC-1 and install Active Directory Domain Services. Promote as a DC: Setup a new forest as mydomain.com. Restart and then log back into DC-1 as user: mydomain.com\labuser.
<p>
Check Active Directory Domain Service

![20](https://github.com/user-attachments/assets/137d40ee-758f-44be-a36b-c149ef0d26e2)
<p>
Deployment Confirguration. 
<p>
In the upper right click on the flag, and add a new forest, with the root domain name: mydomain.com.

![21](https://github.com/user-attachments/assets/8619a19e-ec12-4ea2-bd7b-ed708fe6cbc5)
<p>

<h3 align="center">Create a Domain Admin user within the domain</h3>
<p>
In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”. Create a new OU named “_ADMINS”. Create a new employee named “Jane Doe” with the username of “jane_admin”. Add jane_admin to the “Domain Admins” Security Group. Log out / close the connection to DC-1 and log back in as “mydomain.com\jane_admin”.
<p>
Create _EMPLOYEES.

![22](https://github.com/user-attachments/assets/7e3185a5-cce2-4fd3-93c9-80cceec7a336)
<p>
Create ADMINS.

![23](https://github.com/user-attachments/assets/9b41f58f-c2bd-4b69-bc6e-c801295bf16b)
<p>
Create a new user, Jane Doe, the user logon name: jane_admin, she will be added to  "Domain Admins". 

![24](https://github.com/user-attachments/assets/5e5aa2fb-c0f5-4540-9bdd-b49f49072ee6)

![25](https://github.com/user-attachments/assets/75b23082-c412-484a-a4b7-ad1a62697cf3)
<p>
Log out and log back on with, mydomain.com\jane_admin.

![26](https://github.com/user-attachments/assets/381e89ec-d4f6-476b-a0ba-57a73165a4da)
<p>
<h3 align="center">Join Client-1 to your domain (mydomain.com)</h3>
<p>
Login to Client-1 as the original local admin (labuser) and join it to the domain (computer will restart). Login to the Domain Controller and verify Client-1 shows up in ADUC. Create a new OU named “_CLIENTS” and drag Client-1 into there
<p>
Login into client-1 and join it to the domain(mydomain.com).

![27](https://github.com/user-attachments/assets/c41e4d3f-945e-4a83-9e9a-43a8411c7e5f)
<p>
Client-1 is in the ADUC.

![28](https://github.com/user-attachments/assets/a3ff530f-c816-45c9-a200-cf8e3f20a08f)
<p>
Create, _CLIENTS and drag Client-1 onto the file. 

![29](https://github.com/user-attachments/assets/06c01222-7102-422f-9538-fab6bebd270c)
<p>
<h3 align="center">Setup Remote Desktop for non-administrative users on Client-1</h3>
<p>
Log into Client-1 as mydomain.com\jane_admin. Open system properties. Click “Remote Desktop”. Allow “domain users” access to remote desktop. You can now log into Client-1 as a normal, non-administrative user now.
<p>

![30](https://github.com/user-attachments/assets/3e2142a6-2e83-42c9-bc33-2b0ca8abf9c2)
<p>
<h3 align="center">Create a bunch of additional users and attempt to log into client-1 with one of the users</h3>
<p>
Login to DC-1 as jane_admin. Open PowerShell_ise as an administrator. Create a new File and paste the contents of the script into it. Run the script and observe the accounts being created. When finished, open ADUC and observe the accounts in the appropriate OU　(_EMPLOYEES). Attempt to log into Client-1 with one of the accounts (take note of the password in the script).
<p>

![32](https://github.com/user-attachments/assets/65fb038e-2c1c-45c3-a6af-d7fe17ca29ad)

Users that were created
![33](https://github.com/user-attachments/assets/c25022b9-4750-403a-877f-0018eca3c93c)


<p>
Logging in with one of the users that was created, all the passwords are, Password1. 

![34](https://github.com/user-attachments/assets/d34ccf2d-c74a-409a-9e07-36522b56a311)
<p>
<h3 align="center">Dealing with Account Lockouts</h3>
<p>
Update and change the settings that are related when accounts are locked out due to invalid passwords in Group Policy Management Editor under Security Setttings, Account Lockout Policy. Choose a random user that was generated to log on. Unlock the user's account in DC-1., Change the password. Disable and enable the users account and attempt to log on. 
<p>
Update the settings. 

![35](https://github.com/user-attachments/assets/871dee73-d848-4bf5-9aec-881df2b32328)
<p>
Enter invalid passwords for multiple attempts. 

![37](https://github.com/user-attachments/assets/e4292778-9194-40cb-ba40-52acd5808fa7)

Account will be locked. 

![38](https://github.com/user-attachments/assets/86eca072-10f8-4f68-8abc-89a4d2a310bf)
<p>
Unlocking the account in Active Directory Users and Computers. 

![39](https://github.com/user-attachments/assets/adcb5a42-2ae6-42a5-8025-8f4da37dc111)
<p>
Change the password for the user.

![40](https://github.com/user-attachments/assets/62c6b94a-e9ab-457d-ac1d-60100f6b390f)
<p>
In Active Directory Users and Computers, disable/enable a user's account. 

![41](https://github.com/user-attachments/assets/f50933b3-80ef-4318-aa2f-20f0003136c2)

User cannot log on until account has been enabled. 

![42](https://github.com/user-attachments/assets/cc2cc282-19d3-4615-bab3-4ff3f2d2cab7)
<p>
Finish! A long set of steps, you can keep messing around and practicing with the users, creating more admins, etc!

<br />
