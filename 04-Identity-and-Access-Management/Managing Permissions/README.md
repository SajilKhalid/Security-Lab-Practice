# Managing Permissions

## Overview

This lab demonstrates how to configure and manage file and resource permissions across Linux and Windows environments. The exercises cover Linux file permissions using `chmod`, Windows NTFS permissions using `icacls`, Windows Effective Access, and Windows SMB share permissions.

The lab uses:

* **KALI** — Kali Linux
* **PC10** — Windows Server 2019 functioning as a client

## Objectives

* Configure Linux file permissions using symbolic and octal notation.
* View and modify Windows NTFS permissions.
* Understand the effects of granting, denying, and removing NTFS permissions.
* Review effective permissions for a Windows user.
* Create and manage Windows SMB share permissions.
* Understand how NTFS and share permissions work together.

---

## 1. Linux File Permissions

Linux permissions are divided into three categories:

* **User/Owner**
* **Group**
* **Others**

Each category can have:

* `r` — Read
* `w` — Write
* `x` — Execute

Permissions can be represented symbolically or numerically using octal notation.

### Initial Permissions

The initial permissions of `testfile.txt` were viewed using:

```bash
ls -l te*
```

The original permissions were:

```text
-rw-r--r--
```

This means:

* Owner: Read + Write
* Group: Read
* Others: Read

![Initial Linux Permissions](./01-linux-permissions-initial.png)

### Modifying Permissions with chmod

The lab demonstrated modifying permissions using symbolic notation:

```bash
chmod u+x testfile.txt
chmod g+w testfile.txt
chmod go-r,u-x testfile.txt
```

Octal notation was also demonstrated:

```bash
chmod 777 testfile.txt
chmod 740 testfile.txt
chmod 654 testfile.txt
chmod 644 testfile.txt
```

![Linux chmod Permissions](./02-linux-permissions-chmod.png)

### Configuring demofile.sh

The required permissions for `demofile.sh` were:

```text
-rwx--x---
```

This corresponds to octal permission:

```text
710
```

The command used was:

```bash
chmod 710 demofile.sh
```

The resulting permissions provide:

* Owner: Read, Write, Execute
* Group: Execute
* Others: No permissions

![demofile.sh Permissions](./03-linux-demofile-permissions.png)

The script was then executed and inspected using:

```bash
./demofile.sh
cat demofile.sh
```

---

## 2. Windows NTFS Permissions

Windows NTFS permissions were managed on:

```text
C:\LABFILES\comptia-logo.jpg
```

The existing permissions were first viewed using:

```powershell
icacls .\comptia-logo.jpg
```

### Denying Permissions

Read permission was denied to the `dylan` account:

```powershell
icacls .\comptia-logo.jpg /deny dylan:R
```

The resulting permissions were then verified with:

```powershell
icacls .\comptia-logo.jpg
```

![NTFS Deny Permission](./04a-ntfs-permissions-deny.png)

### Granting and Removing Permissions

Full control was then granted to `dylan`:

```powershell
icacls .\comptia-logo.jpg /grant dylan:F
```

The permissions were verified and then Dylan's specific permission entry was removed:

```powershell
icacls .\comptia-logo.jpg /remove:g dylan
```

The final permissions were verified using:

```powershell
icacls .\comptia-logo.jpg
```

![NTFS Grant and Remove Permissions](./04b-ntfs-permissions-grant-remove.png)

Removing a user's specific NTFS permission entry does not necessarily remove all access. The user may still receive permissions through membership in other groups.

---

## 3. Windows Effective Access

Windows Effective Access was used to determine the permissions that the `dylan` account effectively has on `comptia-logo.jpg`.

The Effective Access tool was accessed through:

**Properties → Security → Advanced → Effective Access**

The `dylan` user was selected and the effective permissions were displayed.

![Windows Effective Access](./05-effective-access.png)

Effective Access considers factors such as:

* Direct user permissions
* Group memberships
* Inherited permissions
* Share permissions when the resource is accessed through a network share

Effective Access is a predictive troubleshooting tool and does not modify permissions or group memberships.

---

## 4. Windows Share Permissions

