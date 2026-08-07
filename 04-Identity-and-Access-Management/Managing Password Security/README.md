# Managing Password Security

## Overview

This lab explores common password attack techniques and demonstrates how organizations can improve password security through stronger authentication policies.

The exercises covered:

- Password spraying attacks
- Dictionary-based password cracking
- Brute force password cracking
- Domain password policy improvements

The lab environment included:

- **KALI** — Kali Linux attack workstation
- **MS10** — Windows Server 2016 target system
- **DC10** — Windows Server 2019 Domain Controller

---

# Objectives

- Understand how password spraying attacks work
- Perform offline password cracking using dictionary attacks
- Perform offline password cracking using brute force attacks
- Analyze password security weaknesses
- Configure stronger Active Directory password policies

---

# Tools Used

- Kali Linux
- Hydra
- John the Ripper
- NTLM password hashes
- Active Directory PowerShell module

---

# Lab 1: Password Spraying Attack

## Description

A password spraying attack attempts a small number of commonly used passwords against many user accounts.

Unlike brute force attacks that attempt many passwords against one account, password spraying attempts to avoid account lockout by using a limited number of passwords across multiple accounts.

---

## Creating Password Spray Files

Created a password list containing commonly discovered passwords:

- abc123
- 123456
- Pa$$w0rd

Created the password file:

```bash
echo abc123 > pass.txt
echo 123456 >> pass.txt
echo 'Pa$$w0rd' >> pass.txt
```

Screenshot:

![Password Spray Setup](./images/01-passwordspray-setup.png)

---

## Running Hydra Password Spray Attack

Used Hydra to perform an SMB password spraying attack against the MS10 system.

Command:

```bash
hydra-wizard
```

Configuration:

- Service: SMB
- Target: 10.1.16.2
- Username file: users.txt
- Password file: pass.txt

Hydra successfully identified accounts using weak shared passwords.

Screenshot:

![Hydra Password Spray Results](./images/02-hydra-passwordspray.png)

---

## Verifying Discovered Credentials

Mounted the HR share using discovered credentials:

```bash
mount //10.1.16.2/HR /mnt/HR -o username=jaime
```

Successfully accessed the shared directory.

Screenshot:

![Mounted HR Share](./images/03-share-mounted.png)

---

# Lab 2: Dictionary Password Cracking

## Description

Dictionary attacks are offline password attacks where stolen password hashes are compared against large password lists.

Unlike online attacks, offline attacks are not limited by account lockout policies.

---

## Viewing Password Hashes

Displayed extracted NTLM password hashes:

```bash
cat ms10-hashes.txt
```

Screenshot:

![NTLM Hash File](./images/04-dictionary-crack.png)

---

## Running John the Ripper Dictionary Attack

Used the SecLists password dictionary:

```bash
john --format=NT --wordlist=/usr/share/seclists/Passwords/xato-net-10-million-passwords.txt ms10-hashes.txt
```

John successfully cracked multiple weak passwords.

Screenshot:

![Dictionary Attack Results](./images/05-dictionary-results.png)

---

## Exporting Cracked Passwords

Saved cracked passwords:

```bash
john --show --format=NT ms10-hashes.txt > dict-cracked.txt
```

Viewed results:

```bash
less dict-cracked.txt
```

Screenshot:

![Dictionary Cracked Passwords](./images/06-bruteforce-status.png)

---

# Lab 3: Brute Force Password Cracking

## Description

A brute force attack attempts every possible character combination until the correct password is discovered.

Password complexity and length significantly increase resistance against brute force attacks.

---

## Clearing Previous John Results

Removed previous John password cache:

```bash
rm ~/.john/john.pot
```

---

## Running Brute Force Attack

Started incremental brute force cracking:

```bash
john --format=NT --incremental ms10-hashes.txt
```

The attack was allowed to run and progress was monitored.

Screenshot:

![Brute Force Attack](./images/07-password-policy-after.png)

---

# Lab 4: Domain Password Policy Hardening

## Description

After evaluating password weaknesses, the Active Directory password policy was strengthened.

Changes implemented:

| Setting | New Value |
|---|---|
| Lockout Observation Window | 15 minutes |
| Lockout Duration | 15 minutes |
| Lockout Threshold | 3 attempts |
| Maximum Password Age | 365 days |
| Minimum Password Age | 3 days |
| Minimum Password Length | 12 characters |

---

## Viewing Updated Password Policy

The domain password policy was displayed using:

```powershell
Get-ADDefaultDomainPasswordPolicy
```

Screenshot:

![Updated Domain Password Policy](./images/08-password-policy-updated.png)

---

# Security Concepts Learned

## Password Spraying

Password spraying attempts:

- A small list of known passwords
- Against many different accounts

Primary goal:

- Avoid account lockout
- Identify weak credentials

**Security controls:**

- Strong password policies
- MFA
- Account lockout thresholds
- Password monitoring

---

## Dictionary Attacks

Dictionary attacks compare password hashes against known password lists.

Success depends on:

- Password appearing in the dictionary
- Weak password selection
- Poor password complexity

**Security controls:**

- Longer passwords
- Password screening
- MFA
- Strong hashing algorithms

---

## Brute Force Attacks

Brute force attacks attempt every possible password combination.

Resistance improves with:

- Increased password length
- Greater complexity
- Strong hashing algorithms

---

# Key Takeaways

- Password spraying targets many accounts with a small password set.
- Dictionary attacks rely on previously known password lists.
- Brute force attacks become exponentially harder as password length increases.
- Offline attacks are dangerous because they bypass account lockout controls.
- Strong password policies reduce the success rate of password attacks.
- Organizations should implement MFA and strong identity controls.

---

# CompTIA Security+ Objectives Covered

## 2.4
Analyze indicators of malicious activity.

## 2.5
Explain mitigation techniques used to secure the enterprise.

## 4.6
Implement and maintain identity and access management.

## 5.1
Summarize elements of effective security governance.

## 5.6
Implement security awareness practices.