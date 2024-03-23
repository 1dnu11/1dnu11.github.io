---
title: Windows Credential Dumping
date: 2024-03-23
description: Learning about Windows hashes, configuration files and credential dumping
tags:
- Article
- Windows
- Hashing
- Credentials
- SAM
- LSA
- LSASS
- LM
- MD4
- DES
- NTLM
---

# Windows Password Hashes
Windows stores hashes user account passwords locally in the **SAM (Security Accounts Manager)** database. Authentication and verification of user credentials is facilitated by the **LSA (Local Security Authority)**.

## SAM Database
SAM is a database file that manages user accounts and passwords. All passwords stored in SAM are hashed. This database **cannot be copied** while the operating system is running. Windows NT keeps the SAM db file locked. Attackrs tipically use *in-memory* techniques to dump SAM hashes from the **LSASS** process.

In modern versions, the SAM db is encrypted with a syskey.

*Note: In order to access and interact with the LSASS process, elevated privileges are required.*

## LM (LanMan)
**LM** is the old stuff, but let's give it a shot first. This protocol hashing process can be broken down in the following steps:
- Passwords is broken into **two seve-character** chunks.
- All characters are then converted into **uppercase**.
- Each chunk is then hashed separately with the **DES algorithm**.

LM hashing is considered to be a **weak** protocol and can easily be cracked, primarily because the hash does not include *salts*, making it brute-force and rainbow table attacks affective against it.

## NTLM (NTHash)
With **NTLM**, when a user account is created, it is encrypted using the **MD4 hashing* algorithm, and the original password is then trashed.

NTLM improves upon LM:
- Does not split the hash
- Case sensitive
- Allows symbols and unicode



# Searching For Passwords in Configuration Files
Windows can automate a variety of tasks, such as a mass rollout or installation of Windows in bulk. This is done through the use of the **Unattended Windows Setup** utility. This tools utilizes config files that contain specific configurations and user account credentials, mainly the Administrator account password.

If this files are left on the target system after the setup, they can reveal too much! These files can be found at the following locations:
`C:\Windows\Panther\Unattend.xml`
`C:\Windows\Panther\Autounattended.xml`

It's important to note that passwords may be encoded in *base64*.

Now we are ready for some password hunting! :D




#### Links:
- [Microsoft Learn - NTLM User Authentication](https://learn.microsoft.com/en-us/troubleshoot/windows-server/windows-security/ntlm-user-authentication)

202403231303
