BlueSky Ransomware — Technical Investigation Report

1. Executive Summary

A network-forensics investigation was performed on the BlueSky Ransomware challenge using the provided PCAP file.

The investigation identified suspicious communication between an external IP address (87.96.21.84) and a target host (87.96.21.81).

The captured traffic revealed SQL Server authentication activity, suspicious SQL command execution using xp_cmdshell, HTTP downloads of PowerShell scripts, credential-dumping indicators, host discovery activity, and ransomware-related artifacts.

The ransomware family was identified as BlueSky.

2. Scope and Evidence

Evidence analyzed:

* BlueSkyRansomware.pcap
* HTTP requests and responses
* TDS login and SQL batch packets
* VirusTotal report for javaw.exe
* Challenge-provided ransomware behavioral information

Tools:

* Wireshark
* VirusTotal
* CyberDefenders challenge environment

3. Network Indicators

Indicator	Value	Significance
Suspected attacker IP	87.96.21.84	Source of suspicious activity
Target host	87.96.21.81	Host communicating with the suspected attacker
Protocol	TDS	SQL Server communication
Target account	sa	SQL Server account targeted

The IP addresses and activity were identified from the challenge evidence. Their roles are described in the context of this lab and should not be treated as independently verified attribution.

4. SQL Server Authentication Analysis

Wireshark was used to filter and inspect TDS traffic.

The login packet revealed:

* Client name: EvgtjIcz
* Username: sa
* Server name: 87.96.21.81
* Database name: master

The packet capture exposed authentication information. The actual password is deliberately omitted from this report.

Security significance:

The SQL Server sa account is a highly privileged account. Unauthorized access to it can potentially allow an attacker to execute administrative database operations.

5. Suspicious SQL Command Execution

The investigation identified a SQL batch containing:

EXEC master..xp_cmdshell

What is xp_cmdshell?

xp_cmdshell is a SQL Server extended stored procedure that allows SQL statements to execute operating-system commands through the SQL Server process.

When enabled and accessible to an attacker, it can provide a route from database access to operating-system command execution.

Why is this suspicious?

An attacker with sufficient SQL Server privileges could use this functionality to:

* Execute commands on the database host.
* Download additional tools.
* Perform reconnaissance.
* Establish further access.

6. HTTP Traffic Analysis

Wireshark’s HTTP filter was used to identify suspicious requests.

The investigation revealed requests for:

Requested resource	Observed purpose / context
checking.ps1	PowerShell script activity
del.ps1	Attacker-hosted script download
ichigo-lite.ps1	Additional PowerShell tooling
Invoke-PowerDump.ps1	Credential-dumping tool
Invoke-SMBExec.ps1	SMB-related remote execution tooling
extracted_hosts.txt	Discovered-host information

The second downloaded file’s URL identified in the challenge was:

http://87.96.21.84/del.ps1

The HTTP responses and packet contents provided evidence of the resources being transferred.

7. PowerShell and Credential-Dumping Analysis

The captured PowerShell content referenced:

Invoke-PowerDump.ps1

This script is associated with credential-dumping activity.

The script content also referenced the following output path:

C:\ProgramData\hashes.txt

The file hashes.txt was identified as the saved credential-dump output.

Security significance:

Credential dumping can expose account hashes or other authentication material. Attackers may use compromised credentials to move laterally or access additional systems.

No actual credential values are reproduced in this report.

8. Host Discovery and Lateral Movement Indicators

The captured PowerShell content referenced:

http://87.96.21.84/extracted_hosts.txt

The file extracted_hosts.txt contained discovered-host information according to the challenge evidence.

The investigation also identified Invoke-SMBExec.ps1, which is associated with remote command execution over SMB.

Security significance:

Host discovery and SMB-based remote execution can help an attacker identify additional systems and attempt lateral movement.

The presence of these tools is an indicator of attacker activity; it does not by itself establish that every attempted lateral movement operation succeeded.

9. Ransomware Analysis

The supplied ransomware sample was examined using the challenge’s behavioral-analysis evidence.

The VirusTotal report showed dropped files associated with the ransomware, including:

* # DECRYPT FILES BLUESKY #.html
* # DECRYPT FILES BLUESKY #.txt
* Files with the .bluesky extension

The identified ransomware family was:

BlueSky

The ransom-note artifacts and encrypted-file naming patterns support the identification of BlueSky ransomware activity.

10. Malware Hash and Threat Intelligence

The suspicious executable identified during the investigation was:

Filename: javaw.exe

SHA-256:

3e035f2d7d30869ce53171ef5a0f761bfb9c14d94d9fe6da385e20b8d96dc2fb

The captured VirusTotal report showed:

56 / 63 security vendors flagged the file as malicious.

This detection result supports further investigation and containment. Antivirus detections alone do not establish the complete functionality or origin of a file.

11. Attack Progression

The observed evidence supports the following high-level sequence:

1. Suspicious network activity involving the target host.
2. SQL Server authentication targeting the sa account.
3. SQL command execution using xp_cmdshell.
4. HTTP retrieval of attacker-controlled PowerShell scripts.
5. Credential-dumping and host-discovery activity.
6. SMB-related remote execution tooling.
7. BlueSky ransomware artifacts identified through behavioral analysis.

This sequence is an analytical reconstruction from the available challenge evidence, not a claim that every action was independently confirmed as successful.

12. Defensive Recommendations

1. Restrict SQL Server access to trusted systems and networks.
2. Disable xp_cmdshell when it is not required.
3. Protect privileged SQL Server accounts and use strong authentication.
4. Monitor SQL Server logs for unusual login activity and suspicious SQL batch commands.
5. Monitor PowerShell execution and suspicious script downloads.
6. Investigate unexpected access to C:\ProgramData\.
7. Monitor SMB connections and remote execution behavior.
8. Use endpoint detection and response tools to identify credential dumping.
9. Maintain offline or otherwise isolated backups.
10. Develop detections for suspicious ransomware file creation and mass file modification.

13. Conclusion

This investigation demonstrated how packet-level network evidence can reveal suspicious SQL Server access, operating-system command execution, PowerShell-based tooling, credential-dumping indicators, host discovery, and ransomware-related activity.

The challenge provided practical experience in identifying suspicious TDS and HTTP traffic, inspecting PowerShell content, enriching file hashes with threat intelligence, and documenting findings in a structured incident report.

14. Evidence References

Supporting screenshots are stored in the screenshots/ directory.

The evidence includes:

* SQL Server login packet
* SQL batch containing xp_cmdshell
* HTTP request list
* PowerShell script content
* Credential-dumping script reference
* Discovered-hosts file request
* VirusTotal detection report
* BlueSky ransomware behavioral artifacts

Disclaimer

This report is based on a CyberDefenders educational challenge. Findings are limited to the provided evidence and third-party analysis. No live malware samples, passwords, or sensitive credentials are distributed.
