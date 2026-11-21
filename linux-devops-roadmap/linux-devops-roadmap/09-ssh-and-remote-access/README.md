# Linux File Transfer – SSH, SCP, SFTP & RSYNC

> **Topic:** Linux Administration  
> **Domain:** DevOps / Cloud Engineering / Linux System Administration  
> **Difficulty:** Beginner to Intermediate  
> **Objective:** Learn how to securely access remote Linux servers, transfer files, and synchronize application data between machines.

---

# 1. SSH – Secure Shell

## 1.1 What is SSH?

SSH (Secure Shell) is a network protocol that allows you to securely connect to and execute commands on another computer over a network.

In DevOps, applications are commonly deployed on remote Linux servers such as AWS EC2 instances.

You don't physically access these servers. Instead, you connect remotely using SSH.

### Real-world scenario

Imagine you are a DevOps engineer working for a company.

Your company hosts an application on an AWS EC2 instance.

You need to:
- Access the production server.
- Check whether the application is running.
- Investigate errors.
- Restart services.
- Monitor server resources.

You can perform these tasks remotely using SSH.

### Basic syntax

```bash
ssh username@hostname
```

Example:

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
2. The remote server listens for SSH connections.
3. The server and client establish an encrypted connection.
4. The server authenticates your identity.
5. Once authenticated, you receive access to the remote terminal.

SSH commonly uses port `22`.

## 1.2 Connect using your local username

```bash
ssh access
```

When you don't specify a username, SSH attempts to use your current local username.

Example:

```bash
ssh 192.168.1.100
```

If your local username is `jasin`, SSH attempts to connect as `jasin`.

**Important:** The remote server must have an account for that username.

## 1.3 Connect using a custom port

```bash
ssh -p port username@hostname
```

Example:

```bash
ssh -p 2222 ubuntu@192.168.1.100
```

### Why do we need this?

SSH uses port `22` by default.

However, an organization may configure SSH to listen on another port, such as `2222`.

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

### Important SSH security concepts

**Password authentication:**

The server asks for your account password.

**SSH key authentication:**

You use a private key on your local machine and a corresponding public key configured on the server.

Example:

```bash
ssh -i mykey.pem ubuntu@192.168.1.100
```

Here:
- `-i` specifies the identity file.
- `mykey.pem` is your private key.
- `ubuntu` is the remote username.

For AWS EC2, SSH key authentication is commonly used.

**Security rule:** Never share your private SSH key. Restrict its permissions:

```bash
chmod 400 mykey.pem
```

---

# 2. SCP – Secure Copy Protocol

## 2.1 What is SCP?

SCP is used to securely copy files between local and remote computers through SSH.

SSH provides remote access, whereas SCP is designed for file copying.

### Real-world scenario

Imagine you have developed a Python application on your laptop.

Your company wants you to deploy that application on an AWS EC2 server.

You need to transfer:

```text
application/
├── app.py
├── requirements.txt
├── config.py
└── static/
```

Instead of manually recreating these files on the remote server, you can use SCP.

## 2.2 General syntax

```bash
scp [user1@]host1:[path1] [user2@]host2:[path2]
```

There are two sides:

- `host1`: Source machine.
- `path1`: Source file or directory.
- `host2`: Destination machine.
- `path2`: Destination location.

Either side can be your local machine.

## 2.3 Upload a file (Local → Remote)

```bash
scp app.py ubuntu@192.168.1.100:/home/ubuntu/
```

### Explanation

| Component | Meaning |
|---|---|
| `scp` | Secure copy |
| `app.py` | Local source file |
| `ubuntu` | Remote username |
| `192.168.1.100` | Remote server |
| `/home/ubuntu/` | Destination directory |

After execution, `app.py` is available on the remote server.

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

## 2.4 Download a file (Remote → Local)

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

## 2.5 Copy files between remote servers

```bash
scp alice@server1:/home/alice/file.txt bob@server2:/home/bob/
```

This transfers a file from one remote server to another.

Depending on SCP implementation and options, the transfer may pass through your local machine.

## 2.6 Transfer using a custom port

```bash
scp -P 2222 app.py ubuntu@192.168.1.100:/home/ubuntu/
```

**Important difference:**

| Command | Port option |
|---|---|
| SSH | `-p` |
| SCP | `-P` |

