# Active Directory Home Lab-Windows Server 2022 Windows 10
Hands-on Active Directory lab with Windows Server 2022 and Windows 10 Client. Configured AD DS, DNS, Group Policy, user management, shared folders, and Remote Desktop for IT support practice.

Prerequisites:
Oracle VirtualBox (latest).
Windows Server 2022 ISO (Desktop Experience). https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022
Windows 10 Pro ISO. https://www.microsoft.com/en-us/software-download/windows10
Host with ≥ 8 GB RAM and ≥ 100 GB free disk.

Full Step-by-Step
Step 1. Set Up Virtual Machines
Create two VMs in VirtualBox:

DC1 (Domain Controller) - Windows Server 2022
CL1 (Client) - Windows 10

Check "Skip Unattended Installation" after creating name and selecting the ISO Image
Give each VM enough RAM (4 GB minimum for DC1, 2 GB for CL1).

Once both are created, Go to settings, go to Network and select "Internal Network" for both
Make sure they're both assigned to the same Internal Network!(Labnet)

Step 2. Start up Virtual Machines
DC1:

After starting DC1, you will have to choose the operating system you want to install. 
Select "Windows Server 2022 Standard Evaluation (Desktop Experience)"

Select "Custom: Install Windows Server Operating System only (advanced)"
Click Next.

CL1:

After starting CL1, you will be asked for a product key. Select "I don't have a product key"
Select "Windows 10 Pro"
Select "Custom: Install Windows only (advanced)"
Click next.

Select "I don't have internet" when it asks and continue with "Limited Access"

Step 3. Configure Static IPs

DC1:

Right click on the internet logo next to the volume on the task bar. (Should be a circular symbol)

Select "Open Network & Internet settings", then "Change adapter options"

Right click "Ethernet" and select "Properties"

Select "Internet Protocol Version 4(TCP/IPv4)

Click on "Use the following IP address" and enter "192.168.1.10" for the IP. "255.255.255.0" for the Subnet mask.

Leave "Default Gateway" blank. Repeat IP address for "Preferred DNS"

CL1:

Repeat same steps as above to get to network settings.
Enter "192.168.1.20" for the IP address. 
Same Subnet mask and Same Preferred DNS as DC1
Default Gateway stays blank

Step 4. Rename PCs
DC1 & CL1:
Right click windows logo at bottom left of home screen. Select "System". Click "Rename PC" (DC1 & CL1 for the names)
Both machines will restart

Step 5. Install Active Directory Domain Services on DC1

Open Server Manager (Opens automatically on machine start up)
Click on "2 Add roles and features"

Before you begin: Click next.
Installation type: "Role-based or feature-based installation" Click next
Server Selection: Click next. (Auto selected)
Server Roles: Select "Active Directory Domain Services". Click "Add Features" Click next
Features: Click next
AD DS: Click next
Confirmation: Click Install

After installation is complete, click close.
Open Server Manager, next to "Manage" you'll see a flag with a yellow caution sign. Click that.
Select "Promote this server to a domain controller"

Deployment Configuration: Select "Add a new forest", in "Root domain name" type "mydomain.local"
Domain Controller Options: Create a DRSM password
DNS Options: Click next
Additional Option: Click next
Paths: Click next
Review options: Click next
Prerequisites Check: Click Install

Step 6. Create Organizational Units (OUs) & Users

Open Server Manager. Go to "Tools", Select "Active Directory Users and Computers"
Right click on "mydomain.local" (You will see it twice. Right Click the one in the middle, not on the left panel)
Select New, Organizational Unit, make the name "IT_Department"
Click the dropdown for "mydomain.local" in the left panel. 
Right click on IT_Department, click New, click User
First name will be Ricky. Last name will be Manning. User logon name will be rmanning (First initial of first name, full last name)
Click next
Set password
Uncheck "User must change password at next logon"
Finish

Right click on your new user Ricky Manning
Select Properties then Member Of
Click Add and type Domain Admins
Click Apply

Step 7. Join CL1 to the Domain

On CL1, Right click the Windows logo on the taskbar again and click System (The same way we renamed the PCs)
Scroll down to "Rename this PC (advanced)" and click it.
Click "Change"
Under "Member of", Select "Domain". Here you will type "mydomain.local"
For Username and Password, it will be Username: Administrator and Password: (Password you created at set up)

After CL1 restarts, at the log in screen, select "Other User"
Log in using rmanning

Step 8. Apply Group Policy (GPO)

On DC1, Open Server Manager and go to Tools. Select Group Policy Management.
Click on "Forest: mydomain.local" in the left panel. Click on "Domains". 
This should drop down "mydomain.local" Click on this. Then Right click on it and select "Create a GPO in this domain, and Link it here..."
Name this GPO "PasswordPolicy"
Go to "Linked Group Policy Objects" and you'll see your new GPO. Right click on it and select Edit.
Click Computer Configuration, Policies, Windows Settings, Security Settings, Account Policies, Password Policy.
Click on "Minimum password length", check the box for "Define this policy setting"
Configure the Minimum password length to be 10. Click apply
Configure the Maximum password age to be 60 days.
This will ask you to define the Minimum password age to 30 days. Apply it.
Enable "Password must meet complexity requirements"

Go to CL1. Press Win + R and type "gpupdate /force"

Step 9. Unmount the DC1 ISO (For File Sharing)

Power off DC1
Open Virtual Box on you host machine and go to DC1.
Open Settings, Go to storage.
Under "Controller: SATA", click on your ISO image. It will be something like this "SERVER_EVAL_x64FRE_en-us.iso"
On the right, next to "Optical Drive", click the small disk. Select "Remove disk from virtual drive". Click Ok.
Start up DC1.

Step 10. Turn on Network Discovery and File Sharing
On DC1 & CL1:

Go to File Explorer, click Network. 
You should receive an error, click Ok. It'll then display a message at the top "Network discovery is turned off. Network computers and devices are not visible. Click to change..."
Select "Click to change", "Turn on network discovery and file sharing"
Select "No, make the network that I am connected to a private network"

Step 11. Create a Shared Folder with Permissions

On DC1, Open File Explorer
Click "This PC", then click "Local Disk (C:)"
Right click in the blank area under the folders.  
Select New, Folder, Name it "Shared"

Right click on your new folder. Select Properties. Go to Sharing, Advanced Sharing.
In "Share Name", type "SharedDocs".
Select Permissions. Select Add. Type in "Domain Users". Click Ok.
In the "Permissions for Everyone" box, Check the "Read" and "Change" boxes. (Read/Write Permissions)

On CL1, Press Win + R
Type in "\\DC1\SharedDocs" and Click Ok. You should now be in the folder.

Step 12. Enable Remote Desktop Access

On DC1, Open Server Manager. On the left side, you'll see "Local Server". Click it.
"Remote Desktop" will be disabled. Click it. Select "Allow remote connections to this computer"
Click "Select Users", Click "Add", Type in "Domain Users"

On CL1, Open the search bar on the taskbar. Type in "Remote Desktop Connection"
In the "Computer:" Tab, type in "DC1"
Enter password.
