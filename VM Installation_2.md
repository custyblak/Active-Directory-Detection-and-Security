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







