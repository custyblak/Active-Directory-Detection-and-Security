<h1> Active Directory Detection & Security </h1>

<h2>Description</h2>
The aim of this project is to showcase how a newly deployed Active Directory is vulnerable to certain attacks and how to properly detect and mitigate those attacks.</b>
To achieve this, we will design a simple lab, build and then perform internal pentesting against the domain controller. 

<h2>Network Diagram</h2>

Using [draw.io](https://app.diagrams.net), you can recreate this network diagram. Feel free to modify it per your preference.



<br>NB: At the time of this documentation, this lab was active on [Cyberdefender](https://cyberdefenders.org/blueteam-ctf-challenges/153#tab=details). Hence, I will showcase my approach to the solutions and not explicitly the answers and it can be found [here](https://github.com/custyblak/Network_Forensics_Exercises/blob/main/Cyberdefender/OpenWire%20Challenge/Approach.md)

<h2>Tool used:</h2>

- <b>Wireshark</b>


<h2>Challenge Questions </h2>
  <br />1. By identifying the C2 IP, we can block traffic to and from this IP, helping to contain the breach and prevent further data exfiltration or command execution. Can you provide the IP of the C2 server that communicated with our server?<br />
  <br />2. Initial entry points are critical to trace back the attack vector. What is the port number of the service the adversary exploited?<br />
  <br />3. Following up on the previous question, what is the name of the service found to be vulnerable?<br />
  <br />4. The attacker's infrastructure often involves multiple components. What is the IP of the second C2 server?<br />
  <br />5. Attackers usually leave traces on the disk. What is the name of the reverse shell executable dropped on the server?<br />
  <br />6. What Java class was invoked by the XML file to run the exploit?<br />
  <br />7. To better understand the specific security flaw exploited, can you identify the CVE identifier associated with this vulnerability?<br />
  <br />8. What is the vulnerable Java method and class that allows an attacker to run arbitrary code? (Format: Class.Method)
