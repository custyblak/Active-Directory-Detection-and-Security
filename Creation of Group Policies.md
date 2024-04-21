<h2> Creating Baseline GPOs</h2>

In this part of the Lab, we will focus on the configuring and securing our domain controller via Group policies. We will be utilizing guidelines from  [**Sean Metcalf's blog - adsecurity.org**](https://adsecurity.org/?p=3377). 

Log into your Active Directory and navigate to **Tools >> Group Policy Management**. 

<p align="center">
<img src="https://imgur.com/tIXRi0H.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

You would notice the presence of two (2) default policies namely "Default Domain Controller" and "Default Domain Policy". These GPOs are same across newly deployment Active Directory. However, to customize additional settings, it is advisable to resist the temptation of customizing the default GPOs but rather create separate GPOs for major configurations like Workstation, Server and User configurations.

To effectively secure our domain controller, we will create 3 new policies - **"EMPIRE DOMAIN SECURITY BASELINE", "EMPIRE DOMAIN CONTROLLER SECURITY BASELINE" and "EMPIRE PASSWORD POLICY"**.

- **EMPIRE DOMAIN SECURITY BASELINE**: This will contain settings that applies to the entire domain except the password, Account Lockout and Kerberos policies that would be contained in the **EMPIRE PASSWORD POLICY**.
- **EMPIRE DOMAIN CONTROLLER SECURITY BASELINE**: This would contain the User Rights Assignment and Security Options (some)

As we progress, other GPOs will be created inline with their functions.

Given that we are just build the security of our domain controller from the ground up, we will be using the Miscrosoft Server 2016 Security Baseline from the [Microsoft Security Compliance Toolkit v1.0](https://www.microsoft.com/en-us/download/details.aspx?id=55319)  

- Navigate to the site, click on "Downloads" and select the **Windows 10 Version 1607 and Windows Server 2016 Security Baseline.zip** and **PolicyAnalyzer.zip**. Download and unzip them to a directory of your choice.

<p align="center">
<img src="https://imgur.com/iTdBxa.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Next, navigate to this directory - "**C:\Users\Administrator\Downloads\Windows 10 Version 1607 and Windows Server 2016 Security Baseline.zip\Windows-10-RS1-and-Server-2016-Security-Baseline\GPOs**" to copy the GPOs to a different directory of your choice where you could edit them.
- In the newly created folder that contains the copied GPOs, navigate into each of the folders and double-click on the **"gpreport"** to get the name of the GPO.

<p align="center">
<img src="https://imgur.com/w4AX9Lw.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Rename the folder containing the gpreport file for easy identification. Repeat this process for others.

<p align="center">
<img src="https://imgur.com/5gbUgd1.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   

