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
<b> *Network Adapter 3: SERVER LAN*</b><br />
<b> *Network Adapter 4: MONITORING*</b><br />
<b> *Network Adapter 5: GUEST*</b><br />

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
Repeat this configuration for the other interfaces. However, ensure you set the adequate IP addresses and aliases for each of the adapters or you can set them up from the GUI but to do this, we will have to install the other VMs first. 

<h2> Downloading and Installing Kali Box </h2>

This is similar to the installation of the FortiGate. However, make use of this [resource](https://youtu.be/i0j-6iFBozg) as a guide. 

<h2> Downloading and Installing Window's Server 2019 for the Domain Controller </h2> 

Kindly make use of this [resource](https://youtu.be/lwxNGUIEB2A) as a guide.

<h2> Downloading and Installing Splunk </h2>

Kindly make use of this [resource](https://www.youtube.com/watch?v=ia9E4x8iVDk&list=PLDqMNdDvMsRkmtiKcZwbhOz7MeLQE0r3G&index=8) as a guide.

The reference guides are from [Cyberwoxacademy.com](https://cyberwoxacademy.com/building-a-cybersecurity-homelab-for-detection-monitoring/)
