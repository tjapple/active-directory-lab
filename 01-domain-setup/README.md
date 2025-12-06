## 🎯 Goal
Deploy a Windows Server 2022 machine and run Active Directory with OU architecture and domain-joined clients. This will be the backbone for many future projects.


## 🏗️ Architecture and Design 
* VM running Windows Server 2022:
  * NIC1 is operating in NAT mode, which is configured automatically by VirtualBox. This enables access to the Internet. 
  * NIC2 is operating in Internal Network mode. This serves as an isolated AD LAN for the clients. 
* VM running Windows 11 Pro
  * NIC is operating in Internal Network mode, which will connect to the local domain.
  <img width="951" height="514" alt="image" src="https://github.com/user-attachments/assets/55821e4c-1f83-4ad6-afdc-d2f588b9662d" />

 
## ⚙️ Actions
* Promoted server to Domain Controller (via "Add Roles and Features" -> "Install Active Directory Domain Services").
  * Added a new forest called lab.local.
* Created the OU structure:
  * Top-level OUs were created for CorpUsers, CorpComputers, and Servers.
  * OUs were added within these for IT, HR, Sales, Support, Laptops, and Workstations.
  * Users were added with simple password configuration for lab and testing purposes.  
   <img width="285" height="242" alt="image" src="https://github.com/user-attachments/assets/2926d2b1-6816-4e94-9fbc-78ea0018c77c" />
 
* Joined the Windows client to the lab domain:
  * Settings -> System Properties -> Change domain or workgroup -> add the "lab.local" domain.


## 🚧 Problems Encountered
* Many problems occurred involving the NIC settings. I began the lab operating the server in Bridged mode so it would act like another client on my LAN, but this caused many issues later with WSUS and DNS. Changing this to a dual-system NIC and switching the Windows client to also be on the Internal Network solved many problems. 


## 💡 What I learned
* A Domain Controller is a server that hosts the Active Directory Database and handles authentication, authorization, and policy enforcement to control access to resources.
* Active Directory requires clients to use only the DC for DNS. Mixed DNS sources can cause issues.
  * Incorrectly operating the Server with a Bridged NIC highlights some fundamentals of AD: it will break if you mix DNS sources.
* A "forest" is the highest-level container in Active Directory. It contains one or more domains, which all share trust. 
* The DSRM password is the emergency backup account in case AD goes down. 
* I purposefully locked out some users so I could practice unlocking their accounts and resetting the passwords. 
