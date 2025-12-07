# ⭐ Active Directory Lab
This project builds a fully functional domain environment on a Windows 2022 Server OS, featuring DNS, DHCP, Group Policy, file services, and Windows Update management. A multi-NIC topology was used to represent a realistic environment where the internal domain is isolated. This documentation demonstrates my troubleshooting ability, design decisions, and practical administration skills. 

## 🎯 Purpose
Build a realistic Active Directory environment that demonstrates some of the core skills expected of a System Support Engineer or Junior Systems Administrator. I did not intend for this to be a tutorial or step-by-step walkthrough, but rather a documentation of real work, decisions, and troubleshooting. 

I wanted to gain experience in how organizations actually deploy and manage:
* Domain Controllers
* DNS and DHCP
* Group Policy
* File servers and access control
* Windows Update management 

## 📘 Introduction
Online tutorials can be great, but they may be one of the worst ways to simulate real-world situations. As is often the case with technology, things do not work as intended. You need to figure out why and how to fix issues as they crop up. For this lab, I used a mix of sources to help guide me through: official documentation, AI, and YouTube videos. However, when something failed to work, I made a point of taking full control over the troubleshooting and thought critically about what was happening under the hood. 

Often, there was simply a setting I was unaware of, and in those cases, I needed to just Google or ask AI for the answer. Other times, I just needed to reboot. Sometimes two reboots. The most rewarding moments came when I pulled from my understanding and solved the problem independently. I absolutely love this aspect of working with these complex devices and networks. These are simply *impossible* things: organized light beams orchestrating invisible symphonies we often take for granted. Yet they work, and persistence and focus are rewarded with understanding and skill.

A few notes on the documentation:
* I did not record every logout/login or reboot, but you can be assured that it happened at nearly every step.
* The focus is not on step-by-step instruction, but on decisions and outcomes.
* The troubleshooting sections include many but not all of the dead ends I went down. 

## 🛠️ Skills Demonstrated
* Active Directory Services:
  * Creating and configuring a Windows Server 2022 Domain Controller
  * Designing an OU structure reflecting real departments and machines
  * Creating and using security groups for proper scoping and access control
  * Joining client machines to the domain and placing them in the proper groups
  * Creating/locking/unlocking users and resetting passwords
* Networking (DNS, DHCP, and NIC architecture):
  * Configuring AD-integrated DNS zones with forward and reverse lookups
  * Configuring DHCP scopes, reservations, server options, and dynamic DNS updates
  * Configuring RRAS to route internal client traffic to the external network
  * Troubleshooting multi-NIC DC networking and routing
* Group Policy:
  * Enforcing domain-wide password and account policies
  * Creating department-specific restrictions and branding policies
  * Mapping shared drives to the relevant departments using security filtering
  * Implementing an AppLocker default-deny policy with path rules and security group scoping
  * MSI-based software deployment to domain computers
* File Services:
  * Designing departmental shares with correct SMB and NTFS permissions
  * Implementing least privilege while giving the IT department operational access
* Update Management:
  * GPO-only Windows Update configuration
  * Full WSUS deployment with synchronization, approvals, and reporting
  * Creating separate rings for staged deployments
  * Forcing client detection and troubleshooting update failures
* Troubleshooting and Diagnostics:
  * Reading and filtering Event Viewer logs for relevant information
  * Using gpresult, nslookup, usoclient, wuauclt, and server logs
  * Fixing subtle but critical issues with permissions, DNS, NICs, and routing

## 🗺️ Diagrams and Key Screenshots
- Network Diagram:
  <img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/faf41e16-8d72-4aa8-bd14-a9e63a0d5683" />

  
- Successful DHCP lease:
  
  <img width="539" height="157" alt="image" src="https://github.com/user-attachments/assets/e9600d3d-22f5-4c00-9e81-91aa35c943e4" />
- DNS zones:
  
  <img width="436" height="196" alt="image" src="https://github.com/user-attachments/assets/ad9edb0a-0ff7-4f3b-8fcd-bbd70c7e785c" />
  <img width="439" height="125" alt="image" src="https://github.com/user-attachments/assets/1ab8b18e-cacf-4423-8de7-d13d082288f5" />

- WSUS client registering 100% compliant:
  
  <img width="362" height="260" alt="image" src="https://github.com/user-attachments/assets/3ed74256-3f4c-4360-b789-380b44b643ca" />

## 📁 Lab Sections
* [Active Directory Setup](./01-domain-setup/)
* [DNS & DHCP Configuration](./02-dns-dhcp/)
* [Group Policy Management](./03-gpo/)
* [File Server & Permissions](./04-file-server/)
* [Update Server and WSUS deployment](./05-windows-updates/)


## 🔮 Future Developments
Next steps I am curious to explore:
* Join a Linux machine to the domain (SSSD)
* Security hardening (LAPS, Credential Guard, audit policies, secure administrative workstations)
* Group Policy automation using PowerShell
* Backups (Windows Server Backup, System State Backup, DSRM testing, restoring AD after failure)
* Imaging and deployment using WDS or MDT
