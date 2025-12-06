## 🎯 Goal
Use Group Policy to centrally manage user and computer settings in the domain, such as passwords, desktop settings, restrictions, and application control.

## 🏗️ Architecture and Design 
* A Password Policy was set at the domain level (in the Default Domain Policy).
* Branding and Restrictions policies were separated into their own GPOs.
* Security groups were created per department and used to selectively apply GPOs (instead of relying on Block Inheritance).
* AppLocker was linked to CorpComputers and enabled to lock down untrusted app sources. 

 
## ⚙️ Actions
* Edited the Default Domain Policy to add password configurations:
  * Sets the lockout threshold, complexity requirements, lockout policy, and more.
    <img width="424" height="146" alt="image" src="https://github.com/user-attachments/assets/e149613f-bf25-4a24-984e-0787e10cfef8" />

* Created security groups for each department OU so I could apply GPOs more selectively.
* Created a company-wide Desktop Branding GPO:
  * Created a shared folder on the Domain Controller called Wallpapers and set NTFS and SMB permissions to read-only.
  * The GPO is linked to CorpUsers, and it sets the wallpaper, enables password-protected screensavers, and sets the screensaver timeout. 
* Created a Restriction GPO for non-IT users:
  * Disables control panel access, certain system settings, task manager, and command prompt.
  * The GPO is linked to CorpUsers, but applied to all department security groups *except* IT.
    <img width="425" height="295" alt="image" src="https://github.com/user-attachments/assets/53bf8038-36cd-42e4-b335-8e83d0434380" />

* Created a GPO that utilizes AppLocker executable default-deny rules:
  * Created a software share on the Domain Controller as a location to house permitted software.
  * In the GPO's Applocker settings, I allowed executables to run from Windows, Program Files, and the Software share. All other locations are implicitly blocked. 
  * Verification: colorcpl.exe, opened from C:\Windows\System32 successfully runs, but if the same file is opened from the Desktop, it is blocked.
    
    <img width="674" height="320" alt="image" src="https://github.com/user-attachments/assets/5f8f026e-f8a6-436b-afc8-c54b2b97b8fe" />
    
    <img width="329" height="227" alt="image" src="https://github.com/user-attachments/assets/aa564954-0763-4c14-8bfb-93ce05997636" />


## 🚧 Problems Encountered
* How to selectively apply GPOs to certain groups:
  * "Block Inheritance" worked, but seemed a very non-elegant solution, and had the potential to cause future headaches.
  * Tried to just link the GPO under each individual department OU, but this became messy later in the lab as I ran into the same selectivity problem.
  * Finally realized I can create security groups to refine which users or computers within the OU actually apply the GPO. The GPO is linked to the OU, but Security Filtering lets me target specific groups without using Block Inheritance. 
* Getting app restriction to work:
  * Applied the Software Restriction Policy GPO -> ❌
  * Switched the restriction policy to use Applocker -> ❌
  * Moved the GPO to apply to computers instead of users -> ❌
  * Rebooted -> ❌
  * After investigating the properties of the Windows client in the AD Users and Groups tool, I saw that it lists "Domain Computers" but not "Authenticated Users" as a group member.
    * So, I went back to the GPO and tried adding "Domain Computers" to the Security Filtering list -> ✅


## 💡 What I learned
* SRP is deprecated in Windows 11; AppLocker should be used instead.
* Application control should be scoped to *computers*, not to users.
* The Default Domain Policy is enforced domain-wide, and it will apply even if blocked. This is why setting password and account policies here is good practice. 
* It is not a good idea to heavily edit the Default Domain Policy. Keep it clean and only edit for account and password settings. 
* Use security groups to apply GPOs selectively, instead of relying on Block Inheritance.
* It is necessary to think carefully about where the GPO should apply. Whether to Users or Computers, and to which groups.
* Practice with unlocking accounts and changing passwords via Active Directory Users and Groups.
