# 🐧 Linux File Permissions – chmod, chown & chgrp

> **Linux & DevOps Learning Journey — File Permissions**

This guide explains Linux file permissions and ownership management from the perspective of a DevOps engineer, focusing on access control, user permissions, group permissions, file security, production server administration, and troubleshooting.

## 📚 Table of Contents

1. [Introduction](#-introduction)
2. [Understanding Linux File Permissions](#1-understanding-linux-file-permissions)
3. [Understanding User, Group and Others](#2-understanding-user-group-and-others)
4. [Understanding Read, Write and Execute Permissions](#3-understanding-read-write-and-execute-permissions)
5. [chmod – Change File Permissions](#4-chmod--change-file-permissions)
6. [Symbolic Permission Method](#5-symbolic-permission-method)
7. [Numeric / Octal Permission Method](#6-numeric--octal-permission-method)
8. [chown – Change File Ownership](#7-chown--change-file-ownership)
9. [chgrp – Change Group Ownership](#8-chgrp--change-group-ownership)
10. [Understanding Special Permission Values](#9-understanding-special-permission-values)
11. [Real-World Production Scenario](#-real-world-production-scenario)
12. [Security Best Practices](#-security-best-practices)
13. [Quick Command Reference](#-quick-command-reference)
14. [Interview Questions](#-interview-questions)
15. [Hands-On Practice](#-hands-on-practice)

---

## 🎯 Introduction

Linux is a multi-user operating system.

Multiple users and applications can access the same Linux server. However, not every user should have permission to read, modify, or execute every file.

For example, imagine you're working as a DevOps engineer managing an AWS EC2 production server.

Your server contains:

- Application source code.
- Configuration files.
- Deployment scripts.
- Application logs.
- Database backup files.
- SSH private keys.

You cannot allow every user to modify all these files.

A developer may need permission to modify application files, while a monitoring user may only need permission to read application logs.

This is where **Linux File Permissions** become important.

Linux provides three important commands for managing permissions and ownership:

| Command | Purpose |
|---|---|
| `chmod` | Change file or directory permissions |
| `chown` | Change file or directory ownership |
| `chgrp` | Change file or directory group ownership |

Understanding these commands is essential for Linux administration, DevOps, server security, and production troubleshooting.

---

## 1. Understanding Linux File Permissions

**Purpose:** Linux file permissions control which users can read, write, or execute files and directories.

To understand permissions, first execute:

```bash
ls -l
```

Example output:

```text
-rwxr-xr-- 1 jasin developers 1024 Nov 21 10:30 deploy.sh
```

Let's break down the output.

```text
-rwxr-xr--
```

This section represents the file type and its permissions.

### Understanding the Permission Structure

```text
- rwx r-x r--
  │   │   │
  │   │   └── Others
  │   └────── Group
  └────────── Owner
```

| Section | Meaning |
|---|---|
| `-` | Regular file |
| `rwx` | Owner permissions |
| `r-x` | Group permissions |
| `r--` | Others permissions |

### Understanding File Types

The first character in `ls -l` indicates the file type.

| Character | File Type |
|---|---|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device |
| `b` | Block device |
| `p` | Named pipe |
| `s` | Socket |

Example:

```bash
ls -l
```

Possible output:

```text
-rw-r--r-- 1 jasin developers 1024 app.txt
drwxr-xr-x 2 jasin developers 4096 logs
lrwxrwxrwx 1 jasin developers   10 current -> releases
```

Here:

- `app.txt` is a regular file.
- `logs` is a directory.
- `current` is a symbolic link.

**DevOps Insight:** When troubleshooting permission errors on a production server, start with `ls -l` to identify the file owner, group, and permission settings.

---

## 2. Understanding User, Group and Others

Linux divides permission ownership into three categories.

### 1. User (`u`)

The user represents the owner of the file.

Example:

```text
-rwxr-xr--
```

The owner has:

```text
rwx
```

This means the owner can read, write, and execute the file.

### 2. Group (`g`)

The group represents users belonging to the file's assigned group.

For example:

```text
developers
```

Every user who belongs to the `developers` group may receive the permissions assigned to that group.

### 3. Others (`o`)

Others represent users who are neither the file owner nor members of its assigned group.

### Understanding All (`a`)

Linux also supports `a`, which represents all three permission categories.

| Symbol | Meaning |
|---|---|
| `u` | Owner / user |
| `g` | Group |
| `o` | Others |
| `a` | All users |

### Real-World Scenario

Imagine your company has three developers working on a shared application.

Your server has a directory:

```text
/var/www/myapp
```

The ownership is:

```text
Owner: jasin
Group: developers
```

You want:

- Jasin to have full access.
- Developers to read and execute application files.
- Other users to have limited access.

Linux permissions allow you to configure these access levels independently.

**DevOps Insight:** Groups are important in production environments because you can manage access for multiple users without changing each user's permissions individually.

---

## 3. Understanding Read, Write and Execute Permissions

Linux provides three basic permissions.

### 1. Read (`r`)

**Purpose:** Allows a user to read file contents.

Example:

```bash
chmod u+r app.txt
```

This grants the file owner read permission.

For a directory, read permission allows users to list its entries, subject to other required permissions.

### 2. Write (`w`)

**Purpose:** Allows a user to modify file contents.

Example:

```bash
chmod u+w app.txt
```

This grants the file owner write permission.

For a directory, write permission allows creating, deleting, and renaming entries, subject to execute permission and other access controls.

### 3. Execute (`x`)

**Purpose:** Allows a user to execute a file as a program or script.

Example:

```bash
chmod u+x deploy.sh
```

This grants the owner execute permission.

For a directory, execute permission allows users to enter or traverse the directory and access entries when other permissions permit it.

### Permission Comparison

| Permission | File | Directory |
|---|---|---|
| `r` | Read file contents | List directory entries |
| `w` | Modify file contents | Create, delete or rename entries when combined with `x` |
| `x` | Execute the file | Enter or traverse the directory |

### Real-World Scenario

Imagine you have a deployment script:

```bash
deploy.sh
```

You try to execute it:

```bash
./deploy.sh
```

But Linux displays:

```text
Permission denied
```

The script may not have execute permission.

Grant execute permission:

```bash
chmod +x deploy.sh
```

Now execute:

```bash
./deploy.sh
```

**DevOps Insight:** A script can exist on a server but still fail to execute because the required execute permission is missing. Always inspect permissions before troubleshooting the script itself.

---

## 4. `chmod` – Change File Permissions

**Purpose:** Changes the read, write, and execute permissions of a file or directory.

`chmod` stands for Change Mode.

### Syntax

```bash
chmod permissions filename
```

Example:

```bash
chmod +x deploy.sh
```

This adds execute permission to the file.

### Important Permission Operators

The symbolic method uses these operators:

| Operator | Meaning |
|---|---|
| `+` | Add permission |
| `-` | Remove permission |
| `=` | Set exact permissions |

### Real-World Scenario

Imagine you're deploying a shell script on an AWS EC2 server.

You download the deployment script:

```bash
wget https://example.com/deploy.sh
```

You try to execute it:

```bash
./deploy.sh
```

Linux returns:

```text
Permission denied
```

You inspect the file:

```bash
ls -l deploy.sh
```

Output:

```text
-rw-r--r-- 1 jasin developers 1024 deploy.sh
```

The file does not have execute permission.

Fix it:

```bash
chmod +x deploy.sh
```

Verify:

```bash
ls -l deploy.sh
```

Output:

```text
-rwxr-xr-x 1 jasin developers 1024 deploy.sh
```

Now the file has execute permission.

**DevOps Insight:** `chmod` is frequently used in CI/CD pipelines to make deployment scripts executable before running them.

---

## 5. Symbolic Permission Method

**Purpose:** Changes permissions using readable symbols instead of numeric values.

### Syntax

```bash
chmod [user/group/others/all][operator][permission] filename
```

The permission categories are:

- `u` – Owner.
- `g` – Group.
- `o` – Others.
- `a` – All users.

The operators are:

- `+` – Add.
- `-` – Remove.
- `=` – Assign exact permissions.

### Example 1: Give Execute Permission to All Users

```bash
chmod +x testfile
```

This adds execute permission to all user categories.

### Example 2: Remove Write Permission from the Owner

```bash
chmod u-w testfile
```

This removes write permission from the owner.

### Example 3: Modify Multiple Permission Categories

```bash
chmod u+wx,g-x,o=rx testfile
```

Let's break it down.

| Part | Meaning |
|---|---|
| `u+wx` | Add write and execute permissions to owner |
| `g-x` | Remove execute permission from group |
| `o=rx` | Set others' permissions to read and execute only |

All three changes are applied in one command.

### Example 4: Give Read Permission to Everyone

```bash
chmod a+r app.txt
```

This adds read permission for the owner, group, and others.

### Example 5: Remove All Permissions from Others

```bash
chmod o-rwx app.txt
```

This removes read, write, and execute permissions from others.

### Example 6: Set Exact Permissions for the Owner

```bash
chmod u=rwx app.sh
```

This sets the owner's permissions to read, write, and execute.

### Real-World Scenario

Imagine your production server contains an application deployment script.

Only the application owner should be able to modify the script, while developers in the assigned group should be able to read and execute it.

You can configure:

```bash
chmod u=rwx,g=rx,o= app.sh
```

Result:

```text
-rwxr-x---
```

The owner has full permissions, the group can read and execute, and others have no permissions.

**DevOps Insight:** Symbolic permissions are useful when you need to modify specific permissions without replacing the entire permission configuration.

---

## 6. Numeric / Octal Permission Method

**Purpose:** Changes permissions using numeric values instead of symbolic operators.

Each permission has a numeric value.

| Permission | Numeric Value |
|---|---:|
| Read (`r`) | 4 |
| Write (`w`) | 2 |
| Execute (`x`) | 1 |
| No permission (`-`) | 0 |

You add the values together to calculate the required permissions.

### Understanding Permission Calculations

| Numeric Value | Calculation | Permission |
|---|---|---|
| `0` | 0 | `---` |
| `1` | 1 | `--x` |
| `2` | 2 | `-w-` |
| `3` | 2 + 1 | `-wx` |
| `4` | 4 | `r--` |
| `5` | 4 + 1 | `r-x` |
| `6` | 4 + 2 | `rw-` |
| `7` | 4 + 2 + 1 | `rwx` |

### Understanding Three-Digit Permissions

A numeric permission contains three digits.

```text
chmod XYZ filename
```

| Position | Represents |
|---|---|
| First digit | Owner |
| Second digit | Group |
| Third digit | Others |

### Example 1: `chmod 777`

```bash
chmod 777 testfile
```

Calculation:

```text
7 = rwx
7 = rwx
7 = rwx
```

Result:

```text
-rwxrwxrwx
```

Everyone can read, write, and execute the file.

**Warning:** Giving everyone full permissions is generally unsafe for production files.

### Example 2: `chmod 755`

```bash
chmod 755 deploy.sh
```

Calculation:

```text
7 = rwx
5 = r-x
5 = r-x
```

Result:

```text
-rwxr-xr-x
```

The owner has full permissions, while group and others can read and execute.

### Example 3: `chmod 644`

```bash
chmod 644 app.txt
```

Calculation:

```text
6 = rw-
4 = r--
4 = r--
```

Result:

```text
-rw-r--r--
```

The owner can read and write, while group and others can only read.

### Example 4: `chmod 700`

```bash
chmod 700 private.sh
```

Result:

```text
-rwx------
```

Only the owner has permissions.

### Example 5: `chmod 600`

```bash
chmod 600 private.key
```

Result:

```text
-rw-------
```

Only the owner can read and write the file.

This is a common restrictive permission setting for private key files.

### Understanding `chmod 365`

```bash
chmod 365 testfile
```

Calculation:

```text
3 = -wx
6 = rw-
5 = r-x
```

Result:

```text
--wxrw-r-x
```

The owner has write and execute permissions, the group has read and write permissions, and others have read and execute permissions.

### Understanding `chmod 177`

```bash
chmod 177 testfile
```

This is a four-character permission value in which the leading `1` sets the sticky bit.

The remaining `77` means:

```text
Owner:   ---
Group:   rwx
Others:  rwx
```

The sticky bit has a special effect on directories, restricting deletion or renaming of entries to the file owner, directory owner, or a privileged user.

### Common Permission Values

| Command | Permission | Typical Usage |
|---|---|---|
| `chmod 777` | `rwxrwxrwx` | Avoid for normal production files |
| `chmod 755` | `rwxr-xr-x` | Executable scripts and directories |
| `chmod 750` | `rwxr-x---` | Restricted application directories |
| `chmod 700` | `rwx------` | Private executable scripts |
| `chmod 644` | `rw-r--r--` | Regular readable files |
| `chmod 640` | `rw-r-----` | Files readable by owner and group |
| `chmod 600` | `rw-------` | Private configuration and key files |

### Real-World Scenario

Imagine your deployment pipeline needs to execute a shell script.

The script should be executable by the owner and readable and executable by other authorized users.

Execute:

```bash
chmod 755 deploy.sh
```

For a private environment file containing application configuration, use:

```bash
chmod 600 .env
```

This restricts access to the file owner.

**DevOps Insight:** Numeric permissions are commonly used in Dockerfiles, deployment scripts, CI/CD pipelines, and Linux server configuration. Understand what each digit means before applying permissions to production files.

---

## 7. `chown` – Change File Ownership

**Purpose:** Changes the owner of a file or directory.

`chown` stands for Change Owner.

Every Linux file has an owner and an associated group.

Sometimes files are created by the root user or another account, but an application needs to access them under a different user.

This is where `chown` becomes useful.

### Syntax

```bash
chown username filename
```

### Example 1: Change File Owner

```bash
sudo chown user2 app.txt
```

This changes the owner of `app.txt` to `user2`.

### Example 2: Change Owner and Group Together

```bash
sudo chown user2:developers app.txt
```

This changes:

```text
Owner: user2
Group: developers
```

### Example 3: Change Ownership Recursively

```bash
sudo chown -R user2:developers /var/www/myapp
```

The `-R` option means recursive.

It changes ownership of the specified directory and its contents.

### Verify Ownership

```bash
ls -l app.txt
```

Example output:

```text
-rw-r--r-- 1 user2 developers 1024 Nov 21 10:30 app.txt
```

### Real-World Scenario

Imagine you're deploying a web application on an Ubuntu server.

Your application directory is:

```text
/var/www/myapp
```

You discover that the files are owned by root:

```text
drwxr-xr-x root root /var/www/myapp
```

Your application process runs under the `www-data` user.

You may need to assign appropriate ownership:

```bash
sudo chown -R www-data:www-data /var/www/myapp
```

Verify:

```bash
ls -ld /var/www/myapp
```

**Important:** Do not blindly assign ownership of every application file to the web server user. The correct owner depends on your deployment model. In many production environments, application code should be owned by a deployment account and only specific runtime directories should be writable by the application process.

**DevOps Insight:** Incorrect ownership is a common reason applications cannot access configuration files, upload directories, log directories, or deployment artifacts.

---

## 8. `chgrp` – Change Group Ownership

**Purpose:** Changes the group ownership of a file or directory.

`chgrp` stands for Change Group.

Linux groups make it easier to provide shared access to multiple users.

### Syntax

```bash
chgrp groupname filename
```

### Example 1: Change Group Ownership

```bash
sudo chgrp developers app.txt
```

This changes the group ownership of `app.txt` to `developers`.

### Example 2: Change Group Ownership Recursively

```bash
sudo chgrp -R developers /var/www/myapp
```

This changes the group ownership of the directory and its contents.

### Verify Group Ownership

```bash
ls -l app.txt
```

Example output:

```text
-rw-r--r-- 1 jasin developers 1024 Nov 21 10:30 app.txt
```

The file owner is `jasin`, and the group is `developers`.

### Real-World Scenario

Imagine your company has a shared application directory.

Multiple developers need access to the same project files.

Instead of changing the owner of every file, you can assign a common group:

```bash
sudo chgrp -R developers /var/www/myapp
```

You can then configure group permissions:

```bash
sudo chmod -R g+rwX /var/www/myapp
```

The capital `X` adds execute permission to directories and to files that already have execute permission.

**DevOps Insight:** Group ownership is useful when multiple developers, deployment users, and administrators need controlled access to shared application directories.

---

## 9. Understanding Special Permission Values

Linux supports special permission bits in addition to the standard read, write, and execute permissions.

These include:

- SUID (Set User ID).
- SGID (Set Group ID).
- Sticky bit.

They are important when managing shared directories and executable programs.

### 1. SUID – Set User ID

The SUID bit is represented by the leading numeric value `4`.

When applied to an executable file, it causes the process to run with the file owner's effective user ID, subject to system security controls.

Example:

```bash
sudo chmod 4755 program
```

You can identify SUID using:

```bash
ls -l program
```

The owner's execute position may display `s`.

**Security Note:** SUID executables require careful security review because they can provide elevated privileges.

### 2. SGID – Set Group ID

The SGID bit is represented by the leading numeric value `2`.

On a directory, newly created files and directories generally inherit the directory's group ownership.

Example:

```bash
sudo chmod 2775 /var/www/shared
```

This is useful for shared project directories.

You can identify SGID using:

```bash
ls -ld /var/www/shared
```

The group's execute position may display `s`.

### 3. Sticky Bit

The sticky bit is represented by the leading numeric value `1`.

It is commonly used on shared directories.

Example:

```bash
sudo chmod 1777 /tmp
```

The sticky bit prevents ordinary users from deleting or renaming files owned by other users inside the directory, subject to ownership and privilege rules.

You can identify it using:

```bash
ls -ld /tmp
```

The others' execute position may display `t`.

### Special Permission Values

| Special Permission | Numeric Value | Common Purpose |
|---|---:|---|
| SUID | `4` | Run executable with file owner's effective identity |
| SGID | `2` | Run executable with file group's effective identity; inherit group on directories |
| Sticky bit | `1` | Restrict deletion or renaming of other users' files in shared directories |

**DevOps Insight:** Understand special permission bits before modifying system directories or privileged executables. Incorrect SUID or SGID configurations can introduce serious security risks.

---

## 🚀 Real-World Production Scenario

### Situation: Fixing Application Permission Issues on an AWS EC2 Server

Imagine you're a DevOps engineer responsible for a Spring Boot application deployed on an Ubuntu EC2 instance.

Your application is located at:

```text
/var/www/myapp
```

The application is failing because the deployment script cannot execute and the application process cannot access its required files.

Let's investigate the issue step by step.

### Step 1: Inspect Current File Permissions

```bash
ls -l /var/www/myapp
```

Example output:

```text
-rw-r--r-- 1 root root 1024 deploy.sh
-rw-r--r-- 1 root root 2048 application.jar
```

You notice that the deployment script does not have execute permission.

### Step 2: Make the Deployment Script Executable

```bash
sudo chmod 755 /var/www/myapp/deploy.sh
```

Now the owner can read, write, and execute the script, while group and others can read and execute it.

### Step 3: Inspect Directory Ownership

```bash
ls -ld /var/www/myapp
```

Example output:

```text
drwxr-xr-x 2 root root 4096 /var/www/myapp
```

The directory is owned by root.

### Step 4: Assign Appropriate Ownership

Suppose your deployment process uses the `deploy` user and `developers` group.

Execute:

```bash
sudo chown -R deploy:developers /var/www/myapp
```

This changes the owner and group of the application directory and its contents.

### Step 5: Configure Group Permissions

```bash
sudo chmod -R g+rwX /var/www/myapp
```

This grants the group read and write access, and adds execute permission to directories and files that already have execute permission.

### Step 6: Verify the Changes

```bash
ls -l /var/www/myapp
```

Check that the ownership and permissions match your deployment requirements.

### Step 7: Execute the Deployment Script

```bash
cd /var/www/myapp
./deploy.sh
```

The script can now execute if its interpreter, dependencies, and other required permissions are correctly configured.

### What Did You Learn?

You used:

- `ls -l` to inspect file permissions.
- `chmod` to modify permissions.
- `chown` to change ownership.
- `chgrp` to change group ownership.
- `chmod -R` to modify permissions recursively.
- `ls -ld` to inspect directory permissions.

**Production Insight:** Permission troubleshooting should begin by identifying the actual user running the application, the owner and group of the affected files, and the permissions on every parent directory. Do not solve every permission problem by using `chmod 777`.

---

## 🔐 Security Best Practices

When managing permissions on production Linux servers, follow these practices:

1. **Avoid `chmod 777`:** It gives everyone full access and can expose files to unauthorized modification.

2. **Use least privilege:** Grant only the permissions users and applications actually require.

3. **Protect private keys:** Use restrictive permissions such as `chmod 600 private.key` where appropriate.

4. **Be careful with recursive operations:** Commands such as `chmod -R` and `chown -R` can affect thousands of files.

5. **Use groups for shared access:** Assign appropriate group ownership rather than giving access to everyone.

6. **Verify before modifying:** Use `ls -l` and `ls -ld` to inspect existing permissions.

7. **Understand application users:** Check which Linux account runs your application or service before changing ownership.

8. **Protect sensitive configuration:** Files containing credentials, tokens, or secrets should not be readable by unauthorized users.

---

## 📌 Quick Command Reference

| Command | Description |
|---|---|
| `ls -l` | Display file permissions and ownership |
| `ls -ld directory` | Display directory permissions and ownership |
| `chmod +x file` | Add execute permission to all categories |
| `chmod u-w file` | Remove owner write permission |
| `chmod u+wx,g-x,o=rx file` | Modify multiple permission categories |
| `chmod 755 file` | Owner full access; group and others read and execute |
| `chmod 644 file` | Owner read/write; group and others read |
| `chmod 700 file` | Only owner has full access |
| `chmod 600 file` | Only owner can read and write |
| `chmod 777 file` | Everyone has full access |
| `chown user file` | Change file owner |
| `chown user:group file` | Change file owner and group |
| `chown -R user:group directory` | Recursively change ownership |
| `chgrp group file` | Change file group |
| `chgrp -R group directory` | Recursively change group ownership |
| `chmod 4755 file` | Set SUID with permissions 755 |
| `chmod 2775 directory` | Set SGID with permissions 775 |
| `chmod 1777 directory` | Set sticky bit with permissions 777 |

---

## 🎯 Interview Questions

### Basic Level

1. What are Linux file permissions?
2. What is the difference between user, group, and others?
3. What do read, write, and execute permissions mean?
4. What is the purpose of the `chmod` command?
5. What is the purpose of the `chown` command?
6. What is the purpose of the `chgrp` command?
7. What does `ls -l` display?

### Intermediate Level

8. What is the difference between symbolic and numeric permission methods?
9. Explain `chmod 755`.
10. Explain `chmod 644`.
11. What is the difference between `chmod 777` and `chmod 700`?
12. What does `chmod u-w file` do?
13. What does `chmod u+wx,g-x,o=rx file` do?
14. What is the purpose of the `-R` option in `chmod` and `chown`?
15. What is the difference between file and directory permissions?

### Senior DevOps Engineer Scenario Questions

16. Your deployment script returns `Permission denied`. How would you troubleshoot it?

17. Your application cannot write to its log directory. How would you investigate the ownership and permissions?

18. Multiple developers need access to the same application directory. How would you configure group ownership and permissions?

19. Why is `chmod 777` considered dangerous in production?

20. Your application runs as `www-data`, but the application directory is owned by root. How would you investigate and resolve the access problem safely?

21. What is the difference between SUID, SGID, and the sticky bit?

22. What happens when you execute `chmod -R 777 /var/www/myapp`? Why should you avoid doing this blindly?

---

## 🧪 Hands-On Practice

Try these commands in your Linux environment.

- [ ] Create a file named `testfile`.
- [ ] Display its permissions using `ls -l`.
- [ ] Add execute permission using `chmod +x testfile`.
- [ ] Remove write permission from the owner using `chmod u-w testfile`.
- [ ] Give everyone read permission using `chmod a+r testfile`.
- [ ] Set permissions to `755` using numeric notation.
- [ ] Set permissions to `644` and inspect the result.
- [ ] Create a directory named `devops`.
- [ ] Change its owner using `chown`.
- [ ] Change its group using `chgrp`.
- [ ] Change both owner and group using `chown user:group`.
- [ ] Apply group permissions using `chmod g+rwX`.
- [ ] Inspect directory permissions using `ls -ld`.
- [ ] Practise setting the sticky bit on a test directory.

---

## 🏁 Key Takeaway

Linux file permissions control who can access, modify, and execute files and directories.

For a DevOps engineer, these concepts are essential for:

- Application deployment.
- Server security.
- User and group management.
- CI/CD automation.
- Production troubleshooting.
- Shared directory management.
- Protecting sensitive configuration files.
- Managing application ownership.

**Learning approach:** Understand the permission structure, practise symbolic and numeric permissions, and apply ownership changes to realistic Linux server scenarios.

---

**Progress:** Linux File Permissions — Completed after practical exercises.

*Learning Linux one topic at a time, from fundamentals to production-level DevOps engineering.*
