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
| 5136 | Directory service object modified | Monitors modifications to Active Directory objects. Can be useful for detecting GPO changes, admin account modifications, and specific user attribute changes. | `index=<domaincontroller> sourcetype=WinEventLog EventCode=5136` |


### EventID 4768 - Kerberos authentication ticket (TGT) requested

This event is captured when a user logs into his workstation using his domain username and password, the workstation then contacts the local domain controller to request a TGT. If the credentials are valid, the DC issues a TGT to the user's machine. Event ID 4768 gets logged on the DC, indicating this TGT request.

#### Security Significance

A large number of these events in a short timeframe, particularly for unusual workstations, could indicate:
Brute-Force Attacks: Hackers might be attempting to crack passwords through repeated login attempts.
Spoofed Login Attempts: Attackers might be trying to impersonate legitimate users by logging in from unauthorized devices

#### Monitoring and Analysis:

Correlate with Login Failures: Investigate if the TGT request coincides with failed login attempts (Event ID 4625/4771). This could indicate brute-force attacks.
Identify Workstation: Analyze the workstation name associated with the event. Unexpected or unknown workstations warrant further investigation.
Monitor for Spikes: A sudden surge in TGT requests, especially from unrecognized workstations, could be a sign of malicious activity.

This Splunk search query can be used. 

**index="domaincontroller" sourcetype="WinEventLog:Security" EventCode=4768 | bin _time span=1m | table _time, Account_Name, Client_Address**

<p align="center">
<img src="https://imgur.com/bcKobfu.png" height="100%" width="80%" alt="Fortinet Support page"/> 
<br />

### EventID 4769 - User requests a Kerberos service ticket

This lets you monitor the granting of service tickets for accessing specific network resources. Service tickets are obtained whenever a user or computer accesses a server on the network.

### Security Implications:

By itself, a single Event ID 4769 isn't necessarily suspicious.
However, it's a valuable data point for security monitoring, especially when analyzed alongside other logs. Here's why:
- Lateral Movement: Hackers who have compromised a legitimate user account might use stolen credentials to request service tickets for other resources within your network. A surge in service ticket requests, particularly from compromised accounts, could be a sign of lateral movement.
- Kerberoasting Attempt: Attackers might attempt to steal user password hashes through a brute-force attack on service tickets. While they wouldn't necessarily gain access through this method, a significant increase in service ticket requests, especially for uncommon services, could warrant investigation.

### Recommendations:

Monitor Event ID 4769 in conjunction with other security logs, such as successful login attempts (Event ID 4768) and failed login attempts.
Investigate a sudden increase in service ticket requests, especially:
-  From compromised user accounts (identified through other security measures).
-  For uncommon services that don't have typical user activity.
-  Implement strong password policies and multi-factor authentication (MFA) to make it more difficult for attackers to exploit stolen credentials.

### EventID 4764 - Custom Special Group logon tracking
This event indicates that a user account has been added to, removed from, or had its membership modified within a security group.

### Security Implications:

By itself, a single Event ID 4964 doesn't necessarily indicate a security threat.
When analyzed in conjunction with other logs, it's valuable for security monitoring, especially for the following reasons:
- Least Privilege Violation: If a user is granted excessive permissions by being added to a group with unnecessary privileges, it could be a security risk.
- Lateral Movement: Hackers who have compromised a user account might add themselves to powerful security groups to gain unauthorized access within your network.
- Accidental Misconfiguration: Inadvertently adding a user to the wrong group could grant them unintended permissions, potentially exposing sensitive data.

### Recommendations:

Monitor Event ID 4964 alongside other security logs, such as user login attempts (Event ID 4624) and permission changes (Event ID 4715).
Investigate changes that seem suspicious, such as:
- User accounts being added to powerful groups without justification.
- Frequent group membership changes for a particular user.
- Group membership changes occurring outside of typical administrative windows.
  
Implement strong access controls and the principle of least privilege to minimize the risk of unauthorized access.

### EventID 4625/4771 - Logon failure

Event ID 4625: This event signifies a failed user account login attempt.
Event ID 4771: Kerberos Preauthentication Failure

This event indicates a failed login attempt using Kerberos authentication. It typically occurs when a user's Kerberos Ticket Granting Ticket (TGT) is invalid or unusable. This TGT is obtained during a successful login (Event ID 4768) and allows users to request service tickets for accessing network resources.

Reasons for failure might include:
- Incorrect password entered during initial login attempt.
- Compromised credentials being used.
- Network connectivity issues preventing proper Kerberos ticket exchange.
  
### Security Significance:
This becomes crucial for security monitoring, especially in large numbers or specific patterns like:
- Brute-Force Attacks: A surge in failed login attempts, particularly with different usernames and from unknown source IPs, could indicate a brute-force attack attempting to crack passwords.
- Spoofed Login Attempts: Failed logins originating from unusual locations or unrecognized devices might suggest someone is trying to access your network with stolen credentials.
- Account Lockouts: Repeated failed login attempts can trigger account lockouts, potentially impacting legitimate users.
  
### Recommendations:

