<h2> Installation of Active Directory features, addition of users and computers </h2>

To begin, log into the server, navigate to the "Manage" tab and click on "Add Roles & Features" then "Next" till you get to the Server Roles menu. Don't change the default settings.

<p align="center">
<img src="https://imgur.com/gt23in5.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   
Select Active Directory Domain Services >> Select “Add Features“ >> Next until the "install" button is ungreyed. Click on it and after the installation, click on "close"

<p align="center">
<img src="https://imgur.com/FBs1E5b.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Next, click on the caution sign >> Promote to Domain Controller

<p align="center">
<img src="https://postimg.cc/GHmHSnZk.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Select "Add a new forest", specify the name of the domain you want to use. In this Lab, we will make use of "EMPIRE.com"

<p align="center">
<img src="https://postimg.cc/SnQ7rPG5.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Click "Next" and then set a password. Continue with "Next" till the prerequisite test is passed. Then click on "Install".

<p align="center">
<img src="https://postimg.cc/XXzphz3m.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   
Afterwards, the VM will reboot. Once up, log back in to add new users

On the Server Manager, Select Tools >> Active Directory Users and Computers, Enter the "Users" Organisational Unit (OU) and click on the User object, add the details of the user and a password.

<p align="center">
<img src="https://imgur.com/bMr9T9x.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

In a production environment, You have to tick the button "User must change password at next logon" but for this lab, we will un-check that button. After this, create a second user following the same process.

<p align="center">
<img src="https://imgur.com/Ra9WC4a.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Now, let's add a workstation to the Domain

Log into the Win10_Enp1 workstation and configure the preferred DNS server to point to the domain controller IP address and the second one, that of the firewall.

<p align="center">
<img src="https://imgur.com/u0uJw1C.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Navigate to the path "Control Panel\System and Security\System", click on "change settings" >> change >> select the Domain option and enter the name of the lab domain.

<p align="center">
<img src="https://imgur.com/b4Mr0M4.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   
Enter the username: Administrator and password of your domain. Afterwards, click "Restart now"

<p align="center">
<img src="https://imgur.com/7csvsfo.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
</b>

Sign in with any of the usernames earlier created. Repeat the process for the second workstation.

<h2> Installation of Sysmon and Splunk's Universal Forwarder on the Domain Controller </h2>

Follow the steps to download and install sysmon

- Download the sysmon application from https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon and the configuration file onto your domain controller server.

<p align="center">
<img src="https://imgur.com/EHKZ9pw.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
- Extract the sysmon application zip file to a destination of your choice.
- Navigate to this github repository by [Olaf Hartong](github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)
- Click on "Raw"
- Right-click and select "save as". Save the .xml file with a name of your choice to the sysmon folder that contains the application.
- Open Powershell as an admin and navigate to the directory where the files are.

<p align="center">
<img src="https://imgur.com/j4xHt9g.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

<p align="center">
<img src="https://imgur.com/F0YV4dx.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
- Install the sysmon driver and service using the command .\Sysmon64.exe -i .\Sysmonconfig.xml. NB: Use your tab key to auto-complete the command.

<p align="center">
<img src="https://imgur.com/kvhGDlv.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />


