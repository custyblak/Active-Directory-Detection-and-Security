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
- Verify that the Sysmon service is running by navigating to the Services, look for Sysmon service. Next you can navigate to the Event Viewer >> Applications and Services logs >> Microsoft >> Windows >> Sysmon >> Operational

<p align="center">
<img src="https://imgur.com/kvhGDlv.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

<h2> Installation of Splunk's Universal Forwarder on the domain controller. </h2>

- Sign into your Splunk account, navigate to the Products >> Free Trials & Downloads >> Universal Forwarder.
- Select the operating system, select the 64 bit option and click on "Download Now"

<p align="center">
<img src="https://imgur.com/sYpa0tR.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   
- Start the installation of the application. Check the license agreement box and click on "Next"
  
<p align="center">
<img src="https://imgur.com/S3vya4S.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Type in the username of your splunk instance and leave the password as randomly generated.
- Skip the part of the deployment server because for this Lab, there aren't
- Under the Receiving indexer, input the IP address of your splunk server and the default port 9997

<p align="center">
<img src="https://imgur.com/Ij8K174.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Click on "Next" and then "Install"

After the installation, we will have to configure the log parameters that will be sent to Splunk. To do this, 

- Open Notepad as an administrator, copy and paste the following below into the notepad 
  
[WinEventLog://Application]

index = DomainController

disabled = false

[WinEventLog://Security]

index = DomainController

disabled = false

[WinEventLog://System]

index = DomainController

disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]

index = DomainController

disabled = false

renderXml = true

source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

*NB: On the Splunk, an index called DomainController will be created to accommodate these log parameters.*

- Save the file to this folder - **C:\Program Files\SplunkUniversalForwarder\etc\system\local** as *inputs.conf*
- Close and exit the folder.
- Change the Logon as user from "NT SERVICE\SplunkForwarder" to "Local System Account". So as to allow permissions to collect all necessary logs.

<p align="center">
<img src="https://imgur.com/aZU6ah3.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br /> 
   
- Restart the SplunkForwarder service under Services. Ensure you open services as an administrator. Always restart the service anytime you modify the inputs.conf file.

<p align="center">
<img src="https://imgur.com/SLEpKWu.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Next, log into your Splunk dashboard, goto settings >> indexes >> New index.
- Add the name of the index we configured on the inputs.conf file, then save the changes

<p align="center">
<img src="https://imgur.com/Sn3DaMw.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

<p align="center">
<img src="https://imgur.com/IipTzii.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Next, configure the spluk to receive the logs on the default port 9997. To do this, follow the steps below.

- Under settings, click on **Forwarding & Receiving**
- Under "Receiving Data", click on "Add new". Input the default port 9997 and save.

<p align="center">
<img src="https://imgur.com/lGYjQ04.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

Now, we should see event on the splunk dashboard. Navigate to "Apps" >> "Search & Reporting", enter the index=DomainController and hit "OK"

<p align="center">
<img src="https://imgur.com/XxIzipB.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
