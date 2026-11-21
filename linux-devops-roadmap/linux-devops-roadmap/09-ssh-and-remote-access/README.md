# 🐧 Linux File Transfer – SSH, SCP, SFTP & RSYNC

> **Linux & DevOps Learning Journey — File Transfer**
>
> **Domain:** DevOps / Cloud Engineering / Linux System Administration  
> **Difficulty:** Beginner to Intermediate  
> **Objective:** Learn how to securely access remote Linux servers, transfer files, and synchronize application data between machines.

---

## 📚 Table of Contents

1. 🔐 [SSH – Secure Shell](#1--ssh--secure-shell)
2. 📂 [SCP – Secure Copy](#2--scp--secure-copy)
3. 📁 [SFTP – SSH File Transfer Protocol](#3--sftp--ssh-file-transfer-protocol)
4. 🔄 [RSYNC – Remote Synchronization](#4--rsync--remote-synchronization)
5. ⚖️ [SSH vs SCP vs SFTP vs RSYNC](#5--comparison--ssh-vs-scp-vs-sftp-vs-rsync)
6. 🚀 [Real-World DevOps Deployment Scenario](#6--real-world-devops-deployment-scenario)
7. 🔒 [Security Best Practices](#7--security-best-practices)
8. 📌 [Quick Command Reference](#8--quick-command-reference)
9. 🎯 [Interview Questions](#9--interview-questions)
10. 🧪 [Hands-On Practice](#10--hands-on-practice)
11. 🏁 [Key Takeaway](#11--key-takeaway)

---

## 🎯 Introduction

In real-world DevOps environments, applications are rarely deployed directly onto the developer's local machine.

Instead, applications are commonly hosted on remote Linux servers, cloud virtual machines, or container infrastructure.

As a DevOps engineer, you need to know how to:

- Connect securely to remote Linux servers.
- Execute commands on remote machines.
- Upload application files.
- Download logs and configuration files.
- Synchronize application directories.
- Transfer data between servers.
- Automate file transfers during deployments.

Linux provides several tools for these operations.

The four important commands covered in this topic are:

| Command | Primary Purpose |
|---|---|
| `ssh` | Secure remote server access |
| `scp` | Secure file copying |
| `sftp` | Interactive file transfer |
| `rsync` | Efficient file synchronization |

---

# 1. 🔐 SSH – Secure Shell

## 1.1 What is SSH?

SSH stands for **Secure Shell**.

It is a network protocol that allows you to securely connect to another computer and execute commands remotely.

SSH encrypts communication between the client and the remote server.

In DevOps, SSH is commonly used to access remote Linux servers such as AWS EC2 instances.

### Real-world scenario

Imagine you are a DevOps engineer working for a company.

Your company hosts an application on an AWS EC2 instance.

You need to:

- Access the production server.
- Check whether the application is running.
- Investigate application errors.
- Restart services.
- Monitor server resources.

You can perform these operations remotely using SSH.

## 1.2 Basic SSH Syntax

```bash
ssh username@hostname
```

**Example:**

```bash
ssh ubuntu@192.168.1.100
```

### Command breakdown

| Component | Explanation |
|---|---|
| `ssh` | Secure Shell command |
| `ubuntu` | Username on the remote server |
| `192.168.1.100` | IP address of the remote server |

### What happens internally?

1. Your local machine initiates an SSH connection.
2. The remote server listens for incoming SSH connections.
3. The client and server negotiate encryption algorithms and establish a secure connection.
4. The server authenticates your identity.
5. Once authenticated, you receive access to the remote terminal.

SSH commonly uses TCP port `22` by default.

## 1.3 Connect Using Your Local Username

```bash
ssh 192.168.1.100
```

When you do not specify a username, SSH attempts to use your current local username.

For example, if your local username is `jasin`, SSH attempts to connect as `jasin`.

**Important:** The remote server must have an account for that username.

## 1.4 Connect Using a Custom Port

**Syntax:**

```bash
ssh -p port username@hostname
```

**Example:**

```bash
ssh -p 2222 ubuntu@192.168.1.100
```

SSH uses port `22` by default.

However, an organization may configure its SSH service to listen on another port, such as `2222`.

In that case, you must specify the correct port.

### Real-world DevOps scenario

Your organization has configured its Linux servers to accept SSH connections on port `2222`.

You try:

```bash
ssh ubuntu@192.168.1.100
```

The connection fails because SSH attempts to connect through port `22`.

Correct command:

```bash
ssh -p 2222 ubuntu@192.168.1.100
```

## 1.5 SSH Key Authentication

SSH supports different authentication methods, including password authentication and public-key authentication.

With public-key authentication:

- The private key stays on your local machine.
- The corresponding public key is configured on the remote server.
- The server verifies that the client possesses the matching private key.

**Example:**

```bash
ssh -i mykey.pem ubuntu@192.168.1.100
```

### Command breakdown

| Component | Meaning |
|---|---|
| `ssh` | Establishes an SSH connection |
| `-i` | Specifies the identity file |
| `mykey.pem` | Private SSH key |
| `ubuntu` | Remote username |
| `192.168.1.100` | Remote server IP |

For AWS EC2, SSH key authentication is commonly used.

### Secure your private key

```bash
chmod 400 mykey.pem
```

This restricts the private key file to read access for its owner.

**Security rule:** Never share your private SSH key or upload it to a public GitHub repository.

### DevOps Insight

SSH is one of the fundamental tools for managing cloud infrastructure.

Even when deployments are automated through CI/CD pipelines, engineers still need to understand SSH for troubleshooting, server administration, and secure remote access.

---

# 2. 📂 SCP – Secure Copy

## 2.1 What is SCP?

SCP stands for **Secure Copy Protocol**.

It is used to securely copy files between local and remote computers through SSH.

SSH provides remote access, whereas SCP is designed for file copying.

### Real-world scenario

Imagine you have developed a Python application on your laptop.

Your company wants you to deploy that application on an AWS EC2 server.

Your project contains:

```text
application/
├── app.py
├── requirements.txt
├── config.py
└── static/
```

Instead of manually recreating these files on the remote server, you can use SCP.

## 2.2 General SCP Syntax

```bash
scp [options] source destination
```

A more general representation is:

```bash
scp [user1@]host1:[path1] [user2@]host2:[path2]
```

Either the source or destination can be your local machine.

## 2.3 Upload a File (Local → Remote)

**Syntax:**

```bash
scp local_file username@remote_host:/remote/path/
```

**Example:**

```bash
scp app.py ubuntu@192.168.1.100:/home/ubuntu/
```

This copies `app.py` from your local machine to the remote server.

### Command breakdown

| Component | Meaning |
|---|---|
| `scp` | Secure copy command |
| `app.py` | Local source file |
| `ubuntu` | Remote username |
| `192.168.1.100` | Remote server |
| `/home/ubuntu/` | Destination directory |

### Real-world use case

You have created a deployment script:

```text
deploy.sh
```

You need to transfer it to the production server.

```bash
scp deploy.sh ubuntu@192.168.1.100:/home/ubuntu/
```

You can then connect through SSH and execute the script, provided it has appropriate permissions.

## 2.4 Download a File (Remote → Local)

Suppose your production application generates logs:

```text
/var/log/application.log
```

You need to download the logs to investigate an issue.

```bash
scp ubuntu@192.168.1.100:/var/log/application.log .
```

The `.` represents your current local working directory.

### Real-world use case

Your production application suddenly stops responding.

You need to investigate its logs locally.

You can download the log file using SCP and analyze it.

## 2.5 Copy Files Between Remote Servers

```bash
scp alice@server1:/home/alice/file.txt bob@server2:/home/bob/
```

This transfers a file from one remote server to another.

Depending on the SCP implementation and options, the transfer may pass through your local machine or be performed through a direct remote-to-remote transfer.

## 2.6 Transfer Using a Custom Port

```bash
scp -P 2222 app.py ubuntu@192.168.1.100:/home/ubuntu/
```

The uppercase `-P` specifies the SSH port used by SCP.

**Important difference:**

| Command | Port Option |
|---|---|
| SSH | `-p` |
| SCP | `-P` |
| SFTP | `-P` |

Remember that SCP uses uppercase `-P`, while SSH uses lowercase `-p`.

## 2.7 Transfer Entire Directories

```bash
scp -r project/ ubuntu@192.168.1.100:/home/ubuntu/
```

The `-r` option means recursive.

It copies the directory and its contents, including subdirectories.

### Real-world use case

You have an entire web application containing:

- Source code.
- Configuration files.
- Static resources.
- Deployment scripts.

You can transfer the complete directory using:

```bash
scp -r project/ ubuntu@192.168.1.100:/home/ubuntu/
```

### DevOps Insight

SCP is useful for straightforward file transfers, but repeatedly copying large application directories can be inefficient.

For frequent deployments where only a few files change, RSYNC is often a better choice.

---

# 3. 📁 SFTP – SSH File Transfer Protocol

## 3.1 What is SFTP?

SFTP stands for **SSH File Transfer Protocol**.

It allows you to securely transfer and manage files on remote systems through an interactive interface.

Unlike a basic SCP transfer, SFTP allows you to connect and perform multiple file operations within the same session.

SFTP operates over SSH and commonly uses TCP port `22`.

### Real-world scenario

Imagine your company has a remote Linux server where application reports are stored.

You need to:

- Browse remote directories.
- Download reports.
- Upload configuration files.
- Navigate between folders.
- Manage files without repeatedly opening SSH connections.

SFTP is useful for this workflow.

## 3.2 Connect to a Remote Server

**Syntax:**

```bash
sftp username@hostname
```

**Example:**

```bash
sftp ubuntu@192.168.1.100
```

Once connected, you enter the SFTP interactive interface.

## 3.3 Connect Using Your Local Username

```bash
sftp 192.168.1.100
```

If no username is specified, SFTP attempts to use your local username.

## 3.4 Connect Using a Custom Port

```bash
sftp -P 2222 ubuntu@192.168.1.100
```

Here, uppercase `-P` specifies the port.

## 3.5 Important SFTP Commands

These commands are executed inside the SFTP session.

| Command | Description |
|---|---|
| `ls` | List remote files |
| `pwd` | Display remote working directory |
| `cd` | Change remote directory |
| `lls` | List local files |
| `lpwd` | Display local working directory |
| `lcd` | Change local working directory |
| `get file` | Download remote file |
| `put file` | Upload local file |
| `mkdir dir` | Create remote directory |
| `rm file` | Remove remote file |
| `rename old new` | Rename remote file |
| `exit` | Exit SFTP |

## 3.6 Practical SFTP Workflow

**Step 1: Connect**

```bash
sftp ubuntu@192.168.1.100
```

**Step 2: Check the remote directory**

```bash
pwd
```

**Step 3: List remote files**

```bash
ls
```

**Step 4: Navigate to the application directory**

```bash
cd /var/www/
```

**Step 5: Upload a local file**

```bash
put app.py
```

**Step 6: Download a remote log**

```bash
get application.log
```

**Step 7: Exit**

```bash
exit
```

## 3.7 Upload and Download Directories

SFTP also supports directory transfers.

Upload a directory:

```bash
put -r project/
```

Download a directory:

```bash
get -r logs/
```

The `-r` option performs recursive transfers.

### Real-world use case

Suppose a business application exchanges daily reports with a remote server.

An administrator can use SFTP to upload new reports and download processed reports securely.

**Important:** SFTP is a file-transfer protocol, not the same thing as an interactive SSH shell. Although both use SSH infrastructure, they provide different interfaces and capabilities.

### DevOps Insight

SFTP is useful when external teams or applications need controlled file-transfer access without requiring a general-purpose interactive shell.

---

# 4. 🔄 RSYNC – Remote Synchronization

## 4.1 What is RSYNC?

Rsync is a Linux utility used to synchronize files and directories between two locations.

It is particularly useful when dealing with large directories and frequent updates.

One of its main advantages is that it can avoid retransferring unchanged file data.

Rsync can work:

- Between two local directories.
- From a local machine to a remote server.
- From a remote server to a local machine.
- Between remote systems through supported remote shells.

### Real-world scenario

Imagine your company hosts a website on an AWS EC2 instance.

Your development team updates the application.

The project contains 1,000 files, but only 5 files have changed.

Using a basic copying approach may transfer all the files again.

Rsync can efficiently identify and transfer necessary changes.

This makes it useful for:

- Application deployments.
- Backups.
- Server synchronization.
- Website updates.
- Data migration.

## 4.2 Basic Syntax

```bash
rsync [options] source destination
```

## 4.3 Archive Mode – `-a`

```bash
rsync -a source destination
```

The `-a` option enables archive mode.

It preserves important file attributes, including:

- Symbolic links.
- Permissions.
- Modification timestamps.
- Directory structures.
- Ownership and group information where permitted.

**Example:**

```bash
rsync -a /home/jasin/project/ /backup/project/
```

This synchronizes the contents of the source directory with the destination.

**Important:** Preserving ownership and group information may require elevated privileges and depends on the destination filesystem and permissions.

## 4.4 Verbose Mode – `-v`

```bash
rsync -av source destination
```

The `-v` option means verbose.

It displays information about files being processed and transferred.

Useful when you want to monitor synchronization.

## 4.5 Compression – `-z`

```bash
rsync -avz source destination
```

The `-z` option enables compression during data transfer.

### Understanding the combination

| Option | Meaning |
|---|---|
| `-a` | Archive mode |
| `-v` | Verbose output |
| `-z` | Compress data during transfer |

## 4.6 Local to Remote Synchronization

```bash
rsync -avz /home/jasin/project/ ubuntu@192.168.1.100:/var/www/project/
```

This synchronizes your local application directory with a remote server.

### Real-world DevOps scenario

Your development team releases an updated application.

You need to synchronize the application files with the production server.

Rsync can transfer the changed files without blindly copying the entire directory.

## 4.7 Remote to Local Synchronization

```bash
rsync -avz ubuntu@192.168.1.100:/var/log/ /home/jasin/logs/
```

This synchronizes remote log files to your local directory.

### Real-world use case

A production server generates application logs.

You need to collect those logs locally for debugging and analysis.

Rsync provides a convenient way to synchronize them.

## 4.8 Understanding Trailing Slashes

This is an important concept in RSYNC.

Consider:

```bash
rsync -av project/ backup/
```

This copies the contents of `project` into `backup`.

Now consider:

```bash
rsync -av project backup/
```

This copies the `project` directory itself into `backup`.

The trailing slash changes how the source directory is handled.

## 4.9 Custom SSH Port with RSYNC

When the remote SSH server uses a non-default port, specify the port through the remote shell option.

**Example:**

```bash
rsync -avz -e "ssh -p 2222" project/ ubuntu@192.168.1.100:/var/www/project/
```

Here:

- `-e` specifies the remote shell command.
- `ssh -p 2222` tells RSYNC to use SSH port `2222`.

## 4.10 Important Advanced Option – `--delete`

```bash
rsync -av --delete source/ destination/
```

The `--delete` option removes files from the destination that no longer exist in the source.

### Real-world scenario

Your production website contains outdated files.

Your source directory represents the desired application state.

You want the destination to match the source, including removing obsolete files.

You can use `--delete`.

**Warning:** This option can permanently delete destination data.

Always verify source and destination paths, and test with `--dry-run` before using it in production.

**Preview the changes:**

```bash
rsync -av --delete --dry-run source/ destination/
```

The `--dry-run` option previews proposed changes without performing them.

## 4.11 Additional Useful RSYNC Options

| Option | Description |
|---|---|
| `-a` | Archive mode |
| `-v` | Verbose output |
| `-z` | Compress data during transfer |
| `-r` | Recursive directory transfer |
| `-h` | Human-readable numbers |
| `--progress` | Display transfer progress |
| `--delete` | Delete destination files absent from source |
| `--dry-run` | Preview changes without applying them |
| `-e` | Specify a remote shell command |

**Example with progress:**

```bash
rsync -avz --progress project/ ubuntu@192.168.1.100:/var/www/project/
```

### DevOps Insight

Rsync is especially useful for recurring synchronization tasks.

However, it is not a complete deployment system by itself. Production deployments should also consider release versioning, backups, rollback strategies, permissions, and application health checks.

---

# 5. ⚖️ Comparison – SSH vs SCP vs SFTP vs RSYNC

| Command | Main Purpose | Typical Use |
|---|---|---|
| SSH | Remote access | Managing servers |
| SCP | File copying | Uploading application files |
| SFTP | Interactive file transfer | Managing remote files |
| RSYNC | File synchronization | Deployments and backups |

### Which command should a DevOps engineer use?

**SSH:** When you need to access and manage a remote server.

**SCP:** When you need to copy a file or directory quickly.

**SFTP:** When you need an interactive session to upload, download, and manage files.

**RSYNC:** When you need to synchronize directories efficiently, particularly when files change frequently.

---

# 6. 🚀 Real-World DevOps Deployment Scenario

## Scenario: Deploying an Application to AWS EC2

Imagine you are a DevOps engineer.

Your application is developed locally and hosted on an AWS EC2 Linux instance.

Your objective is to transfer the application and verify the deployment.

### Step 1: Connect to the EC2 Server

```bash
ssh -i mykey.pem ubuntu@server-ip
```

### Step 2: Verify the Server

```bash
hostname
whoami
uptime
```

### Step 3: Exit the Server

```bash
exit
```

### Step 4: Transfer Application Files

```bash
scp -r ./project ubuntu@server-ip:/home/ubuntu/
```

### Step 5: Connect Again

```bash
ssh ubuntu@server-ip
```

### Step 6: Verify the Transferred Files

```bash
ls -la /home/ubuntu/project/
```

### Step 7: Synchronize Future Updates

From your local machine:

```bash
rsync -avz ./project/ ubuntu@server-ip:/home/ubuntu/project/
```

This workflow demonstrates how SSH, SCP, and RSYNC can work together.

### Production Considerations

This is a simplified deployment workflow.

Real production deployments should also consider:

- Release directories.
- Rollback strategies.
- File and directory permissions.
- Secrets management.
- Application health checks.
- CI/CD automation.
- Backups before destructive synchronization.

**DevOps Insight:**

In modern infrastructure, these operations are often automated using tools such as Jenkins, GitLab CI/CD, GitHub Actions, Ansible, and deployment pipelines.

Understanding the underlying Linux commands helps you troubleshoot those automated workflows.

---

# 7. 🔒 Security Best Practices

Secure file transfer is an important responsibility when managing production infrastructure.

### 7.1 Protect Private SSH Keys

```bash
chmod 400 mykey.pem
```

Never upload private keys to public repositories or share them with unauthorized users.

### 7.2 Use Individual User Accounts

Avoid sharing one Linux account among multiple engineers.

Individual accounts improve accountability and access management.

### 7.3 Use Least Privilege

Grant users only the permissions required for their responsibilities.

Avoid transferring application files into sensitive system directories without understanding ownership and permissions.

### 7.4 Verify Remote Destinations

Before executing file-transfer commands, verify:

- Remote hostname or IP.
- Remote username.
- Destination directory.
- File ownership requirements.

### 7.5 Be Careful with RSYNC `--delete`

Always preview destructive synchronization operations:

```bash
rsync -av --delete --dry-run source/ destination/
```

Verify the output before removing `--dry-run`.

### 7.6 Avoid Exposing Sensitive Files

Do not transfer passwords, private keys, production credentials, or secret configuration files unnecessarily.

Use an appropriate secrets-management solution for sensitive application configuration.

---

# 8. 📌 Quick Command Reference

| Command | Purpose |
|---|---|
| `ssh user@host` | Connect to remote server |
| `ssh -p 2222 user@host` | Connect using custom SSH port |
| `ssh -i key.pem user@host` | Connect using private key |
| `scp file user@host:/path/` | Upload file |
| `scp user@host:/path/file .` | Download file |
| `scp -P 2222 file user@host:/path/` | SCP using custom port |
| `scp -r dir user@host:/path/` | Copy directory recursively |
| `sftp user@host` | Start SFTP session |
| `sftp -P 2222 user@host` | SFTP using custom port |
| `put file` | Upload file in SFTP |
| `get file` | Download file in SFTP |
| `rsync -a source destination` | Synchronize in archive mode |
| `rsync -avz source destination` | Verbose compressed synchronization |
| `rsync -avz --progress source destination` | Synchronize with progress |
| `rsync -av --delete source/ destination/` | Synchronize and remove obsolete destination files |
| `rsync -av --dry-run source/ destination/` | Preview synchronization |

---

# 9. 🎯 Interview Questions

## Basic Level

**Q1. What is SSH, and why is it used?**

SSH is a secure network protocol used to remotely access another computer and execute commands through an encrypted connection.

**Q2. What is the default SSH port?**

The default SSH port is TCP `22`.

**Q3. What is the difference between SSH and SCP?**

- SSH provides remote shell access.
- SCP securely copies files between systems.

**Q4. What is the difference between SCP and SFTP?**

- SCP is primarily designed for file copying.
- SFTP provides an interactive file-management and transfer session.

**Q5. What does the `-r` option do in SCP?**

It enables recursive copying of directories and their contents.

## Intermediate Level

**Q6. Why does SCP use uppercase `-P` while SSH uses lowercase `-p`?**

They are different command-line options defined by their respective utilities.

- SSH uses `-p` for the port.
- SCP uses `-P` for the port.

**Q7. What is the purpose of RSYNC?**

Rsync synchronizes files and directories efficiently between locations, often avoiding unnecessary data transfers.

**Q8. Explain `rsync -avz`.**

- `-a`: Archive mode.
- `-v`: Verbose output.
- `-z`: Compress data during transfer.

Together, these options provide archive-style synchronization with detailed output and compression.

**Q9. What is the difference between `rsync -a` and `rsync -avz`?**

`rsync -a` uses archive mode.

`rsync -avz` additionally enables verbose output and compression.

**Q10. What is the difference between `project/` and `project` in RSYNC?**

- `project/` copies the contents of the directory.
- `project` copies the directory itself into the destination.

## Senior DevOps Scenario-Based Questions

**Q11. You have to transfer a 10 GB directory to another server. Which tool would you consider and why?**

I would consider RSYNC because it supports efficient synchronization, compression, and resumable workflows when used with appropriate options.

For a large transfer that may be interrupted, I would also consider:

```bash
rsync -avz --partial --progress project/ user@server:/backup/project/
```

The `--partial` option keeps partially transferred files so a subsequent run can continue more efficiently.

**Q12. Your application has 1,000 files, but only 10 have changed. How would you synchronize the updates efficiently?**

I would use RSYNC:

```bash
rsync -avz project/ user@server:/var/www/project/
```

It compares source and destination files and transfers the required changes.

**Q13. Your SSH connection fails because the server uses a different port. How would you troubleshoot it?**

First, verify the configured SSH port and whether the server is listening on it.

Then connect using:

```bash
ssh -p 2222 ubuntu@192.168.1.100
```

I would also inspect firewall rules, cloud security groups, routing, and SSH service status.

**Q14. How would you securely authenticate to an AWS EC2 instance?**

I would use the configured EC2 key pair and the appropriate Linux username.

```bash
ssh -i mykey.pem ubuntu@server-ip
```

I would ensure that the private key is protected and that SSH access is restricted appropriately.

**Q15. How would you synchronize a production directory while ensuring outdated destination files are removed safely?**

I would first preview the operation:

```bash
rsync -av --delete --dry-run source/ destination/
```

After verifying that the source and destination are correct and the proposed deletions are expected, I would run:

```bash
rsync -av --delete source/ destination/
```

I would also ensure a backup or rollback strategy exists.

---

# 10. 🧪 Hands-On Practice

Complete these exercises using a Linux virtual machine or two test Linux systems.

### SSH Practice

- [ ] Connect to a remote server using `ssh`.
- [ ] Connect using a specific username.
- [ ] Connect using a custom SSH port.
- [ ] Authenticate using an SSH private key.
- [ ] Verify your remote username using `whoami`.
- [ ] Exit the remote session.

### SCP Practice

- [ ] Upload a text file to a remote server.
- [ ] Download a remote file to your local machine.
- [ ] Copy an entire directory using `scp -r`.
- [ ] Transfer a file using a custom SSH port.
- [ ] Verify that transferred files exist on the destination.

### SFTP Practice

- [ ] Connect to a remote server using SFTP.
- [ ] Display the remote working directory.
- [ ] List remote files.
- [ ] Change the remote directory.
- [ ] List local files using `lls`.
- [ ] Upload a file using `put`.
- [ ] Download a file using `get`.
- [ ] Exit the SFTP session.

### RSYNC Practice

- [ ] Synchronize two local directories.
- [ ] Use archive mode with `-a`.
- [ ] Use verbose mode with `-v`.
- [ ] Enable compression using `-z`.
- [ ] Synchronize a local directory to a remote server.
- [ ] Synchronize remote files to your local machine.
- [ ] Observe the difference between `source/` and `source`.
- [ ] Preview changes using `--dry-run`.
- [ ] Test `--delete` using disposable test directories.

---

# 11. 🏁 Key Takeaway

Linux file-transfer commands are essential for remote server administration and DevOps deployment workflows.

After completing this topic, you should understand how to:

- Securely access remote Linux servers using SSH.
- Authenticate using SSH keys.
- Transfer individual files and directories using SCP.
- Manage remote files interactively using SFTP.
- Synchronize application directories efficiently using RSYNC.
- Understand the difference between file copying and synchronization.
- Apply security practices when transferring production data.

**Remember:**

- SSH = Remote access.
- SCP = Secure file copying.
- SFTP = Interactive file transfer.
- RSYNC = Efficient file synchronization.

A strong DevOps engineer should understand not only how to execute these commands, but also when to use each one in a real production environment.

---

**End of Topic: Linux File Transfer**

**Next Step:** Practice these commands in a Linux lab and complete the scenario-based exercises before moving to the next topic.