A Windows SMB share named `LABFILES` was created for:

```text
C:\LABFILES
```

The share was created using:

```powershell
New-SmbShare -Name "LABFILES" -Path "C:\LABFILES" -Description "Share for LABFILES"
```

Existing shares were viewed with:

```powershell
Get-SMBShare
```

The permissions for the new share were viewed using:

```powershell
Get-SmbShareAccess -Name "LABFILES"
```

### Granting Share Permissions

Change permission was granted to `dylan`:

```powershell
Grant-SmbShareAccess -Name "LABFILES" -AccountName "dylan" -AccessRight Change
```

![Windows Share Permissions](./06-share-permissions.png)

### Removing Share Permissions

Dylan's specific share permission was removed with:

```powershell
Revoke-SmbShareAccess -Name "LABFILES" -AccountName "dylan"
```

The resulting share permissions were then verified.

![Windows Share Permissions Removed](./07-share-permissions-removed.png)

---

## Key Security Concepts

### Linux chmod

`chmod` is used to modify Linux file and directory permissions.

The octal values are:

| Permission    | Value |
| ------------- | ----: |
| Read (`r`)    |     4 |
| Write (`w`)   |     2 |
| Execute (`x`) |     1 |

For example:

```text
7 = rwx
5 = r-x
0 = ---
```

Therefore:

```text
750 = rwxr-x---
```

### NTFS Permissions

Common NTFS permissions include:

* Read
* Read & Execute
* Write
* Modify
* Full Control

**Full Control** provides the highest level of access to a file or folder.

### Share vs. NTFS Permissions

When a resource is accessed through a Windows network share, both **share permissions** and **NTFS permissions** can apply.

The **most restrictive combination** determines the user's effective access.

### Effective Access

Effective Access helps administrators determine what permissions a user actually has after considering:

* User permissions
* Group memberships
* Inheritance
* Share permissions

---

## Security+ Exam Takeaways

* `chmod` modifies Linux file permissions.
* Linux permissions are divided into **owner, group, and others**.
* `rwx` corresponds to **read, write, execute**.
* `chmod 750` gives the owner full access, the group read/execute access, and others no access.
* `icacls` is the Windows command-line tool for viewing and modifying NTFS permissions.
* **Full Control** is the highest standard NTFS permission.
* Removing a user's direct permission does not necessarily remove permissions inherited through groups.
* **Effective Access** calculates a user's resulting access based on permissions and group membership.
* Windows network access can involve both **NTFS permissions and share permissions**.
* When both share and NTFS permissions apply, the **most restrictive access** takes precedence.

## Commands Used

### Linux

```bash
ls -l
ls -l te*
chmod u+x testfile.txt
chmod g+w testfile.txt
chmod go-r,u-x testfile.txt
chmod 777 testfile.txt
chmod 740 testfile.txt
chmod 654 testfile.txt
chmod 644 testfile.txt
chmod 710 demofile.sh
./demofile.sh
cat demofile.sh
```

### Windows NTFS

```powershell
icacls .\comptia-logo.jpg
icacls .\comptia-logo.jpg /deny dylan:R
icacls .\comptia-logo.jpg /grant dylan:F
icacls .\comptia-logo.jpg /remove:g dylan
```

### Windows SMB Shares

```powershell
New-SmbShare -Name "LABFILES" -Path "C:\LABFILES" -Description "Share for LABFILES"
Get-SMBShare
Get-SmbShareAccess -Name "LABFILES"
Grant-SmbShareAccess -Name "LABFILES" -AccountName "dylan" -AccessRight Change
Revoke-SmbShareAccess -Name "LABFILES" -AccountName "dylan"
```

## Lab Summary

This lab demonstrated how access control is implemented across Linux and Windows systems. Linux permissions were configured using both symbolic and octal `chmod` notation. Windows NTFS permissions were viewed and modified using `icacls`, while Effective Access was used to evaluate the resulting permissions for a specific user.

The lab also demonstrated that Windows share permissions operate alongside NTFS permissions and that the most restrictive combination determines effective network access. These concepts are important for implementing least privilege and preventing unauthorized access to enterprise resources.
           