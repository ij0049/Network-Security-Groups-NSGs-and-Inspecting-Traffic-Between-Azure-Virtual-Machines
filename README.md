<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this project, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create some sample file shares with various permissions
- Attempt to access file shares as a normal user
- Create an “ACCOUNTANTS” Security Group, assign permissions, an test access


<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/xaQboo7.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
</p>
<p>
Creating Sample File Shares with Various Permissions

1. Connecting to DC-1 as Domain Admin:

I logged into DC-1 using my domain admin credentials:
Username: mydomain.com\jane_admin
Password: Cyberlab123!

2. Creating the Folders:

![Image 1-29-25 at 12 01 PM](https://github.com/user-attachments/assets/4447108a-356c-4cc1-bf1f-74a47ce74c6c)

On DC-1, I navigated to the C:\ drive and created the following four folders:

read-access,
write-access,
no-access,
accounting

3. Configuring Folder Permissions and Sharing:

![Image 1-29-25 at 12 05 PM](https://github.com/user-attachments/assets/8649cfc1-699f-4c32-9bc7-07f495b15213)

Folder: read-access
I right-clicked the read-access folder, selected Properties, and went to the Sharing tab.
I clicked Advanced Sharing, checked Share this folder, and set the share name as read-access.
I clicked Permissions and added the Domain Users group.
I set their permissions to Read only.
I applied the changes and clicked OK.

![Image 1-29-25 at 12 13 PM](https://github.com/user-attachments/assets/9deea49d-a673-4441-b58e-3431a7b4b9ea)

Folder: write-access
For the write-access folder, I repeated the same steps as above but:
Set the share name as write-access.
Granted the Domain Users group Full Control, which includes both Read and Write permissions.

![Image 1-29-25 at 12 15 PM](https://github.com/user-attachments/assets/4bde8177-2edd-4ad8-8b10-4c4e77fa7a0c)

Folder: no-access
For the no-access folder:
I set the share name as no-access.
Instead of adding Domain Users, I added the Domain Admins group.
I gave the Domain Admins group Full Control permissions.
This ensured that only administrators could access this folder, while regular users had no access.

4. Verifying the Setup:

After setting up the shares, I tested the permissions by connecting to Client-1.

Connecting to Client-1 as a Normal User

5. Logging into Client-1:

On Client-1, I logged in as a standard domain user, for example:
Username: mydomain\someuser
Password: (default password or one set previously).

6. Testing the File Shares:

I opened File Explorer and connected to DC-1 using its network path (e.g., \\DC-1).
I tested access to each folder:
read-access: I could view files but not modify them.
write-access: I could both view and modify files.
no-access: I couldn’t access this folder at all, receiving a permissions error.
</p>
<br />

<p>

![Image 1-29-25 at 12 19 PM](https://github.com/user-attachments/assets/9900e8ac-e2ed-4090-b437-bb1baafaa23e)

</p>
<p>
Testing File Share Access as a Normal User

1. Logging into Client-1:

I logged into Client-1 as a normal domain user, for example:
Username: mydomain\someuser
Password: (default password or one I set previously).

2. Accessing the Shared Folders:

I opened Run (Windows + R), typed \\dc-1, and pressed Enter. This opened a window displaying all the shared folders on DC-1. I then attempted to access each folder:

Folder: read-access
I was able to open this folder and view its contents.
However, when I tried to create or modify files, I received an error saying I didn’t have permission to perform that action.
Observation: This made sense because the permissions for read-access were set to Read only for the Domain Users group.

Folder: write-access
I was able to open this folder and view its contents.
I could also create new files, modify existing ones, and delete files.
Observation: This matched the permissions set, as the Domain Users group had both Read and Write access to this folder.

Folder: no-access
When I tried to open this folder, I received an error stating that I didn’t have permission to access it.
Observation: This also made sense because the Domain Admins group was the only one granted access, and I was logged in as a regular user.


</p>
<br />

<p>
<img src="https://i.imgur.com/3lp5Hh6.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
</p>
<p>
Creating and Testing Access with the "ACCOUNTANTS" Security Group
1. Creating the "ACCOUNTANTS" Security Group on DC-1:

I logged into DC-1 as the domain admin (mydomain.com\jane_admin) and opened Active Directory Users and Computers (ADUC).

I navigated to the _EMPLOYEES OU.
Right-clicked and selected New > Group.
Named the group ACCOUNTANTS, set it as a Security Group, and clicked OK.
2. Assigning Permissions to the "accounting" Folder:

Next, I configured the accounting folder’s permissions:

![Image 1-29-25 at 12 50 PM](https://github.com/user-attachments/assets/ce514411-a0ef-4c2d-a196-c65a9f9892a7)

I navigated to C:\accounting on DC-1.
Right-clicked the folder, selected Properties, and went to the Sharing tab.
Clicked Advanced Sharing, enabled sharing, and set the share name to accounting.
Clicked Permissions, added the ACCOUNTANTS group, and gave it Full Control (Read/Write) permissions.
Applied all changes and closed the settings.
3. Testing Access as a Normal User:

On Client-1, I logged in as a normal user, for example:
Username: mydomain\someuser
Password: (user’s password).

![Image 1-29-25 at 12 52 PM](https://github.com/user-attachments/assets/6b29ffc3-f861-4003-ad57-96085d60a4de)

I opened Run (Windows + R), typed \\DC-1, and pressed Enter.
When I tried to access the accounting folder, I received a permissions error stating I didn’t have access.
Observation: This made sense because someuser wasn’t a member of the ACCOUNTANTS security group yet.

4. Adding the User to the "ACCOUNTANTS" Security Group:

I returned to DC-1:

![Image 1-29-25 at 12 54 PM](https://github.com/user-attachments/assets/c7015ba8-e09e-4a9a-9165-7b1fdfaff3de)

Opened ADUC and navigated to the _EMPLOYEES OU.
Located the ACCOUNTANTS group, right-clicked it, and selected Properties.
Went to the Members tab and clicked Add.
Added someuser to the group and clicked OK.
This added the user to the ACCOUNTANTS security group.

5. Retesting Access as Someuser:

![Image 1-29-25 at 12 56 PM](https://github.com/user-attachments/assets/e411bcc1-cb34-4ebc-9e63-f6b29319f4b4)

I logged back into Client-1 as someuser and retried accessing the accounting folder:

Opened Run (Windows + R), typed \\DC-1, and pressed Enter.
This time, I was able to open the accounting folder and create, modify, and delete files within it.
Observation: The access worked now because someuser was added to the ACCOUNTANTS group, which had the necessary permissions.
</p>
<br />
