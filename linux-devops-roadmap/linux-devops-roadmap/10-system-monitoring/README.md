# 🐧 Linux Process Management, System Monitoring & User Management

> **Topic:** Linux System Administration  
> **Domain:** DevOps / Cloud Engineering / Linux Administration  
> **Difficulty:** Beginner to Advanced  
> **Objective:** Learn how to manage background processes, inspect running applications, monitor system resources, troubleshoot performance issues, capture network traffic, and manage Linux users and groups.

---

## 📚 Table of Contents

1. [Introduction](#-introduction)
2. [Background Processes](#1-background-processes)
3. [Viewing Running Processes](#2-viewing-running-processes)
4. [Monitoring Processes](#3-monitoring-processes)
5. [Terminating Processes](#4-terminating-processes)
6. [Open Files and Process Investigation](#5-open-files-and-process-investigation)
7. [CPU and Memory Monitoring](#6-cpu-and-memory-monitoring)
8. [System Logs](#7-system-logs)
9. [Network Traffic Monitoring](#8-network-traffic-monitoring)
10. [Continuous System Monitoring](#9-continuous-system-monitoring)
11. [User Management](#10-user-management)
12. [Real-World DevOps Scenario](#-real-world-devops-scenario)
13. [Production Best Practices](#-production-best-practices)
14. [Quick Command Reference](#-quick-command-reference)
15. [Interview Questions](#-interview-questions)
16. [Hands-On Practice](#-hands-on-practice)
17. [Key Takeaways](#-key-takeaways)

---

## 🎯 Introduction

Linux process management is one of the most important skills for a DevOps engineer.

Every application running on a Linux server creates one or more processes.

For example:

- Nginx runs as a process to serve web traffic.
- MySQL runs as a process to manage database operations.
- Docker runs processes to manage containers.
- Java applications run as processes inside the JVM.
- Background scripts execute scheduled automation tasks.

As a DevOps engineer, you must know how to:

- Identify running processes.
- Find applications consuming excessive CPU or memory.
- Stop unresponsive processes.
- Investigate files opened by processes.
- Monitor CPU, memory, and disk I/O.
- Inspect system logs.
- Troubleshoot network traffic.
- Manage users and groups securely.

---

# 1. Background Processes

## 1.1 The `&` Operator

The `&` character runs a command in the background.

Normally, when you execute a command, your terminal waits until that command finishes.

However, adding `&` allows the terminal to return immediately while the process continues running.

### Syntax

```bash
command &
```

### Example

```bash
sleep 100 &
```

This starts a process that sleeps for 100 seconds in the background.

Example output:

```text
[1] 2456
```

Here:

| Output | Meaning |
|---|---|
| `[1]` | Shell job number |
| `2456` | Process ID (PID) |

### Another Example

```bash
python3 app.py &
```

This starts a Python application in the background.

You can continue executing other commands in the same terminal.

### Useful Background Job Commands

```bash
jobs
```

Displays background jobs managed by the current shell.

```bash
fg %1
```

Brings job number 1 into the foreground.

```bash
bg %1
```

Resumes a suspended job in the background.

### Real-World DevOps Scenario

Suppose you need to run a data-processing script while performing other server administration tasks.

Instead of keeping your terminal occupied:

```bash
python3 data_processing.py
```

Run:

```bash
python3 data_processing.py &
```

**Important:** A process started with `&` is not automatically independent of your terminal session. It may receive a hangup signal when the session closes.

For persistent jobs, consider:

```bash
nohup python3 app.py > app.log 2>&1 &
```

For production services, prefer `systemd` or an appropriate process supervisor instead of manually backgrounding applications.

---

# 2. Viewing Running Processes

## 2.1 The `ps` Command

`ps` stands for Process Status.

It displays information about currently running processes.

### Syntax

```bash
ps [options]
```

### Example 1: Display Processes

```bash
ps
```

Displays processes associated with the current terminal session.

### Example 2: Display All Processes

```bash
ps aux
```

Displays processes from all users, including processes without an associated terminal.

Breakdown:

| Option | Meaning |
|---|---|
| `a` | Show processes associated with terminals across users |
| `u` | Display user-oriented information |
| `x` | Include processes without controlling terminals |

### Example 3: Another Way to Display All Processes

```bash
ps -e
```

or:

```bash
ps -A
```

Both display processes across the system.

### Example 4: Detailed Process Overview

```bash
ps -ef
```

Displays a full-format process listing.

Important columns:

| Column | Meaning |
|---|---|
| UID | User running the process |
| PID | Process ID |
| PPID | Parent Process ID |
| C | CPU utilization indicator |
| STIME | Process start time |
| TTY | Associated terminal |
| TIME | CPU time consumed |
| CMD | Command that started the process |

### Example 5: Display Processes Running Under Root

```bash
ps -U root -u root
```

Displays processes associated with the root account.

### Example 6: Display Selected Columns

```bash
ps -eo pid,user,command
```

Displays only the PID, username, and command.

| Option | Meaning |
|---|---|
| `-e` | Select all processes |
| `-o` | Specify output columns |
| `pid` | Process ID |
| `user` | Process owner |
| `command` | Command used to start the process |

### Example 7: Find a Particular Process

```bash
ps aux | grep nginx
```

Searches the process listing for `nginx`.

For a cleaner search, you can also use:

```bash
pgrep -a nginx
```

**Senior DevOps Insight:** `grep` can sometimes match its own search command. `pgrep` is often more convenient when you only need to identify processes by name.

---

# 3. Monitoring Processes

## 3.1 The `top` Command

`top` provides real-time information about running processes and system resource utilization.

### Syntax

```bash
top
```

It displays:

- CPU utilization.
- Memory utilization.
- Running processes.
- Process IDs.
- Process owners.
- CPU consumption per process.
- Memory consumption per process.

### Important Keys Inside `top`

| Key | Action |
|---|---|
| `q` | Exit |
| `P` | Sort by CPU usage |
| `M` | Sort by memory usage |
| `k` | Send a signal to a process |
| `1` | Display individual CPU core statistics |

### Example

```bash
top
```

Suppose a Java application is consuming 95% CPU.

You can identify its PID and investigate why it is consuming excessive resources.

---

## 3.2 The `htop` Command

`htop` is an interactive process-monitoring tool with visual highlights.

It provides a more user-friendly interface than `top`.

### Syntax

```bash
htop
```

Features:

- Color-coded CPU and memory usage.
- Interactive process selection.
- Vertical and horizontal scrolling.
- Process tree visualization.
- Process termination through its interface.
- Easier navigation through long process commands.

### Installation

Ubuntu/Debian:

```bash
sudo apt install htop
```

RHEL/Fedora:

```bash
sudo dnf install htop
```

### Example

```bash
htop
```

**Production Insight:** `htop` is useful during interactive server troubleshooting, especially when you need to identify resource-intensive applications quickly.

---

## 3.3 The `atop` Command

`atop` provides detailed information about processes and hardware resource utilization.

### Syntax

```bash
atop
```

It can monitor:

- CPU usage.
- Memory usage.
- Disk activity.
- Network activity.
- Process-level resource consumption.

### Installation

Ubuntu/Debian:

```bash
sudo apt install atop
```

RHEL/Fedora:

```bash
sudo dnf install atop
```

### Example

```bash
atop
```

**Senior DevOps Insight:** Unlike basic process inspection, `atop` can help investigate resource consumption across multiple system components. Its historical reporting capabilities are particularly useful when configured to collect system data over time.

---

# 4. Terminating Processes

## 4.1 The `kill` Command

The `kill` command sends a signal to a process.

It is commonly used to stop an unresponsive application.

### Syntax

```bash
kill PID
```

### Example

First, identify the process:

```bash
ps aux | grep nginx
```

Suppose the process ID is `2456`.

```bash
kill 2456
```

By default, `kill` sends `SIGTERM` (signal 15).

This requests that the process terminate gracefully.

### Important Signals

| Signal | Number | Purpose |
|---|---:|---|
| SIGTERM | 15 | Request graceful termination |
| SIGKILL | 9 | Force immediate termination |
| SIGHUP | 1 | Hangup; commonly used to request reloads in supported applications |
| SIGINT | 2 | Interrupt, similar to Ctrl+C |

### Forcefully Terminate a Process

```bash
kill -9 2456
```

This sends `SIGKILL`.

The process cannot catch or ignore this signal.

**Important:** Always try normal termination first. Use `kill -9` only when necessary because the process cannot perform cleanup.

---

## 4.2 The `killall` Command

`killall` sends a signal to processes matching a process name.

### Syntax

```bash
killall process_name
```

### Example

```bash
killall nginx
```

This sends the default termination signal to processes named `nginx`.

To force termination:

```bash
killall -9 nginx
```

To check available matching processes before terminating them:

```bash
pgrep -a nginx
```

**Production Warning:** Be careful when using `killall` on shared servers. Multiple instances of the same application may be running, and you could terminate more processes than intended.

For a managed service, prefer:

```bash
sudo systemctl stop nginx
```

This allows the service manager to handle the application lifecycle.

---

# 5. Open Files and Process Investigation

## 5.1 The `lsof` Command

`lsof` stands for List Open Files.

In Linux, many resources are represented as files, including:

- Regular files.
- Directories.
- Network sockets.
- Pipes.
- Devices.

The `lsof` command helps identify which processes are using these resources.

### Syntax

```bash
lsof
```

Displays open files across the system.

### Example 1: Display Files Opened by Root

```bash
sudo lsof -u root
```

Displays files opened by processes owned by the root user.

### Example 2: Identify Which Process Is Using a File

```bash
sudo lsof /var/log/syslog
```

Shows processes that currently have the specified file open.

### Example 3: Identify Processes Using a Port

```bash
sudo lsof -i :8080
```

Shows processes using network port 8080.

### Example 4: Identify Processes Using a Directory

```bash
sudo lsof +D /var/www/html
```

Recursively checks files under the specified directory.

This can be expensive on directories containing many files.

### Real-World Scenario: Cannot Unmount a Disk

Suppose you try to unmount an external drive:

```bash
sudo umount /mnt/backup
```

But Linux returns:

```text
target is busy
```

Investigate:

```bash
sudo lsof +D /mnt/backup
```

This helps identify processes accessing files inside the mount point.

You can then stop or reconfigure the responsible process before unmounting.

**Senior DevOps Insight:** `lsof` is extremely useful for investigating busy filesystems, unexpected open file descriptors, and processes occupying network ports.

---

# 6. CPU and Memory Monitoring

Performance monitoring is essential when managing production Linux servers.

Three important commands are:

- `mpstat`
- `vmstat`
- `iostat`

## 6.1 The `mpstat` Command

`mpstat` displays processor-related statistics.

### Syntax

```bash
mpstat 1
```

The number `1` means that statistics are refreshed every second.

### Example

```bash
mpstat 2
```

Refreshes processor statistics every two seconds.

### Display Statistics for All CPUs

```bash
mpstat -P ALL 1
```

This displays statistics for individual processors.

### Important Metrics

| Metric | Meaning |
|---|---|
| `%usr` | CPU time spent executing user applications |
| `%sys` | CPU time spent executing kernel code |
| `%idle` | CPU time spent idle |
| `%iowait` | CPU time spent waiting for I/O |
| `%steal` | CPU time taken by the hypervisor from a virtual machine |

### DevOps Scenario

Suppose your application is experiencing slow response times.

You run:

```bash
mpstat 1
```

If `%iowait` is consistently high, investigate storage performance rather than immediately assuming the application code is responsible.

---

## 6.2 The `vmstat` Command

`vmstat` stands for Virtual Memory Statistics.

It reports information about:

- Memory.
- Processes.
- Paging.
- Interrupts.
- Block I/O.
- CPU scheduling.

### Syntax

```bash
vmstat 1
```

Refreshes system statistics every second.

### Example

```bash
vmstat 2
```

Refreshes statistics every two seconds.

### Important Columns

| Column | Meaning |
|---|---|
| `r` | Processes waiting for CPU time |
| `b` | Processes blocked, usually waiting for I/O |
| `swpd` | Amount of virtual memory used |
| `free` | Free memory |
| `si` | Memory swapped in per second |
| `so` | Memory swapped out per second |
| `bi` | Blocks received from block devices |
| `bo` | Blocks sent to block devices |
| `us` | CPU time spent in user space |
| `sy` | CPU time spent in system space |
| `id` | Idle CPU time |
| `wa` | CPU time waiting for I/O |

### Example

```bash
vmstat 1
```

If `si` and `so` remain high, the system may be experiencing memory pressure.

Investigate memory consumption and application behavior.

**Senior DevOps Insight:** Linux uses available memory for caching. Low `free` memory alone does not necessarily indicate a memory problem. Check `available` memory with `free -h` and observe swap activity.

---

## 6.3 The `iostat` Command

`iostat` provides CPU and disk I/O statistics.

It helps identify storage bottlenecks.

### Syntax

```bash
iostat 1
```

Refreshes CPU and device statistics every second.

### Example

```bash
iostat -xz 1
```

Displays extended device statistics with additional detail.

### Important Metrics

| Metric | Meaning |
|---|---|
| `r/s` | Read requests per second |
| `w/s` | Write requests per second |
| `rkB/s` | Kilobytes read per second |
| `wkB/s` | Kilobytes written per second |
| `await` | Average I/O request waiting time |
| `%util` | Percentage of time the device was busy processing I/O |

### Installation

Ubuntu/Debian:

```bash
sudo apt install sysstat
```

RHEL/Fedora:

```bash
sudo dnf install sysstat
```

### DevOps Scenario

A database server is responding slowly.

Run:

```bash
iostat -xz 1
```

If disk latency is consistently high, investigate:

- Storage performance.
- Disk saturation.
- Database query patterns.
- Excessive logging.
- Storage infrastructure.

**Important:** `%util` must be interpreted according to the storage architecture. On modern SSDs, RAID systems, and cloud volumes, it does not always directly represent a simple performance limit.

---

# 7. System Logs

## 7.1 The `tail` Command

The `tail` command displays the end of a file.

It is frequently used to inspect application and system logs.

### Syntax

```bash
tail -n 100 /var/log/messages
```

Displays the last 100 lines of the system log.

On many Debian-based systems, the corresponding traditional system log is:

```bash
tail -n 100 /var/log/syslog
```

### Follow Logs in Real Time

```bash
tail -f /var/log/syslog
```

Continuously displays new log entries as they are written.

Press:

```text
Ctrl + C
```

to stop following the log.

### Follow Multiple Logs

```bash
tail -f /var/log/syslog /var/log/nginx/error.log
```

Displays updates from multiple files.

### Modern Alternative: `journalctl`

On systems using systemd, you can inspect service logs with:

```bash
sudo journalctl -u nginx
```

Follow logs continuously:

```bash
sudo journalctl -u nginx -f
```

Show logs from the current boot:

```bash
journalctl -b
```

### Real-World DevOps Scenario

Suppose a production web application suddenly stops responding.

Start by inspecting its service logs:

```bash
sudo journalctl -u nginx -n 100
```

Look for:

- Permission errors.
- Configuration errors.
- Port conflicts.
- Failed upstream connections.
- Application startup failures.

Logs help you identify what happened before changing the system.

**Important:** Log locations differ between Linux distributions. Some systems use `/var/log/messages`, others use `/var/log/syslog`, and some rely primarily on the systemd journal.

---

# 8. Network Traffic Monitoring

## 8.1 The `tcpdump` Command

`tcpdump` is a command-line packet analyzer.

It captures and displays network packets passing through a network interface.

It is useful for troubleshooting network communication.

### Syntax

```bash
sudo tcpdump -i interface
```

### Example 1: Capture Traffic on an Interface

```bash
sudo tcpdump -i eth0
```

Captures packets on interface `eth0`.

### Example 2: Monitor Traffic on Port 80

```bash
sudo tcpdump -i eth0 port 80
```

Captures traffic involving port 80 on interface `eth0`.

Port 80 is commonly used for HTTP.

### Example 3: Monitor HTTPS Traffic

```bash
sudo tcpdump -i eth0 port 443
```

Captures traffic involving port 443.

### Example 4: Capture Traffic from a Specific Host

```bash
sudo tcpdump -i eth0 host 192.168.1.10
```

Captures traffic to or from the specified host.

### Example 5: Save Captured Packets

```bash
sudo tcpdump -i eth0 -w capture.pcap
```

Saves captured packets to a file.

You can inspect the capture later:

```bash
tcpdump -r capture.pcap
```

### Important Options

| Option | Meaning |
|---|---|
| `-i` | Select network interface |
| `-w` | Write packets to a file |
| `-r` | Read packets from a file |
| `-n` | Avoid resolving hostnames |
| `-c` | Stop after capturing a specified number of packets |

### Real-World DevOps Scenario

Your application cannot communicate with an external API.

You suspect a network connectivity problem.

Run:

```bash
sudo tcpdump -i eth0 host 203.0.113.10
```

This helps determine whether packets are being sent to or received from the destination.

**Security Note:** Packet captures may contain sensitive information. Restrict access to capture files and avoid collecting unnecessary production traffic.

---

# 9. Continuous System Monitoring

## 9.1 The `watch` Command

`watch` repeatedly executes a command and displays its output.

It is useful when you want to observe system information without manually running the command repeatedly.

### Syntax

```bash
watch command
```

### Example

```bash
watch df -h
```

Runs `df -h` repeatedly and displays updated disk usage.

The default refresh interval is generally two seconds.

### Example 2: Change the Refresh Interval

```bash
watch -n 5 df -h
```

Refreshes disk usage every five seconds.

### Example 3: Monitor Memory

```bash
watch -n 2 free -h
```

Refreshes memory statistics every two seconds.

### Example 4: Monitor Running Processes

```bash
watch -n 2 'ps aux --sort=-%cpu | head'
```

Displays the processes consuming the most CPU.

Press `Ctrl + C` to exit.

### DevOps Scenario

You are deploying an application and want to observe disk usage while logs and temporary files are being generated.

Run:

```bash
watch -n 2 df -h
```

This provides a continuously refreshed view of filesystem usage.

---

# 10. User Management

Linux is a multi-user operating system.

Multiple users can access the same server, but each user should have appropriate permissions.

As a DevOps engineer, you must understand how to inspect users, create accounts, manage groups, and control access.

## 10.1 The `who` Command

Displays users currently logged into the system.

### Syntax

```bash
who
```

Example output:

```text
jasin    pts/0    2026-11-21 18:30
admin    pts/1    2026-11-21 18:45
```

This indicates two active login sessions.

---

## 10.2 The `w` Command

Displays currently logged-in users and what they are doing.

### Syntax

```bash
w
```

It provides:

- Username.
- Login terminal.
- Login time.
- Idle time.
- CPU usage.
- Current activity.

### Difference Between `who` and `w`

| Command | Purpose |
|---|---|
| `who` | Displays who is logged in |
| `w` | Displays logged-in users and their activity |

---

## 10.3 The `users` Command

Displays usernames associated with current login sessions.

### Syntax

```bash
users
```

Example output:

```text
jasin admin root
```

This is a simple way to see which accounts have active login sessions.

---

## 10.4 The `whoami` Command

Displays the username associated with the current effective user identity.

### Syntax

```bash
whoami
```

Example output:

```text
jasin
```

If you switch to root:

```bash
sudo -i
whoami
```

Output:

```text
root
```

---

## 10.5 The `id` Command

Displays user ID and group ID information.

### Syntax

```bash
id
```

Example output:

```text
uid=1000(jasin) gid=1000(jasin) groups=1000(jasin),27(sudo)
```

### Display Information for a Specific User

```bash
id root
```

### Important Fields

| Field | Meaning |
|---|---|
| `uid` | User ID |
| `gid` | Primary group ID |
| `groups` | Primary and supplementary group memberships |

**Senior DevOps Insight:** Linux permissions are based on user and group identities. Understanding `id` helps troubleshoot permission-denied errors.

---

## 10.6 The `last` Command

Displays login history.

### Syntax

```bash
last
```

It typically reads login records from `/var/log/wtmp`.

Useful for investigating:

- Previous login sessions.
- Login times.
- Reboots.
- Unexpected account activity.

### Example

```bash
last -n 10
```

Displays the last 10 recorded entries.

**Security Insight:** Login history is useful during basic security investigations, but it is not a complete security audit system.

---

## 10.7 The `groupadd` Command

Creates a new Linux group.

Groups help administrators assign permissions to multiple users.

### Syntax

```bash
sudo groupadd group_name
```

### Example

```bash
sudo groupadd gp1
```

Creates a group named `gp1`.

### Verify the Group

```bash
getent group gp1
```

Example output:

```text
gp1:x:1002:
```

---

## 10.8 The `useradd` Command

Creates a new Linux user account.

### Syntax

```bash
sudo useradd [options] username
```

### Example

```bash
sudo useradd -c "Alice Bob" -m ab1
```

Creates an account named `ab1`, adds the comment `Alice Bob`, and creates a home directory.

### Command Breakdown

| Option | Meaning |
|---|---|
| `-c` | Add a user comment or description |
| `-m` | Create the user's home directory |

### Specify a Login Shell

```bash
sudo useradd -m -s /bin/bash ab1
```

This creates the user with `/bin/bash` as the login shell.

### Set a Password

```bash
sudo passwd ab1
```

Prompts the administrator to set a password for the account.

### Verify the User

```bash
id ab1
```

Check the home directory:

```bash
ls -ld /home/ab1
```

**Important:** Default user creation behavior can differ between distributions. Check local defaults before relying on them in automation.

---

## 10.9 The `userdel` Command

Deletes a Linux user account.

### Syntax

```bash
sudo userdel username
```

### Example

```bash
sudo userdel ab1
```

Deletes the account named `ab1`.

By default, this normally leaves the user's home directory and files behind.

### Delete the User and Home Directory

```bash
sudo userdel -r ab1
```

The `-r` option removes the user's home directory and mail spool where applicable.

**Production Warning:** This is a destructive operation. Check ownership of files and preserve any required data before deleting a production account.

---

## 10.10 The `usermod` Command

Modifies an existing Linux user account.

One common use is adding a user to a supplementary group.

### Syntax

```bash
sudo usermod -aG group_name username
```

### Example

```bash
sudo usermod -aG gp1 ab1
```

Adds the user `ab1` to the supplementary group `gp1`.

### Command Breakdown

| Option | Meaning |
|---|---|
| `-a` | Append the group instead of replacing existing supplementary memberships |
| `-G` | Specify supplementary groups |

### Verify Group Membership

```bash
id ab1
```

Or:

```bash
groups ab1
```

### Important: Why Is `-a` Necessary?

Consider:

```bash
sudo usermod -G gp1 ab1
```

This can replace the user's existing supplementary group memberships.

Instead, use:

```bash
sudo usermod -aG gp1 ab1
```

This preserves existing supplementary memberships while adding the new group.

**Senior DevOps Insight:** A user may need to log out and log back in for a new group membership to appear in their existing login session.

---

# 🚀 Real-World DevOps Scenario

## Scenario: Production Application Is Running Slowly

Imagine you are responsible for a production Linux server hosting a Java application.

Users report that the application is responding very slowly.

Your responsibility is to identify the problem without blindly restarting the server.

### Step 1: Identify Running Processes

```bash
ps aux
```

Or:

```bash
ps -ef
```

Find the Java application and note its PID.

### Step 2: Check CPU and Memory Usage

```bash
top
```

Or:

```bash
htop
```

Determine whether the Java process is consuming excessive resources.

### Step 3: Inspect CPU Statistics

```bash
mpstat 1
```

Check whether CPU utilization is consistently high.

### Step 4: Investigate Memory Pressure

```bash
vmstat 1
```

Check for excessive swapping and blocked processes.

### Step 5: Investigate Disk I/O

```bash
iostat -xz 1
```

Look for storage latency or unusually high I/O activity.

### Step 6: Inspect Application Logs

```bash
sudo journalctl -u myapp -n 100
```

Look for application exceptions, database errors, or failed dependencies.

### Step 7: Check Network Connections

```bash
sudo lsof -i
```

Or:

```bash
sudo tcpdump -i eth0 port 8080
```

Investigate whether the application is receiving or sending the expected traffic.

### Step 8: Terminate Only If Necessary

If the application is genuinely unresponsive and a restart is justified, use its service manager:

```bash
sudo systemctl restart myapp
```

Avoid immediately using:

```bash
kill -9 PID
```

A graceful service restart is generally safer.

### What You Learned

This workflow demonstrates how a DevOps engineer investigates:

- Process health.
- CPU bottlenecks.
- Memory pressure.
- Disk performance.
- Application errors.
- Network communication.

The goal is to identify the root cause before taking corrective action.

---

# 🔐 Production Best Practices

1. **Avoid unnecessary force termination:** Use `SIGTERM` before `SIGKILL`.
2. **Use service managers:** Prefer `systemctl` for production services rather than manually killing processes.
3. **Monitor resource trends:** A single snapshot may not reveal a recurring performance problem.
4. **Investigate before restarting:** Check logs, CPU, memory, disk, and network metrics.
5. **Follow least privilege:** Give users only the permissions required for their jobs.
6. **Use groups for shared access:** Avoid assigning permissions individually when a group is more appropriate.
7. **Be careful with `userdel -r`:** It can permanently remove user data.
8. **Protect packet captures:** Network captures can contain sensitive information.
9. **Audit user access:** Regularly review accounts, group membership, and login history.
10. **Use monitoring systems:** For production environments, consider Prometheus, Grafana, and centralized logging.

---

# 📌 Quick Command Reference

| Command | Purpose |
|---|---|
| `command &` | Run a command in the background |
| `jobs` | Display shell-managed jobs |
| `ps` | Display process status |
| `ps aux` | Display processes across users |
| `ps -e` | Display all processes |
| `ps -ef` | Display detailed process information |
| `ps -U root -u root` | Display root-owned processes |
| `ps -eo pid,user,command` | Display selected process columns |
| `top` | Interactive process monitoring |
| `htop` | Enhanced process monitoring |
| `atop` | Detailed system and process monitoring |
| `kill PID` | Request process termination |
| `kill -9 PID` | Forcefully terminate a process |
| `killall nginx` | Send a signal to matching processes |
| `lsof` | List open files |
| `lsof -u root` | List files opened by root-owned processes |
| `lsof -i :8080` | Identify processes using port 8080 |
| `mpstat 1` | Monitor CPU statistics every second |
| `vmstat 1` | Monitor memory and system statistics |
| `iostat -xz 1` | Monitor extended disk I/O statistics |
| `tail -n 100 file` | Display the last 100 lines |
| `tail -f file` | Follow log updates |
| `journalctl -u nginx` | Inspect service logs |
| `tcpdump -i eth0` | Capture network packets |
| `tcpdump -i eth0 port 80` | Capture traffic involving port 80 |
| `watch df -h` | Refresh disk usage periodically |
| `who` | Display logged-in users |
| `w` | Display logged-in users and activity |
| `users` | Display logged-in usernames |
| `whoami` | Display effective username |
| `id` | Display user and group IDs |
| `last` | Display login history |
| `groupadd gp1` | Create a group |
| `useradd -c "Alice Bob" -m ab1` | Create a user with a home directory |
| `userdel ab1` | Delete a user account |
| `usermod -aG gp1 ab1` | Add a user to a supplementary group |

---

# 🎯 Interview Questions

## Basic Level

**1. What is a Linux process?**

A process is an instance of a running program. Each process has a unique Process ID (PID).

**2. What is the purpose of the `ps` command?**

It displays information about running processes.

**3. What is the difference between `top` and `htop`?**

Both monitor processes and system resources. `htop` provides a more interactive and visually enhanced interface.

**4. What does the `&` operator do?**

It starts a command in the background.

**5. What is the purpose of `kill`?**

It sends a signal to a process, commonly to request termination.

## Intermediate Level

**6. What is the difference between `kill` and `kill -9`?**

`kill` sends SIGTERM by default, allowing graceful termination. `kill -9` sends SIGKILL, which forces termination.

**7. What is the difference between `ps aux` and `ps -ef`?**

Both show processes across the system, but they use different output formats and option conventions.

**8. What is the purpose of `lsof`?**

It identifies open files and the processes using them.

**9. What is the difference between `vmstat` and `iostat`?**

`vmstat` reports memory, processes, paging, and CPU scheduling statistics. `iostat` focuses on CPU and storage I/O statistics.

**10. Why would you use `tcpdump`?**

To capture and inspect network packets during connectivity and traffic troubleshooting.

## Senior DevOps Scenario-Based Questions

**11. A Java application is consuming 100% CPU. How would you investigate?**

Start with `top` or `ps` to identify the process. Then inspect CPU statistics with `mpstat`, review application logs, and investigate the application's threads and workload before taking corrective action.

**12. Your server has very little free memory. Does that automatically mean the server is running out of memory?**

No. Linux uses available memory for caching. Check `free -h`, available memory, swap activity, and application memory consumption.

**13. A filesystem cannot be unmounted because it is busy. How would you troubleshoot it?**

Use `lsof` or `fuser` to identify processes accessing the mount point. Stop or reconfigure the responsible processes before unmounting.

**14. A user needs access to a shared deployment directory. How would you provide access?**

Create a dedicated group, add the user using `usermod -aG`, and assign appropriate directory ownership and permissions to the group.

**15. Your production application is slow. How would you determine whether the bottleneck is CPU, memory, disk, or network?**

Use `top`/`htop`, `mpstat`, `vmstat`, `iostat`, application logs, and `tcpdump` as appropriate. Correlate the findings rather than relying on one metric.

---

# 🧪 Hands-On Practice

Complete these tasks on a Linux virtual machine or lab environment.

- [ ] Start a background process using `sleep 100 &`.
- [ ] Display background jobs using `jobs`.
- [ ] List all processes using `ps aux`.
- [ ] Display selected process columns using `ps -eo pid,user,command`.
- [ ] Monitor processes using `top`.
- [ ] Install and explore `htop`.
- [ ] Start a test process and terminate it using `kill`.
- [ ] Identify open files using `lsof`.
- [ ] Identify which process is using port 8080.
- [ ] Monitor CPU statistics using `mpstat 1`.
- [ ] Monitor memory statistics using `vmstat 1`.
- [ ]
