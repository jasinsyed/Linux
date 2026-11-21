# 🐧 Linux Commands for File Management

> **Linux & DevOps Learning Journey — Topic 01**

This guide covers essential Linux file-management commands from the perspective of a DevOps engineer, focusing on practical usage, production scenarios, and troubleshooting.

## 📚 Table of Contents

1. [Introduction](#-introduction)
2. [pwd – Print Working Directory](#1-pwd--print-working-directory)
3. [ls – List Directory Contents](#2-ls--list-directory-contents)
4. [cd – Change Directory](#3-cd--change-directory)
5. [mkdir – Create Directories](#4-mkdir--create-directories)
6. [touch – Create Files](#5-touch--create-files)
7. [cp – Copy Files and Directories](#6-cp--copy-files-and-directories)
8. [mv – Move and Rename Files](#7-mv--move-and-rename-files)
9. [rm – Remove Files and Directories](#8-rm--remove-files-and-directories)
10. [find – Search for Files](#9-find--search-for-files)
11. [file – Identify File Types](#10-file--identify-file-types)
12. [stat – File Metadata](#11-stat--file-metadata)
13. [Production Scenario](#-real-world-production-scenario)
14. [Quick Command Reference](#-quick-command-reference)
15. [Interview Questions](#-interview-questions)

---

## 🎯 Introduction

File management is one of the fundamental responsibilities of a Linux administrator and DevOps engineer.

In production environments, Linux servers manage application files, configuration files, logs, backups, and deployment directories.

As a DevOps engineer, understanding file-management commands is essential for:

- Managing application directories.
- Creating and maintaining backups.
- Organizing deployment files.
- Troubleshooting missing files.
- Identifying large files consuming disk space.
- Managing application logs.
- Maintaining production server environments.

Let's understand these commands through practical examples.

---

## 1. `pwd` – Print Working Directory

**Purpose:** Displays the current working directory.

### Syntax

```bash
pwd
```

### Example

```bash
pwd
```

Output:

```text
/home/ubuntu
```

### Real-World Scenario

You connect to an AWS EC2 Linux instance through SSH.

Before modifying application files, you need to confirm your current location.

```bash
ssh ubuntu@192.168.1.10
pwd
```

Output:

```text
/home/ubuntu
```

**DevOps Insight:** Always verify your working directory before executing potentially destructive file-management commands.

---

## 2. `ls` – List Directory Contents

**Purpose:** Displays files and directories.

### Common Options

| Command | Description |
|---|---|
| `ls` | Lists files and directories |
| `ls -l` | Displays detailed file information |
| `ls -a` | Displays hidden files |
| `ls -lh` | Displays human-readable file sizes |
| `ls -lt` | Sorts by modification time, newest first |
| `ls -la` | Displays all files in detailed format |

### Example

```bash
ls -la
```

Output:

```text
drwxr-xr-x  5 ubuntu ubuntu 4096 Nov 21 10:30 .
-rw-r--r--  1 ubuntu ubuntu 2048 Nov 21 10:20 app.py
-rw-------  1 ubuntu ubuntu  256 Nov 21 10:15 .env
```

### Understanding `ls -l`

```text
-rw-r--r-- 1 ubuntu ubuntu 2048 Nov 21 10:20 app.py
```

| Component | Meaning |
|---|---|
| `-` | Regular file |
| `rw-r--r--` | File permissions |
| `1` | Hard-link count |
| `ubuntu` | File owner |
| `ubuntu` | Group |
| `2048` | File size in bytes |
| `Nov 21 10:20` | Modification time |
| `app.py` | Filename |

### Real-World Scenario

Your application is failing because it cannot locate its configuration file.

You suspect the file might be hidden.

```bash
ls -la /var/www/myapp
```

This displays hidden files such as `.env` and `.gitignore`.

**DevOps Insight:** Hidden configuration files are common in application deployments. Use `ls -la` when troubleshooting missing configuration files.

---

## 3. `cd` – Change Directory

**Purpose:** Changes the current working directory.

### Common Examples

```bash
cd /var/www/myapp
```

Navigate to a specific directory.

```bash
cd ..
```

Move to the parent directory.

```bash
cd ~
```

Navigate to the current user's home directory.

```bash
cd -
```

Return to the previous working directory.

```bash
cd /
```

Navigate to the filesystem root.

### Real-World Scenario

You need to inspect application logs on a production server.

```bash
cd /var/log
ls
```

You may find:

```text
syslog
auth.log
kern.log
```

**DevOps Insight:** Linux directory navigation is essential when troubleshooting application and system logs.

---

## 4. `mkdir` – Create Directories

**Purpose:** Creates new directories.

### Syntax

```bash
mkdir directory_name
```

### Example

```bash
mkdir myapp
```

Create multiple directories:

```bash
mkdir logs backups config
```

### Important Option: `-p`

The `-p` option creates missing parent directories.

```bash
mkdir -p /opt/company/backend/logs
```

This creates the complete directory structure even if the parent directories do not exist.

### Real-World Scenario

Your team is preparing a new application deployment.

You need separate directories for application logs, backups, and configurations.

```bash
mkdir -p /opt/company/backend/{logs,backups,config}
```

Result:

```text
backend/
├── logs/
├── backups/
└── config/
```

**DevOps Insight:** `mkdir -p` is frequently used in deployment scripts because it safely handles existing directories and missing parent directories.

---

## 5. `touch` – Create Empty Files

**Purpose:** Creates an empty file if it does not exist or updates timestamps if it already exists.

### Syntax

```bash
touch filename
```

### Examples

Create a file:

```bash
touch app.log
```

Create multiple files:

```bash
touch app.log error.log access.log
```

### Real-World Scenario

Your application requires a log file.

```bash
touch /var/log/myapp/app.log
```

You have now created an empty log file.

### Important Concept

Suppose `app.log` already contains 100 MB of application logs.

Executing:

```bash
touch app.log
```

**Does not delete the existing content.**

It updates the file's timestamps.

**DevOps Insight:** Understand the difference between updating file timestamps and truncating file contents.

---

## 6. `cp` – Copy Files and Directories

**Purpose:** Copies files and directories.

### Copy a File

```bash
cp config.json config.json.bak
```

Creates a backup copy of the configuration file.

### Copy a Directory

```bash
cp -r myapp myapp_backup
```

The `-r` option recursively copies directories and their contents.

### Copy to Another Location

```bash
cp app.log /tmp/
```

Copies the log file into `/tmp`.

### Real-World Scenario

Before modifying a production configuration file, create a backup.

```bash
cp /var/www/myapp/config.json /var/www/myapp/config.json.bak
```

If the new configuration causes problems, you have a separate backup available for recovery.

**DevOps Insight:** Always consider backup and recovery before modifying critical production configuration files. Ensure backups containing secrets have restricted permissions.

---

## 7. `mv` – Move and Rename Files

**Purpose:** Moves files and directories or renames them.

### Rename a File

```bash
mv app_old.py app.py
```

Renames `app_old.py` to `app.py`.

### Move a File

```bash
mv app.log logs/
```

Moves `app.log` into the `logs` directory.

### Real-World Scenario

During application deployment, you need to organize application logs separately from application source files.

```bash
mv /var/www/myapp/app.log /var/www/myapp/logs/
```

The file is moved to the logs directory.

### Important Difference

| Command | Behaviour |
|---|---|
| `cp` | Creates a copy |
| `mv` | Moves or renames the original |

**DevOps Insight:** Moving files can affect application paths and permissions. Check application dependencies before moving production files.

---

## 8. `rm` – Remove Files and Directories

**Purpose:** Deletes files and directories.

⚠️ **Warning:** This is a destructive command. Use it carefully, particularly on production servers.

### Remove a File

```bash
rm app.log
```

### Remove an Empty Directory

```bash
rmdir old_logs
```

### Remove a Directory Recursively

```bash
rm -r old_logs
```

### Force Remove a File

```bash
rm -f old.log
```

### Real-World Scenario

An old application directory is no longer required.

Before deleting it, inspect its contents:

```bash
ls -la /var/www/old_app
```

After confirming that the directory is safe to remove:

```bash
rm -r /var/www/old_app
```

### Important Warning

```bash
rm -rf /var/www/myapp
```

This recursively deletes the application directory without prompting for confirmation.

Potential consequences:

- Application files may be permanently lost.
- Production services may stop working.
- Configuration files may disappear.
- Recovery may require backups or redeployment.

**DevOps Insight:** Always verify the target path and deletion scope before executing `rm -r` or `rm -rf`.

---

## 9. `find` – Search for Files and Directories

**Purpose:** Searches for files and directories based on specified conditions.

### Find a File by Name

```bash
find /var/www -name "config.json"
```

### Find All Log Files

```bash
find /var/log -name "*.log"
```

### Find Directories

```bash
find /var/www -type d
```

### Find Regular Files

```bash
find /var/www -type f
```

### Find Files Larger Than 100 MB

```bash
find /var/log -type f -size +100M
```

### Real-World Scenario

Your production EC2 server is running out of disk space.

You suspect application logs are consuming too much storage.

First, identify large files:

```bash
find /var/log -type f -size +100M
```

Review the results before deciding whether to archive, rotate, or remove old logs.

**DevOps Insight:** `find` is extremely useful for filesystem troubleshooting, log management, and automated maintenance scripts.

---

## 10. `file` – Identify File Types

**Purpose:** Identifies the actual type of a file by examining its contents.

### Example

```bash
file backup
```

Possible output:

```text
backup: gzip compressed data
```

Another example:

```bash
file app.py
```

Possible output:

```text
app.py: Python script, ASCII text executable
```

### Real-World Scenario

You downloaded a backup file from a production server.

Before extracting it, you need to verify its actual file type.

```bash
file backup
```

This helps identify whether the file is a compressed archive, text file, executable, or another format.

**DevOps Insight:** Linux does not rely exclusively on filename extensions to identify file types.

---

## 11. `stat` – Display File Metadata

**Purpose:** Displays detailed information about files and directories.

### Syntax

```bash
stat filename
```

### Example

```bash
stat app.log
```

You can inspect:

- File size.
- Permissions.
- Owner and group.
- Inode number.
- Access time.
- Modification time.
- Change time.

### Important Timestamp Concepts

| Timestamp | Meaning |
|---|---|
| atime | Last access time |
| mtime | Last content modification time |
| ctime | Last metadata or inode-status change time |

### Real-World Scenario

Your application configuration file was unexpectedly modified.

You need to investigate its metadata.

```bash
stat config.json
```

Check the modification timestamp and other metadata to help with troubleshooting.

**Important:** `ctime` is not the file creation time.

---

# 🚀 Real-World Production Scenario

### Situation: Managing an Application on an AWS EC2 Linux Server

Imagine you are a DevOps engineer responsible for deploying a backend application.

Your application directory should look like this:

```text
/opt/company/backend/
├── app.py
├── config/
│   └── config.json
├── logs/
│   └── app.log
└── backups/
```

### Step 1: Check Your Current Location

```bash
pwd
```

### Step 2: Inspect Existing Files

```bash
ls -la
```

### Step 3: Create Application Directories

```bash
mkdir -p /opt/company/backend/{config,logs,backups}
```

### Step 4: Create Application Files

```bash
touch /opt/company/backend/app.py
touch /opt/company/backend/config/config.json
touch /opt/company/backend/logs/app.log
```

### Step 5: Create a Configuration Backup

```bash
cp /opt/company/backend/config/config.json /opt/company/backend/backups/config.json.bak
```

### Step 6: Inspect File Information

```bash
stat /opt/company/backend/config/config.json
```

### Step 7: Find Large Log Files

```bash
find /opt/company/backend/logs -type f -size +100M
```

### Step 8: Verify the Directory Structure

```bash
ls -R /opt/company/backend
```

This workflow demonstrates how Linux file-management commands support everyday DevOps operations.

---

# 📌 Quick Command Reference

| Command | Description |
|---|---|
| `pwd` | Print current working directory |
| `ls` | List directory contents |
| `cd` | Change directory |
| `mkdir` | Create directories |
| `touch` | Create empty files / update timestamps |
| `cp` | Copy files and directories |
| `mv` | Move or rename files |
| `rm` | Remove files |
| `rmdir` | Remove empty directories |
| `find` | Search files and directories |
| `file` | Identify file types |
| `stat` | Display file metadata |

---

# 🎯 Interview Questions

1. What is the difference between `cp` and `mv`?
2. What is the purpose of `mkdir -p`?
3. What happens when you execute `touch` on an existing file?
4. What is the difference between `ls -l` and `ls -la`?
5. How do you find files larger than 100 MB?
6. What is the difference between `rm -r` and `rmdir`?
7. How do you identify the actual type of a file?
8. What is the difference between atime, mtime, and ctime?
9. How would you investigate a production server running out of disk space?
10. Why should you be careful when executing `rm -rf`?

---

## 🏁 Key Takeaway

Linux file management is not just about creating, copying, moving, or deleting files.

For a DevOps engineer, these commands are essential for:

- Production server administration.
- Application deployment.
- Backup and recovery.
- Filesystem troubleshooting.
- Log management.
- Automation.

**Learning approach:** Understand the command, practise it in a Linux environment, and then apply it to a realistic production scenario.

---

**Progress:** Topic 01 — Linux File Management ✅

**Next Topic:** Linux Input/Output Redirection (`stdin`, `stdout`, `stderr`, `>`, `>>`, `<`, `2>`, `2>&1`, and pipes).

*Learning Linux one topic at a time, from fundamentals to production-level DevOps engineering.*