SCP uses uppercase `-P`.

## 2.7 Transfer entire directories

```bash
scp -r project/ ubuntu@192.168.1.100:/home/ubuntu/
```

The `-r` option means recursive.

It copies the directory and its contents, including subdirectories.

### Real-world use case

You have an entire web application containing:

- Source code
- Configuration files
- Static resources
- Deployment scripts

You can transfer the complete directory using `scp -r`.

---

# 3. SFTP – SSH File Transfer Protocol

## 3.1 What is SFTP?

SFTP stands for SSH File Transfer Protocol.

It allows you to securely transfer and manage files on remote systems through an interactive interface.

Unlike a basic SCP transfer, SFTP allows you to connect and perform multiple file operations within the same session.

### Real-world scenario

Imagine your company has a remote Linux server where application reports are stored.

You need to:
- Browse remote directories.
- Download reports.
- Upload configuration files.
- Navigate between folders.
- Manage files without repeatedly opening SSH connections.

SFTP is useful for this workflow.

## 3.2 Connect to a remote server

```bash
sftp ubuntu@192.168.1.100
```

Once connected, you enter the SFTP interactive interface.

## 3.3 Connect using your local username

```bash
sftp 192.168.1.100
```

If no username is specified, SFTP attempts to use your local username.

## 3.4 Connect using a custom port

```bash
sftp -P 2222 ubuntu@192.168.1.100
```

Here, uppercase `-P` specifies the port.

## 3.5 Important SFTP commands

These commands are executed inside the SFTP session.

| Command | Description |
|---|---|
| `ls` | List remote files |
| `pwd` | Display remote working directory |
| `cd` | Change remote directory |
| `lls` | List local files |
| `lpwd` | Display local working directory |
| `lcd` | Change local directory |
| `get file` | Download remote file |
| `put file` | Upload local file |
| `mkdir dir` | Create remote directory |
| `rm file` | Remove remote file |
| `rename old new` | Rename remote file |
| `exit` | Exit SFTP |

## 3.6 Practical SFTP workflow

Connect:

```bash
sftp ubuntu@192.168.1.100
```

Check the remote directory:

```bash
pwd
```

List files:

```bash
ls
```

Navigate to the application directory:

```bash
cd /var/www/
```

Upload a local file:

```bash
put app.py
```

Download a remote log:

```bash
get application.log
```

Exit:

```bash
exit
```

### Real-world use case

Suppose a business application exchanges daily reports with a remote server.

An administrator can use SFTP to upload new reports and download processed reports securely.

**Important:** SFTP is a file-transfer protocol, not the same thing as an interactive SSH shell.

---

# 4. RSYNC – Remote Synchronization

## 4.1 What is RSYNC?

Rsync is a Linux utility used to synchronize files and directories between two locations.

It is particularly useful when dealing with large directories and frequent updates.

One of its main advantages is that it can avoid retransferring unchanged file data.

### Real-world scenario

Imagine your company hosts a website on an AWS EC2 instance.

Your development team updates the application.

The project contains 1,000 files, but only 5 files have changed.

Using a basic copying approach may transfer all the files again.

Rsync can efficiently identify and transfer the necessary changes.

This makes it useful for:
- Application deployments.
- Backups.
- Server synchronization.
- Website updates.
- Data migration.

## 4.2 Basic syntax

```bash
rsync [options] source destination
```

## 4.3 Archive mode – `-a`

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

Example:

```bash
rsync -a /home/jasin/project/ /backup/project/
```

This synchronizes the contents of the source directory with the destination.

## 4.4 Verbose mode – `-v`

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

## 4.6 Local to Remote synchronization

```bash
rsync -avz /home/jasin/project/ ubuntu@192.168.1.100:/var/www/project/
```

This synchronizes your local application directory with a remote server.

### Real-world DevOps scenario

Your development team releases an updated application.

You need to synchronize the application files with the production server.

Rsync can transfer the changed files without blindly copying the entire directory.

## 4.7 Remote to Local synchronization

```bash
rsync -avz ubuntu@192.168.1.100:/var/log/ /home/jasin/logs/
```

This synchronizes remote log files to your local directory.

### Real-world use case

A production server generates application logs.

You need to collect those logs locally for debugging and analysis.

