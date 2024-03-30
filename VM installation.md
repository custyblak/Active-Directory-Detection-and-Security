<h2> Downloading and Installing VMware Workstation </h2>

NB: VirtualBox can also be used for this lab if VMware Workstation is not an option as a result of its paid license. You can download the latest version from [here](https://www.virtualbox.org/wiki/Downloads). However, I will make use VMware and it can be downloaded for [here](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html). </b>

Once downloaded, install it on your PC. Reference this [YouTube](https://youtu.be/gp5eXjWZUBk) video on how to install a VMware workstation on a PC. 

<h2> Downloading and Installing FortiGate VMware image </h2>

Firstly, you will have to register with your email address on the Fortinet support portal this is [link](https://support.fortinet.com/cred/#/sign-up) or sign in if you already have an account registered. 

<p align="center">
<img src="https://imgur.com/Ws9wNSP.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

You will be required to input the captacha code displayed on your screen. Afterwards, an email verification code will be sent to the registered email. Input the code and register a new password. Once this stage has been completed, login and you will seen a dashboard like this. 

NB: Kindly disregard the total products/fortigate numbers, currently using my already created account for this. Yours would be different.

<p align="center">
<img src="https://imgur.com/uAqioQh.png" height="100%" width="80%" alt="Dashboard"/> 
<br />

Next, click on the **Support** tab, navigate to the **VM Images** and click on it. 
<p align="center">
<img src="https://imgur.com/PC4jWYH.png" height="100%" width="80%" alt="Support.VM images"/> 
<br />

This takes you the the download page as seen below. Under the **Select Platform**, click the drop-down and select **VMWare ESXi** and the version of your choice. (*I suggest you select the latest*)

<p align="center">
<img src="https://imgur.com/YzgB96s.png" height="100%" width="80%" alt="VM images"/> 
<br />

Given that this is a new or first deployment, scroll down and select the image file that starts with **New deployment of FortiGate for VMware FGT_VM64-vX.X.X.X-buildXXXX-FORTINET.out.ovf.zip**. Note the end of the image file is ***.ovf.zip**. The X.X.X.X represents the versions and build number which I excluded because a new one could be out at the time you would be viewing this domentation but at the time of this documentation, the image version is a v7
<p align="center">
<img src="https://imgur.com/tvY59Oh.png" height="100%" width="80%" alt="Download the VMWare Image"/> 
<br />

Download and extract the image file to the location of your choice on your PC. Once this has been done, import it into your VMware workstation by clicking on the **Open a Virtual Machine**
<p align="center">
<img src="https://imgur.com/lDo0VkF.png" height="100%" width="80%" alt="Download the VMWare Image"/> 
<br />

Once the above has been done, select the image and click on **Edit Virtual Machine Settings**. You will see alot of Network Adapters but from our diagram, we have only 4 connections to the FortiGate firewall and 1 connection to the internet. In summary, we need 5 network adapters 1 as either a NAT or Bridge and the others as LANs.

<p align="center">
<img src="https://imgur.com/DI8Hpsf.png" height="100%" width="80%" alt="Download the VMWare Image"/> 
<br />

Also from the diagram, we have different subnets. To add those subnet names to the firewall, follow the steps below.
1. Click on the second interface and check the **LAN Segment** button.
2. Click on the **LAN Segments** button
3. Add the name of the LAN segment that you want to assign to each of the network adapters.
   
<p align="center">
<img src="https://imgur.com/VtjJqaI.png" height="100%" width="80%" alt="LAN Segment config"/> 
<br />
   
<b>*Network Adapter 1: NAT or Bridge*</b><br />
<b> *Network Adapter 2: AD LAN*</b><br />
<b> *Network Adapter 3: GUEST LAN*</b><br />

After this has been done, go back to the various network adapters and select each of those LAN segments for each. Save your configuration and now start the virtual machine.
Log into the VM after bootup with the username as **admin** and **no password**. You will be prompted for a new password, input it and type in the following commands to prevent license invalid issues.

#config system ntp<br />
#set ntpsync disable<br />
#set type custom<br />
#end
<p align="center">
<img src="https://imgur.com/ne8gmpf.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<h2> Configuration of the FortiGate interfaces </h2>

<h3> Port 1 (NAT or Bridge) </h3>

So, we will starts with the Port 1 as WAN and this points the first network adapter we have connected as **NAT** or **Bridge**. This interface will be configured as **DHCP** so as to receive IP address from our host PC for internet connectivity. The following command will enable us achieve this.

<b>config system interface</b>: This command allows you to configure any of the interfaces via FortiGate CLI<br />
<b>edit port 1</b>: Just as the name implies, you want to edit port 1.<br />
<b>set mode dhcp</b>: Sets the interface to receive an IP address from the host PC <br />
<b>set role wan</b>: The tells the interface that it is responsible for our internet connectivity.<br />
<b>set alias WAN</b>: This is optionally. However, it helps users to easily identify the use of the interface<br />
<b>set allowaccess http https ssh ping</b>: This tells what services are allowed on this port.<br />
<br>Now use the **show** command to review what you have configured on that interface.

<p align="center">
<img src="https://imgur.com/nPJY74d.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Now, lets configure the second interface and from how we mapped the LAN segments above, the network adapter 2 is assigned to the **AD LAN** and from our diagram, this LAN is assigned to 192.168.10.0/24 subnet. To configure this, simply type in **next** after the output of the **show** command to go back to the interfaces prompt. From there, type in **edit port2** as seen in the snippet below to start the configuration on the interface. 

<p align="center">
<img src="https://imgur.com/6EliO2k.png" height="70%" width="80%" alt="NTP check"/> 
<br />
   
Use the following below: <br />
<b>set mode static</b><br />
<b>set role lan</b><br />
<b>set ip 192.168.10.1/24</b><br />
<b>set alias AD LAN</b><br />
<b>set allowaccess http https ssh ping</b><br />

Notice that the mode for the port 2 is static and this is because we want to assign it an ip address which will act as a gateway for the 192.168.10.0/24 subnet. Now, use the **show** command to review what you configured.<br />
Use the command **get system interface physical** to show the IP addresses configured on each interface. Copy the IP address assigned to the port 1 to your PC browser. 

<p align="center">
<img src="https://imgur.com/VmnRlJE.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Login to configure the remaining interfaces and internet connectivities to the VMs.

<p align="center">
<img src="https://imgur.com/Ut6ldii.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Navigate to the **Netork > Interface** tab to port 2 and 3 respectively. Configure their IP addresses as described on the diagram in the IP/Netmask column, fill in the aliases and roles, specify the respective administrative accesses - port 2: HTTPS, Ping; port 3: Ping. 

<p align="center">
<img src="https://imgur.com/XapzRyw.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<p align="center">
<img src="https://imgur.com/clfMN1B.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Save and now navigate to the **Static Route** still under the Network row. We will create a default route via the port 1 interface for internet connectivity. Click on the **+** sign and add the port 1 interface, leave others as default and then save.

<p align="center">
<img src="https://imgur.com/X1wwy4C.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<p align="center">
<img src="https://imgur.com/lYFF2OA.png" height="70%" width="80%" alt="NTP check"/> 
<br />
   
Next, go to **Policy & Objects** > **Firewall policy**. Click on **Create new** (NB: For the different LANs and WAN interfaces to communicate, we have to create firewall rules or policies that will allow this communication. Without policies, each LAN will be isolated. And don't forget that the PCs within in the diagram will require internet connection and somethings internet access will be granted to our servers for updates. Hence, the need to create rules.)

<p align="center">
<img src="https://imgur.com/t3lEzy5.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<h2> Installing Splunk on Ubuntu VM</h2>

Download the Ubuntu Desktop version [here](https://ubuntu.com/download/desktop) and install with the default settings. Before powering on the machine after installing the VM, enter the Virtual Machine Settings and change the **Network Adapter** setting to **LAN Segment** with **AD LAN** selected.
<p align="center">
<img src="https://imgur.com/OAknXlQ.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Now, lets configure a static IP address for the Splunk Server. To do this, we will have to navigate into the **Netplan** directory using the **cd /etc/netplan/* to modify the configuration file written using YAML and end with the extension .yaml.

In this directory, you will see a file named *01-network-manager-all.yaml* using the *ls* command. NB: If you don't see one, create this exact file using the *touch* command. 

<p align="center">
<img src="https://imgur.com/rBzAEsu.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Modify the content of this file using either VIM or NANO, depending on the one you are most comfortable with. 
using the *sudo vi 01-network-manager-all.yaml*, below is what you will see by default

<p align="center">
<img src="https://imgur.com/NcgzczG.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Next, add the *ethernets* and reference the network adapter name. (NB: To get the network adapter name, open a new tab and simply run the **IP addr** command)

<p align="center">
<img src="https://imgur.com/6d8Hvhc.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<p align="center">
<img src="https://imgur.com/yMNzqFj.png" height="70%" width="80%" alt="NTP check"/> 
<br />

As we are setting a static IP and we do not want to dynamically assign an IP to this network adapter, we'll set *dhcp4* to **no**. Now, we'll specify the Splunk server static IP as outlined in the diagram with the default gateway and name server the firewall's IP address of LAN segment. Use the command: **sudo netplan try** to permanently save the config. Reconfirm if the changes have taken effect using **ip addr**. Now, ping the firewall ip address (192.168.10.1) and from the firewall, ping the server ip address.

<p align="center">
<img src="https://imgur.com/FpOia5j.png" height="70%" width="80%" alt="NTP check"/> 
<br />

Now, let's install splunk on the server. Firstly download the splunk application from the splunk [website](https://www.splunk.com/), 

- click on “Free Splunk“ button, Create an account or login.

- Click on the "Free Trials and Downloads page" on the dashboard, >> "Splunk Enterprise" >> "Get my free trial"

- Select the Linux package and download the .deb file

- Navigate to the downloads directory and open the terminal

<p align="center">
<img src="https://imgur.com/xKRa7ZZ.png" height="70%" width="80%" alt="NTP check"/> 
<br />
   
- Install the file using the *sudo apt install ./<name of the splunk file>. You can use your tab key to auto complete.

<p align="center">
<img src="https://imgur.com/nMJ54ez.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<p align="center">
<img src="https://imgur.com/XVel9uR.png" height="70%" width="80%" alt="NTP check"/> 
<br />

- Switch to Splunk user using *sudo su -splunk*, navigate to */bin* directory  and then use the command **./splunk start** to start the splunk instance. Agree to the license contract and then enter an admin username and password of your choice. At the end of your startup, a URL will be displayed on your commandline. Copy that to your browser and now login

<p align="center">
<img src="https://imgur.com/wRD0sBT.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<h2> Download and install Domain Controller </h2>

Download Window Server 2019 from the Microsoft Evaluation Center [here](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019) and Windows 10 from [here](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise)

- Install the VM on VMware using the default settings. However, select the option of "I will install the operating system later" to avoid *no operating system found* error.

<p align="center">
<img src="https://imgur.com/1QWZVws.png" height="70%" width="80%" alt="NTP check"/> 
<br />

- After the VM have been installed, edit the settings and select the **AD LAN** of the LAN Segment
  
<p align="center">
<img src="https://imgur.com/3fbLeMm.png" height="70%" width="80%" alt="NTP check"/> 
<br />

- Power up the VM, click on **install now** and select the Windows Server 2019 standard Evaluation (Desktop Experience). Accept the License Terms and click Next

- Select Custom Install and continue with the default *Next* prompts
- Input your password and save.
- Once logged in, configure static IP address and gateway then change the hostname.
  - Navigate to **Control Panel > Network and Internet > Network Connections**
  - Input the following IP address configuration based on our diagram.
  - Run ping tests to the gateway ip and internet (*NB: Access to the internet on the domain is not advisable. This will be later blocked on the firewall*)

<p align="center">
<img src="https://i.postimg.cc/xdF3GYjM/AD-ip-address-config.png" height="70%" width="80%" alt="NTP check"/> 
<br />

<p align="center">
<img src="https://imgur.com/x8JLP3k.png" height="70%" width="80%" alt="NTP check"/> 
<br />

- Rename the Domain Controller to **Empire.DC1**

  - Navigate to *File Explorer >> My PC* in the search bar
 
   <p align="center">
   <img src="https://imgur.com/fHMKjyq.png" height="70%" width="80%" alt="NTP check"/> 
   <br />
   
  - Right-click and select properties

   <p align="center">
   <img src="https://imgur.com/dO2tSSg.png" height="70%" width="80%" alt="NTP check"/> 
   <br />

  - Click on *Change Settings*

   <p align="center">
   <img src="https://imgur.com/vmSHFk5.png" height="70%" width="80%" alt="NTP check"/> 
   <br />

  - Input the above name into the Computer description under Computer Name tab and then click on the "change" button to re-enter the same name into the "Computer Name" space

   <p align="center">
   <img src="https://imgur.com/fB7tGXe.png" height="70%" width="80%" alt="NTP check"/> 
   <br />

  - Apply the setting and then select Restart Now

   
<h2> Downloading and Installing Kali Box </h2>

This is similar to the installation of the FortiGate. However, make use of this [resource](https://youtu.be/i0j-6iFBozg) as a guide. 

