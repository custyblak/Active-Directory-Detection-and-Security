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

On the Server Manager, Select Tools >> Active Directory Users and Computers
</b>