Rsync provides a convenient way to synchronize them.

## 4.8 Understanding trailing slashes

This is an important concept.

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

## 4.9 Important advanced option – `--delete`

```bash
rsync -av --delete source/ destination/
```

The `--delete` option removes files from the destination that no longer exist in the source.

### Real-world scenario

Your production website contains outdated files.

Your source directory represents the desired application state.

You want the destination to match the source, including removing obsolete files.

You can use `--delete`.

**Warning:** This option can permanently delete destination data. Always verify source and destination paths, and test with `--dry-run` before using it in production.

Example:

```bash
rsync -av --delete --dry-run source/ destination/
```

`--dry-run` previews the proposed changes without performing them.

---

# 5. Comparison – SSH vs SCP vs SFTP vs RSYNC

| Command | Main purpose | Typical use |
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

# 6. Practical DevOps Workflow

Let's combine these concepts into a realistic deployment scenario.

### Situation

You are a DevOps engineer.

Your application is developed locally and hosted on an AWS EC2 Linux instance.

Your objective is to transfer the application and verify the deployment.

### Step 1: Connect to the EC2 server

```bash
ssh -i mykey.pem ubuntu@server-ip
```

### Step 2: Verify the server

```bash
hostname
whoami
uptime
```

### Step 3: Exit the server

```bash
exit
```

### Step 4: Transfer application files

```bash
scp -r ./project ubuntu@server-ip:/home/ubuntu/
```

### Step 5: Connect again

```bash
ssh ubuntu@server-ip
```

### Step 6: Verify the transferred files

```bash
ls -la /home/ubuntu/project/
```

### Step 7: Synchronize future updates

From your local machine:

```bash
rsync -avz ./project/ ubuntu@server-ip:/home/ubuntu/project/
```

This workflow demonstrates how SSH, SCP, and RSYNC can work together.

**Production note:** This is a simplified deployment workflow. Real production deployments should also consider release directories, rollback strategies, permissions, secrets management, health checks, and CI/CD automation.

---

# 7. Important Interview Questions

### Basic questions

1. What is SSH, and why is it used?
2. What is the default SSH port?
3. What is the difference between SSH and SCP?
4. What is the difference between SCP and SFTP?
5. What does the `-r` option do in SCP?

### Intermediate questions

6. Why does SCP use uppercase `-P` while SSH uses lowercase `-p`?
7. What is the purpose of RSYNC?
8. Explain `rsync -avz`.
9. What is the difference between `rsync -a` and `rsync -avz`?
10. What is the difference between `project/` and `project` in RSYNC?

### Senior DevOps scenarios

11. You have to transfer a 10 GB directory to another server. Which tool would you consider and why?
12. Your application has 1,000 files, but only 10 have changed. How would you synchronize the updates efficiently?
13. Your SSH connection fails because the server uses a different port. How would you troubleshoot it?
14. How would you securely authenticate to an AWS EC2 instance?
15. How would you synchronize a production directory while ensuring outdated destination files are removed safely?

---

# 8. Important Commands Cheat Sheet

| Command | Purpose |
|---|---|
| `ssh user@host` | Connect to remote server |
| `ssh -p 2222 user@host` | Connect using custom port |
| `ssh -i key.pem user@host` | Connect using private key |
| `scp file user@host:/path/` | Upload file |
| `scp user@host:/path/file .` | Download file |
| `scp -P 2222 file user@host:/path/` | SCP using custom port |
| `scp -r dir user@host:/path/` | Copy directory recursively |
| `sftp user@host` | Start SFTP session |
| `sftp -P 2222 user@host` | SFTP using custom port |
| `rsync -a source destination` | Synchronize in archive mode |
| `rsync -avz source destination` | Verbose compressed synchronization |
| `rsync -av --delete source/ destination/` | Synchronize and remove obsolete destination files |

---

# 9. Learning Outcome

After completing this topic, you should understand:

- How to remotely access Linux servers using SSH.
- How to authenticate using SSH keys.
- How to transfer files using SCP.
- How to interactively manage remote files using SFTP.
- How to synchronize application directories using RSYNC.
- How these commands are used in real-world DevOps deployment workflows.

**End of Topic: Linux File Transfer**

Next: Practice these commands in a Linux lab and complete scenario-based exercises before moving to the next topic.
