<h2> EventID detection for Active Directory.</h2>

One aspect of Active Directory security is protecting Domain Controllers. Another aspect is using logs to identify unusual activities. Therefore, this Lab provides guidance for implementing Splunk-based detection of critical security events within your Active Directory environment. By leveraging EventID monitoring, you can gain real-time insights into potential security incidents and potential security breaches.

We will be implementing monitoring for the baseline DC events outlined by **Sean Metcalf** in his [blog](https://adsecurity.org/?p=3377) post. However, for a more comprehensive monitoring solution in a production environment, I recommend considering Sean Metcalf's outlined [DC EventID](https://view.officeapps.live.com/op/view.aspx?src=https%3A%2F%2Fadsecurity.org%2Fwp-content%2Fuploads%2F2016%2F11%2FDC-Events.xlsx&wdOrigin=BROWSELINK) list available in this Excel spreadsheet.


### Understanding Event IDs

Active Directory security relies on monitoring specific events for potential threats. This table details the baseline Event IDs, significance and their respective Splunk search query:

| EventID | Description | Impact | Splunk Search |
|---|---|---|---|
| 4768 | Kerberos authentication ticket (TGT) requested | Tracks user Kerberos authentication with client/workstation name. Useful for identifying suspicious login attempts. | `index=<domaincontroller> sourcetype=WinEventLog EventCode=4768` |
| 4769 | User requests a Kerberos service ticket | Tracks user resource access requests and potential Kerberoasting attempts. | `index=<domaincontroller> sourcetype=WinEventLog EventCode=4769` |
| 4964 | Custom Special Group logon tracking | Tracks logins for administrative and "users of interest" groups. Useful for monitoring privileged access. | `index=<domaincontroller> sourcetype=WinEventLog EventCode=4964` |
| 4625/4771 | Logon failure | Captures interesting logon failures, including those with bad passwords (ReasonCode=0x18 in EventCode 4771). | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4625 OR EventCode=4771) OR (EventCode=4771 ReasonCode=0x18)` |
| 4765/4766 | SID History | Monitors attempts to modify an account's SID history. Can be suspicious if not actively migrating accounts. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4765 OR EventCode=4766)` |
| 4794 | DSRM account password change attempt | Tracks attempts to change passwords for Domain Service Replication Manager (DSRM) accounts. Unexpected changes might be malicious. | `index=<domaincontroller> sourcetype=WinEventLog EventCode=4794` |
| 4780 | ACLs set on admin accounts | Monitors changes to Access Control Lists (ACLs) for administrative accounts. Unexpected changes could be malicious. | `index=<domaincontroller> sourcetype=WinEventLog EventCode=4780` |
| 4739/643 | Domain Policy changes | Tracks modifications to Domain policies. Unexpected changes could indicate security breaches. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4739 OR EventCode=643)` |
| 4713/617 | Kerberos policy changes | Monitors modifications to Kerberos policies. Unexpected changes could be malicious. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4713 OR EventCode=617)` |
| 4724/628 | Attempt to reset account password | Monitors attempts to reset passwords, particularly for administrative and sensitive accounts. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4724 OR EventCode=628)` |
| 4735/639 | Security-enabled local group changes | Tracks modifications to security-enabled local groups, including membership changes for administrative or sensitive groups. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4735 OR EventCode=639)` |
| 4737/641 | Security-enabled global group changes | Monitors changes to security-enabled global groups, including membership changes for administrative or sensitive groups. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4737 OR EventCode=641)` |
| 4755/659 | Security-enabled universal group changes | Tracks modifications to security-enabled universal groups, including membership changes for administrative or sensitive groups. | `index=<domaincontroller> sourcetype=WinEventLog (EventCode=4755 OR EventCode=659)` |
| 5136 | Directory service object modified | Monitors modifications to Active Directory objects. Can be useful for detecting GPO changes, admin account modifications, and specific user attribute changes. | `index=<your_windows


### EventID 4768




<p align="center">
<img src="https://imgur.com/tIXRi0H.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />



[Next: EventID detection on Splunk](https://github.com/custyblak/Active-Directory-Detection-and-Security/blob/main/EventID%20Detection%20on%20Splunk.md)

