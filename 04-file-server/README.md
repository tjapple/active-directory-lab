## 🎯 Goal
Implement file shares with secure NTFS/SMB permissions for each department. Use Group Policy to map the drives and deploy approved software. 

## 🏗️ Architecture and Design 
* Each department has its own shared folder hosted inside the file server.
* SMB and NTFS permissions enforce which departments can access and modify each drive.
* The IT department was granted modify permissions on all drives to reflect a realistic support model. 
* Drive mappings utilize a user-side GPO with security group targeting.
* Software deployment utilizes a computer-side GPO with the software stored on a dedicated share. 

 
## ⚙️ Actions
* Created shared drives and set permissions: 
  * Created a folder on the server for each department in C:\DC1\Shares\<department>
  * Configured SMB and NTFS permissions for each one, allowing only the relevant department to have Modify access.
  * IT was given Modify access for all department folders for support reasons. 
    <img width="457" height="432" alt="image" src="https://github.com/user-attachments/assets/9c3473cb-f3e6-4867-9d8f-a26bd01dfe19" />

  * Created a Drive Mapping GPO linked to the users and set the target on the relevant security group for each department.
    <img width="404" height="305" alt="image" src="https://github.com/user-attachments/assets/e5250d0e-24fd-4bbf-9138-e922cdd18c74" />
* Created a GPO to deploy software:
  * Created a Software share (\\\DC1\Software) so all domain computers could access the permitted applications.
  * Linked the new GPO to CorpComputers and selected the target software to be installed.
  * On Reboot, the Windows client automatically installed the software that was located in the shared folder.
    <img width="882" height="342" alt="image" src="https://github.com/user-attachments/assets/0fd8e36a-ce14-4389-8b4d-48605ffddd80" />


## 🚧 Problems Encountered
* Mapped drives were not appearing on the client machine:
  * Tried searching for the share and manually mapping, and that worked -> permissions were good. 
  * The fix was to set the Drive Map Action to "Replace" instead of Update so the drive mounted on the user login.
* Software Deploy GPO was not pushing the test application to the client.
  * Looked in the GPO and found that the target software was linked to the local server location (C:\DC1\Shares\Software\) instead of the UNC share path (\\\DC1\Software\).
  * Fixed the path and the software installed on reboot. 


## 💡 What I learned
* How to create shared drives and automatically map them to users via GPO.
* How to configure SMB and NTFS permissions for departmental access control.
* How to deploy software to domain computers using a GPO and network share.
* Drive mappings may require the *Replace* action on first deployment to ensure the drive appears at first login. 
