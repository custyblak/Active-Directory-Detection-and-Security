<h1> Downloading and Installing VMware Workstation </h1>

NB: VirtualBox can also be used for this lab if VMware Workstation is not an option as a result of its paid license. You can download the latest version from [here](https://www.virtualbox.org/wiki/Downloads). However, I will make use VMware and it can be downloaded for [here](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html). </b>

Once downloaded, install it on your PC. Reference this [YouTube](https://youtu.be/gp5eXjWZUBk) video on how to install a VMware workstation on a PC. 

<h1> Downloading and Installing FortiGate VMware image </h1>

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

Once the above has been done, select on the image and click on **Play Virtual Machine**. 
