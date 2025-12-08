## 🎯 Goal
Implement both GPO-based and WSUS-based Windows Update management to simulate how organizations control update timing, approval, and deployment across domain-joined machines. 

## 🏗️ Architecture and Design 
* GPO-only Windows update configuration controls client behavior (timing, restarts, deadlines) but cannot approve or deny updates.
* WSUS server provides centralized control over which updates clients receive and allows IT to approve/decline, stage, and report on update compliance. 
* Pilot and Production rings were created so updates could first be deployed to test devices before being deployed to all clients. 
* Dual-NIC architecture was required on the Domain Controller so WSUS could sync from the internet *and* serve clients on an isolated LAN.
 
## ⚙️ Actions
* Configured a GPO-only Windows Update policy (control over when updates happen, deadlines, and settings, but not specific approve/decline commands):
  * Linked the Windows Update Policy to domain computers.
  * Configured various settings:
    * Automatic Updates.
    * Install during automatic maintenance.
    * No auto-restart when users are logged in.
    * Removed "Pause Update" option.
    * Specified deadlines for updates and restarts.
      
      <img width="359" height="388" alt="image" src="https://github.com/user-attachments/assets/ba1aca24-043f-4439-a6a4-25081e0b6999" />

* Installed, configured, and synced a WSUS server:
  * Added and installed the Windows Server Update Services role to the DC.
  * Synchronized to an upstream Microsoft server to receive updates.
  * Selected some relevant products I would need for my Windows 11 and Windows Server 2022 machines and chose the classifications of updates.
  * Set a sync schedule and started the initial synchronization.
  * Added a Pilot and a Production computer group to push updates out in controlled deployments.
  * Added the Windows 11 client machine to the Pilot ring. 
* Linked and registered clients to the WSUS server:
  * Created a WSUS Client GPO and linked it to CorpComputers.
  * Enabled "Specify intranet Microsoft update service location" and added the WSUS port http://DC1:8530
    <img width="545" height="354" alt="image" src="https://github.com/user-attachments/assets/ae85f2f6-e582-4843-8bc8-08d9d4746e6f" />

  * Started the Windows Update service scan to look for and register with the server: *usoclient StartScan; usoclient StartDownload; usoclient StartInstall*
  * After client registered with the server, I selected and approved updates and applied them to the target ring (Pilot).
    <img width="391" height="277" alt="image" src="https://github.com/user-attachments/assets/60f50cde-2c1b-4eb7-9278-f7e4c9244d13" />

* Verification:
  * The WSUS server now shows the status of the client as "Updates installed/not applicable: 124 - 100%". This indicates the client successfully scanned the server and is now 100% compliant.
    
    <img width="392" height="253" alt="image" src="https://github.com/user-attachments/assets/7c8451ef-2cd2-47f8-8669-506a38ba21c3" />



## 🚧 Problems Encountered
* GPO-only Windows Update:
  * I was still able to access and change update settings on the client machine (despite disabling this in the GPO).
  * Tried rebooting -> ❌
  * Really didn't know what the problem could be. A few minutes later, I just tried rebooting again -> ✅
    * It turns out that Windows Update GPOs sometimes require 2 reboots...
* WSUS:
  * The server would not synchronize with the upstream Windows Update server.
    * Reboot -> ❌
    * Fiddled around with NIC settings and ensured DHCP and DNS were properly configured -> ❌
    * After digging around, I learned I needed to give some security groups full NTFS permissions on the WSUS content folder (NETWORK SERVICE, IIS_IUSRS, and APPPool\\\WsusPool)
      * Ran *iisreset -> net stop wsusservice -> net start wsusservice* -> ✅
          <img width="578" height="274" alt="image" src="https://github.com/user-attachments/assets/db4c6024-ca40-48a2-95ab-2b0a78fc410b" />

  * The client would not register with the server:
    * Ran gpupdate and *gpresult /r /scope computer* to ensure the WSUS GPO was applying.
    * Tested that the client could reach http://DC1:8530
    * Restarted the scan with *net stop wuauserv; net start wuauserv; usoclient StartScan* -> ❌
    * Ran *wuauclt /reportnow* and *usoclient.exe scaninstallwait* -> ❌
    * Reboot -> ❌
    * Enabled "Do not connect to any Windows Update Internet locations" -> ❌
    * Changed the Windows Server VM to use a dual-NIC system to isolate the internal network -> ✅


## 💡 What I learned
* GPO-only policies are not ideal. They may be used in small organizations, but they do not allow fine-grained control over update timing, content, reporting, or deployment.
* WSUS servers can download updates, store them locally, allow IT to approve/decline updates, allow staged deployment rings, give reporting visibility, and many more optional control features.
  * Clients then download updates from the server instead of directly from Microsoft. 
* Though WSUS drives the updates and controls, it still needs a GPO to connect everything together (server location, update frequency, maintenance windows, behavior settings).
* WSUS needs one NIC that talks to the internet and another NIC that talks exclusively to domain clients (to maintain an isolated LAN).
* Update rings are used to test patches safely, protect production systems, and stage updates gradually.
* Learned multiple methods to verify WSUS client communication: checking the registry, forcing scans, and reviewing Windows Update event logs. 
