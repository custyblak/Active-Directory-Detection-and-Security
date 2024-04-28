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

<h2> Other Security Controls to implement.</h2>

<h3>Change the default domain Administrator account and passwords</h3>
   It is advisable to rename your default domain Administrator making it slightly more difficult for attackers to guess the account name for brute-force password cracking attempts at least 1x or 2x per year. In addition to this, change the Administrator & KRBTGT passwords should be changed every year & when an AD admin leaves. 

Let's rename the Administrator's account using GPO.
- Navigate to the Group Policy Management Console
- Create a new GPO to identify the desired function.
- Navigate to Computer Configuration >>Windows Settings >>Security Settings >> Local Policies >> Security Options.
- Check the "Define this policy setting" and input any name of your choice.
- Link the GPO to your domain and then enforce it.

<p align="center">
<img src="https://imgur.com/zX492Ce.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

I will leave you to change the passwords of both the Administrator and KRBTGT accounts.

<h3>Set all admin accounts to “sensitive & cannot be delegated”</h3>
This prevents an admin account's credentials from being delegated to other users or services, making it more difficult for attackers to move laterally through the network and compromise additional systems. Also, it enforces the principle of least priviledge because Admin accounts should only be used for administrative tasks, and they should not be used for everyday tasks. 
If you have multiple domain admins, use this powershell cmdlet to effect it.

  <p></p>$admins = Get-ADGroupMember -Identity "Domain Admins"
foreach ($admin in $admins) {
  Set-ADAccountControl -Identity $admin.SamAccountName -AccountNotDelegated $true}</p>

However, you can also effect it from the GUI by navigating to each of the domain admin names, then click on **properties >> Accounts >> Account Options, scroll down and check the "Account is sensitive and cannot be delegated" box**.

<p align="center">
<img src="https://imgur.com/zGFeR0P.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

<h3>Add admin accounts to “Protected User's Group”</h3>
The "Protected Users" group enforces limitations on how credentials are stored on devices. When a domain user in this group logs in, their credentials are not cached locally. Instead, they are obtained directly from the domain controller and this reduces the risk of attackers stealing credentials from local machine caches to gain access via Pass-the-Hash attack.

<p align="center">
<img src="https://imgur.com/LWC6bhN.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

<h3>Configure AppLocker on DCs to only allow authorized applications to run</h3>

This enhances the Security Posture of our DC by limiting applications to only those explicitly allowed, you significantly reduce the attack surface. Hence, helps mitigate the risk of malware compromising your DCs and potentially spreading across your network. Also, with AppLocker in place, it becomes easier to identify and investigate any attempt to run unauthorized applications on the DC. The application execution logs provide a clear audit trail, simplifying forensic analysis in case of suspicious activity.

- Create a new group policy and label it "DC Applocker" Right-click to edit it.
- Navigate to **Computer Configuration >> Windows Setting >> System Services and enable Application Identity** startup mode as **"Automatic"**

<p align="center">
<img src="https://imgur.com/a7omX8f" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Continue to Application Control Policies >> Applocker >> Configure rule enforcement to select the rules you want to enforce.

   <p align="center">
   <img src="https://imgur.com/UJwCIlu.png" height="100%" width="80%" alt="Fortinet Support page"/> 
   <br />

- Right-click on any rule collection and select "Create new rule"

<p align="center">
<img src="https://imgur.com/zw3w1GU.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

- Lets create a sample rule to prevent any installation for .exe or .msi files from the Temp Directory.
   - Skip the default page
   - Select **Deny** under "Action" and **Everyone** under "User or group". Click on **Next**
     
   <p align="center">
   <img src="https://imgur.com/0mRXYJb.png" height="100%" width="80%" alt="Fortinet Support page"/> 
   <br />

   - Since we want to prevent any application in the Temp Directory from running, select the **Path** option. Then, **Next**
 
  <p align="center">
   <img src="https://imgur.com/951A183.png" height="100%" width="80%" alt="Fortinet Support page"/> 
   <br />
     
   - Under the **Path**, click on **Browse Folder**. Select the Temp directory under the C:\Windows\
 
  <p align="center">
   <img src="https://imgur.com/faZuSIf.png" height="100%" width="80%" alt="Fortinet Support page"/> 
   <br />
     
   - Skip the exceptions page and name the rule.

   <p align="center">
   <img src="https://imgur.com/ucqnxgh.png" height="100%" width="80%" alt="Fortinet Support page"/> 
   <br />
      
   - Click on Create.

   <p align="center">
   <img src="https://imgur.com/1kiTX2B.png" height="100%" width="80%" alt="Fortinet Support page"/> 
   <br />

   - Repeat the above for other rule collections.
     

<h3>Block internet access for my DC</h3>

This can be achieved by creating a policy on the firewall to prevent inbound/outbound the domain controller
- Log into the FortiGate firewall and navigate to **Policy & Objects** >> Addresses.
- Add the IP address of the DC and save

 <p align="center">
 <img src="https://imgur.com/DvfdVr1.png" height="100%" width="80%" alt="Fortinet Support page"/> 
 <br />

- Navigate to the **Firewall Policy** and click on **Create New**

 <p align="center">
 <img src="https://imgur.com/4KkhPvs.png" height="100%" width="80%" alt="Fortinet Support page"/> 
 <br />

- Name: Your policy choice name
- Incoming interface: The interface in the same subnet as the DC IP
- Outgoint interface: any
- Source: The newly created IP address of the DC
- Destination: all
- Service: all
- Action: Deny

- Save the policy and move it to the top of the policy table.

 <p align="center">
 <img src="https://imgur.com/mA90ajV.png" height="100%" width="80%" alt="Fortinet Support page"/> 
 <br />

 - Confirm via pings on the DC to the internet

<p align="center">
 <img src="https://imgur.com/sZayBHN.png" height="100%" width="80%" alt="Fortinet Support page"/> 
 <br />
