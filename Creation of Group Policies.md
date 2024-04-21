<h2> Creating Baseline GPOs</h2>

In this part of the Lab, we will focus on the configuring and securing our domain controller via Group policies. We will be utilizing guidelines from  [**Sean Metcalf's blog - adsecurity.org**](https://adsecurity.org/?p=3377). 

Log into your Active Directory and navigate to **Tools >> Group Policy Management**. 

<p align="center">
<img src="https://imgur.com/tIXRi0H.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

You would notice the presence of two (2) default policies namely "Default Domain Controller" and "Default Domain Policy". These GPOs are same across newly deployment Active Directory. However, to customize additional settings, it is advisable to resist the temptation of customizing the default GPOs but rather create separate GPOs for major configurations like Workstation, Server and User configurations.

To effectively secure our domain controller, we will create 2 new policies - **"EMPIRE DOMAIN SECURITY BASELINE" and "EMPIRE DOMAIN CONTROLLER SECURITY BASELINE"**.

- **EMPIRE DOMAIN SECURITY BASELINE**: This will contain settings of password, Account Lockout and Kerberos policies that would be contained in the **EMPIRE PASSWORD POLICY**.
- **EMPIRE DOMAIN CONTROLLER SECURITY BASELINE**: This would contain the User Rights Assignment and Security Options (some) and others. 

As we progress, other GPOs will be created inline with their functions.

Given that we are just build the security of our domain controller from the ground up, we will be using the Miscrosoft Server 2016 Security Baseline from the [Microsoft Security Compliance Toolkit v1.0](https://www.microsoft.com/en-us/download/details.aspx?id=55319)  

- Navigate to the site, click on "Downloads" and select the **Windows 10 Version 1607 and Windows Server 2016 Security Baseline.zip** and **PolicyAnalyzer.zip**. Download and unzip them to a directory of your choice.

<p align="center">
<img src="https://imgur.com/iTdBxa.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- On your Group Policy Management Console, create the above baseline policies, right-click on each and let **import settings**. Click on **Next** until you get to the backup path.

<p align="center">
<img src="https://imgur.com/eFoJSq2.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   
- Select the path were you extracted the zip file to and click on **Next**. You will see the various policies within that folder, select the either the "SCM Windows Server 2016 - Domain Controller Baseline" or "SCM Windows 10 and Server 2016 - Domain Security" depending on the name of the newly created GPO.

<p align="center">
<img src="https://imgur.com/x77DnWe.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Click on **Next** and successfully import the baseline policy into the Group Policy Management Console. Repeat the process for the second policy.

<p align="center">
<img src="https://imgur.com/H7adhmX.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Right-click on your domain name (EMPIRE.com) and select **Link to existing GPO** and select the 2 newly imported GPOs.

<p align="center">
<img src="https://imgur.com/7RFQGRH.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Right-click on both of them and select **Enforced**. Next move the **Domain Security Policy** to the top followed by the **Domain Controller Security Policy**

With this, you have successfully configured a security baseline policies recommended by Microsoft for your domain. 
<img src="https://imgur.com/5gbUgd1.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />
   

