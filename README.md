# Bluesky-ransomware-investigation
BlueSky Ransomware network forensics investigation using Wireshark, SQL Server TDS analysis, PowerShell command analysis, HTTP traffic inspection, and VirusTotal threat intelligence.
BlueSky Ransomware — Network Forensics Investigation

Project Overview

This repository documents my investigation of the BlueSky Ransomware challenge on CyberDefenders.

The investigation focuses on identifying suspicious network activity, analyzing Microsoft SQL Server traffic, discovering attacker command execution, examining PowerShell downloads, and identifying ransomware-related artifacts.

The primary network evidence was analyzed using Wireshark. A suspicious executable was also investigated using VirusTotal.

Objectives

* Identify the suspected attacker and targeted host.
* Investigate SQL Server authentication activity.
* Identify suspicious command execution through SQL Server.
* Analyze attacker-hosted PowerShell downloads.
* Identify credential-dumping activity and related files.
* Investigate ransomware artifacts and identify the ransomware family.

Tools Used

* Wireshark — PCAP and protocol analysis
* VirusTotal — File hash and behavioral intelligence
* CyberDefenders — BlueSky Ransomware challenge
* Windows — Supporting analysis environment

Key Findings

Category	Finding
Suspected attacker IP	87.96.21.84
Target host IP	87.96.21.81
Targeted SQL account	sa
Database protocol	TDS (Microsoft SQL Server)
Suspicious SQL command	xp_cmdshell
PowerShell script	del.ps1
Credential-dumping script	Invoke-PowerDump.ps1
Credential output file	hashes.txt
Discovered-hosts file	extracted_hosts.txt
Ransomware family	BlueSky
Ransom-note artifacts	#DECRYPT FILES BLUESKY #.html and .txt
Suspicious executable	javaw.exe
SHA-256	3e035f2d7d30869ce53171ef5a0f761bfb9c14d94d9fe6da385e20b8d96dc2fb

Investigation Summary

1. Network reconnaissance

Identified suspicious activity involving 87.96.21.84 and the target host 87.96.21.81.

2. SQL Server authentication

Analyzed TDS login traffic and identified the targeted SQL Server account as sa. Credentials visible in the lab traffic have intentionally been excluded from this repository.

3. Suspicious command execution

Identified SQL batch activity containing:

EXEC master..xp_cmdshell

This SQL Server extended stored procedure can execute operating-system commands through SQL Server when enabled and appropriately privileged.

4. PowerShell downloads

HTTP analysis revealed requests for multiple scripts, including:

* checking.ps1
* del.ps1
* ichigo-lite.ps1
* Invoke-PowerDump.ps1
* Invoke-SMBExec.ps1

The second file-download URL identified in the challenge was:

http://87.96.21.84/del.ps1

5. Credential dumping and host discovery

Identified Invoke-PowerDump.ps1 as the credential-dumping script.

The network evidence also referenced:

* hashes.txt — credential-dump output file
* extracted_hosts.txt — discovered-host information

6. Ransomware investigation

The supplied ransomware sample’s analysis showed ransom-note artifacts containing the name DECRYPT FILES BLUESKY.

The identified ransomware family was BlueSky.

7. Threat intelligence

The executable javaw.exe was searched by SHA-256 on VirusTotal.

The captured VirusTotal report showed 56 out of 63 security vendors detecting the file as malicious. This is third-party threat-intelligence evidence, not independent proof of every behavior attributed to the sample.

Skills Demonstrated

* Network traffic and PCAP analysis
* TDS and SQL Server protocol investigation
* Suspicious SQL command identification
* HTTP request and response analysis
* PowerShell script investigation
* Credential-dumping indicator identification
* Host reconnaissance analysis
* Malware hash enrichment
* Ransomware artifact identification
* Evidence-based incident reporting

Evidence

Screenshots supporting the investigation are available in the screenshots/ directory.

Conclusion

The investigation identified suspicious activity involving SQL Server authentication, command execution through xp_cmdshell, PowerShell-based tooling, credential-dumping indicators, host discovery, and BlueSky ransomware artifacts.

This project demonstrates a practical network-forensics workflow for investigating a multi-stage intrusion using packet-level evidence and threat-intelligence enrichment.

Disclaimer

This project was completed for educational and defensive cybersecurity training using the CyberDefenders challenge.

No live malware samples, passwords, or sensitive credentials are included in this repository.