Monitor both Event IDs 4625 and 4771 in conjunction with successful login attempts (Event ID 4624) and password changes. Investigate patterns of failed login attempts, including:
- High frequency of failures from a single source IP.
- Failed attempts for non-existent usernames.
- Login attempts outside of typical working hours.
Implement strong password policies (e.g., minimum password length, complexity requirements) and multi-factor authentication (MFA) to make it harder for attackers to succeed.

### EventID 4765/4766 - SID History

This event indicates that a new Security Identifier (SID) has been successfully added to a user account's SID history. SIDs are unique identifiers assigned to user accounts, groups, and computer accounts within a domain, which plays a crucial role of access control by ensuring the right users have access to the appropriate resources.

There are legitimate reasons for adding a new SID to a user account's history, such as:
- Migrating a user account from another domain.
- Renaming a domain or performing a domain trust migration.

Event ID 4766: SID History Addition Failed

This event signifies that an attempt to add a new SID to a user account's SID history failed.

### Security Implications:

Given that a user account's SID history tracks all SIDs ever associated with that account, it's essential to monitor them for potential security risks:
- Unauthorized SID History Modification: Attackers might try to manipulate a user account's SID history to gain unauthorized access or escalate privileges. A failed attempt to add a SID could be a sign of such an attack.


### Recommendations:

Monitor both Event IDs 4765 and 4766 alongside other security logs, particularly those related to user account modifications such as EventIDs related to  Account Management (4715 - 4729) and Password Management (4739 - 4741)
- Investigate failed SID history additions (Event ID 4766) to understand the cause.
- If you suspect a malicious attempt, take steps to secure your domain, such as:
  - Reviewing domain controller logs for suspicious activity.
  - Verifying the legitimacy of any recent user account modifications.

### EventID 4794 - DSRM account password change attempt
This signifies a successful attempt to set the Directory Services Restore Mode (DSRM) administrator password. DSRM is a special mode within Windows Server that allows administrators to access and restore Active Directory in case of corruption or failure. To access DSRM, you need a dedicated DSRM administrator password, separate from regular domain administrator credentials.

### Security Significance:

It's crucial to monitor this event for the following reasons:
- Legitimate Maintenance: IT administrators might routinely change the DSRM password for security best practices.
- Potential Malicious Activity: If the password change was unauthorized, it could be a sign of an attacker attempting to gain control over your Active Directory. An attacker with DSRM access could potentially:
  - Restore deleted user accounts or modify existing ones.
  - Damage or manipulate critical Active Directory data.
  - Disrupt access to essential network resources.

### Recommendations:

- Monitor Event ID 4794 alongside other security logs, such as user login attempts and privileged account activity.
- Investigate unexpected DSRM password changes, especially if they occur outside of typical administrative windows or from unrecognized sources.
- Implement strong password policies and multi-factor authentication (MFA) for the DSRM administrator account to make it more difficult for attackers to gain unauthorized access.
- Restrict access to tools or commands that can modify the DSRM password to authorized personnel only.

### EventID 4780 - ACLs set on admin accounts

This signifies that the permissions on a member of the Domain Admins or Administrators group have been modified. ACLs define which users or groups have specific permissions (read, write, modify) to access resources.

### Security Implications:

It's valuable for security monitoring, especially when analyzed alongside other logs, for the following reasons:
- Unauthorized ACL Modification: If someone besides authorized administrators modifies the permissions of these powerful accounts, it could be a security breach. Granting excessive permissions or removing essential ones could compromise the security of your Active Directory.
- Accidental Misconfiguration: Inadvertently modifying permissions on these accounts could lead to unintended consequences, potentially impacting legitimate administrative tasks.

### Recommendations:

- Monitor Event ID 4780 alongside other security logs, such as user account modifications (e.g., Event ID 4715) and privileged account activity.
- Investigate unexpected ACL modifications, especially if they occur outside of typical administrative windows or from unrecognized sources.
- Implement the principle of least privilege, granting users only the minimum permissions required for their roles. This minimizes the risk of damage caused by compromised accounts.
- Consider using tools that can monitor and alert on changes to sensitive groups like Domain Admins and Administrators.

### EventID 4739/643 - Domain Policy changes

The combination of Event ID 4739 and 643 in Active Directory provides information about user account password changes.

### Security Considerations:

- Password Hygiene: Frequent password changes can be a sign of good security practices, but excessively frequent changes might indicate brute-force attacks where attackers cycle through many password guesses.
- Compromised Credentials: If a user's account is compromised, attackers might change the password to maintain access. Monitoring password changes can help identify such situations.
- Unauthorized Access Attempts: Password changes originating from unusual locations or unrecognized devices could suggest unauthorized attempts to access an account.

### Recommendations:

- Implement strong password policies that enforce password complexity requirements and minimum password length.
- Encourage users to avoid predictable passwords and change them periodically (but not too frequently).
- Enable multi-factor authentication (MFA) for all user accounts to add an extra layer of security beyond passwords.
- Monitor password change events (Event ID 4739) alongside other security logs, such as failed login attempts (Event ID 4625) and suspicious account activity.

