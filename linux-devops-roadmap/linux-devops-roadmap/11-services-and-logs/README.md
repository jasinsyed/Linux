# 🐧 Linux Services and Logs – systemctl, journalctl & Log Management

> **Roadmap:** Topic 11 of 12  
> **Topic:** Services and Logs  
> **Domain:** Linux Administration / DevOps / Cloud Engineering  
> **Difficulty:** Intermediate to Advanced  
> **Objective:** Understand how Linux services work, manage services using systemd, inspect system and application logs, configure log rotation, and troubleshoot production service failures.

---

## 📚 Table of Contents

1. [Introduction](#-introduction)
2. [Understanding Linux Services](#1-understanding-linux-services)
3. [systemctl – Managing Services](#2-systemctl--managing-services)
4. [service – Managing Services](#3-service--managing-services)
5. [journalctl – Viewing System Logs](#4-journalctl--viewing-system-logs)
6. [Linux Log Directories](#5-linux-log-directories)
7. [Log Rotation](#6-log-rotation)
8. [Service Troubleshooting](#7-service-troubleshooting)
9. [Working with systemd Service Files](#8-working-with-systemd-service-files)
10. [Real-World DevOps Scenarios](#-real-world-devops-scenarios)
11. [Production Best Practices](#-production-best-practices)
12. [Quick Command Reference](#-quick-command-reference)
13. [Interview Questions](#-interview-questions)
14. [Hands-On Practice](#-hands-on-practice)
15. [Key Takeaways](#-key-takeaways)

---

## 🎯 Introduction

Linux services are programs that run in the background and provide functionality to the operating system or applications.

For example:

- Nginx provides web server functionality.
- SSH allows secure remote access.
- MySQL manages database operations.
- Docker manages containers.
- Cron executes scheduled tasks.
- Jenkins runs automation and CI/CD workloads.

In production environments, services must remain reliable and available.

As a DevOps engineer, you need to understand:

- How to start and stop services.
- How to enable services during system startup.
- How to check service status.
- How to inspect system and application logs.
- How to troubleshoot failed services.
- How to prevent log files from consuming all available disk space.
- How to create and manage custom systemd services.

**Important Concept:** A service is generally a background application managed by the operating system. On most modern Linux distributions, `systemd` is the primary service manager.

---

# 1. Understanding Linux Services

## 1.1 What Is a Linux Service?

A Linux service is a background program that performs a particular task without requiring continuous user interaction.

For example, consider an Nginx web server.

When you start Nginx:

```bash
sudo systemctl start nginx
```

It runs in the background and waits for incoming HTTP requests.

You do not need to keep a terminal open for it to continue running.

### Service vs Process

These concepts are related but not identical.

| Service | Process |
|---|---|
| A managed application or system function | An instance of a running program |
| Managed through a service manager such as systemd | Managed by the Linux kernel |
| Has service configuration and lifecycle rules | Has a PID and process state |
| Can manage one or multiple processes | Represents an individual execution instance |

### Example

Nginx can be managed as a service:

```bash
sudo systemctl status nginx
```

You can inspect its processes using:

```bash
ps aux | grep nginx
```

**Senior DevOps Insight:** A service can be active while individual worker processes change or restart. Service health and process health should be investigated together.

---

# 2. systemctl – Managing Services

## 2.1 What Is systemctl?

`systemctl` is the command-line utility used to communicate with systemd.

It allows administrators to manage services and other systemd units.

Common operations include:

- Starting services.
- Stopping services.
- Restarting services.
- Reloading service configuration.
- Checking service status.
- Enabling services at boot.
- Disabling services at boot.

### Syntax

```bash
systemctl [command] service_name
```

Most service-management operations require `sudo`.

---

## 2.2 Start a Service

Starts a service immediately.

### Syntax

```bash
sudo systemctl start service_name
```

### Example

```bash
sudo systemctl start nginx
```

Starts the Nginx web server.

**Important:** Starting a service does not automatically configure it to start after a reboot.

---

## 2.3 Stop a Service

Stops a running service.

### Syntax

```bash
sudo systemctl stop service_name
```

### Example

```bash
sudo systemctl stop nginx
```

Stops Nginx.

### Production Scenario

Suppose an application is consuming excessive resources and needs to be temporarily stopped.

You can stop its managed service:

```bash
sudo systemctl stop myapp
```

Investigate the issue before starting it again.

---

## 2.4 Restart a Service

Stops and starts a service again.

### Syntax

```bash
sudo systemctl restart service_name
```

### Example

```bash
sudo systemctl restart nginx
```

Restarts Nginx.

### When Should You Use It?

Use restart when:

- The application is unresponsive.
- A service needs a complete restart.
- Configuration changes require a restart.
- A service is stuck in an unexpected state.

**Production Insight:** Restarting a service can interrupt active connections. Understand the application's availability requirements before restarting a production service.

---

## 2.5 Reload a Service

Reloads a service's configuration without performing a full service restart, if the application supports it.

### Syntax

```bash
sudo systemctl reload service_name
```

### Example

```bash
sudo systemctl reload nginx
```

Requests Nginx to reload its configuration.

### Difference Between Restart and Reload

| Restart | Reload |
|---|---|
| Stops and starts the service | Requests configuration reload |
| Can interrupt active connections | Often avoids a full service restart |
| Recreates the service process lifecycle | Usually keeps the main service running |
| Used when a complete restart is necessary | Used when supported configuration changes are made |

**Senior DevOps Insight:** Always validate configuration before reloading.

For Nginx:

```bash
sudo nginx -t
```

If the configuration is valid:

```bash
sudo systemctl reload nginx
```

---

## 2.6 Check Service Status

Displays the current status of a service.

### Syntax

```bash
systemctl status service_name
```

### Example

```bash
sudo systemctl status nginx
```

Example output:

```text
● nginx.service - A high performance web server
     Loaded: loaded
     Active: active (running)
       Docs: man:nginx(8)
   Main PID: 1250 (nginx)
      Tasks: 5
```

### Important Fields

| Field | Meaning |
|---|---|
| Loaded | Whether systemd loaded the unit |
| Active | Current service state |
| Main PID | Main process ID |
| Tasks | Number of tasks associated with the service |
| Docs | Documentation information |

### Common Service States

| State | Meaning |
|---|---|
| `active (running)` | Service is running |
| `active (exited)` | Unit completed successfully but has no continuously running process |
| `inactive (dead)` | Service is not running |
| `failed` | Service failed |
| `activating` | Service is starting |
| `deactivating` | Service is stopping |

**Important:** `active (exited)` is not necessarily an error. Some one-shot services complete their work and exit successfully.

---

## 2.7 Enable a Service at Boot

Configures a service to start automatically during system startup.

### Syntax

```bash
sudo systemctl enable service_name
```

### Example

```bash
sudo systemctl enable nginx
```

Nginx is configured to start automatically during boot.

### Enable and Start Immediately

```bash
sudo systemctl enable --now nginx
```

This both enables and starts the service.

---

## 2.8 Disable a Service at Boot

Prevents a service from being automatically started through its normal boot enablement.

### Syntax

```bash
sudo systemctl disable service_name
```

### Example

```bash
sudo systemctl disable nginx
```

This does not necessarily stop an already running service.

To disable and stop it:

```bash
sudo systemctl disable --now nginx
```

---

## 2.9 List Running Services

```bash
systemctl list-units --type=service --state=running
```

Displays currently running service units.

To list loaded service units regardless of state:

```bash
systemctl list-units --type=service --all
```

To list installed service unit files and their enablement status:

```bash
systemctl list-unit-files --type=service
```

**Senior DevOps Insight:** Do not confuse running services with enabled services. A service can be running but disabled at boot, or enabled at boot but currently stopped.

---

# 3. service – Managing Services

## 3.1 What Is the service Command?

`service` is a traditional command used to manage system services.

It is commonly available for compatibility with older service-management systems and scripts.

On many modern systemd-based distributions, it acts as a compatibility interface.

### Syntax

```bash
sudo service service_name action
```

## 3.2 Start a Service

```bash
sudo service nginx start
```

Starts Nginx.

## 3.3 Stop a Service

```bash
sudo service nginx stop
```

Stops Nginx.

## 3.4 Restart a Service

```bash
sudo service nginx restart
```

Restarts Nginx.

## 3.5 Check Service Status

```bash
sudo service nginx status
```

Displays the service status.

### systemctl vs service

| systemctl | service |
|---|---|
| Native systemd management interface | Traditional compatibility interface |
| Supports enable and disable | Does not provide the same full systemd lifecycle management |
| Manages systemd units | Commonly manages service scripts or delegates to systemd |
| Preferred on modern systemd systems | Useful on older systems and compatibility scripts |

**Senior DevOps Insight:** When working with modern Linux distributions such as Ubuntu, Debian, Rocky Linux, or Fedora, understand `systemctl` first.

---

# 4. journalctl – Viewing System Logs

## 4.1 What Is journalctl?

`journalctl` is the command-line utility used to query logs collected by systemd-journald.

Systemd-journald collects system and service messages.

These logs can contain information about:

- Service startup and shutdown.
- Application errors.
- Authentication events.
- Kernel messages.
- System boot events.
- Hardware and system events.

### Syntax

```bash
journalctl [options]
```

---

## 4.2 Display All Available Journal Entries

```bash
journalctl
```

Displays journal entries available to the current user.

Depending on system configuration, administrative privileges may be needed to access all logs.

```bash
sudo journalctl
```

---

## 4.3 Display the Latest Logs

```bash
journalctl -n 50
```

Displays the latest 50 log entries.

### Example

```bash
sudo journalctl -n 100
```

Displays the latest 100 system journal entries.

---

## 4.4 Follow Logs in Real Time

```bash
journalctl -f
```

Continuously displays new journal entries.

This is similar to:

```bash
tail -f /var/log/syslog
```

### Production Example

```bash
sudo journalctl -f
```

Use this while starting or restarting a service to observe new log messages.

Press `Ctrl + C` to exit.

---

## 4.5 View Logs for a Specific Service

```bash
sudo journalctl -u nginx
```

Displays logs associated with the Nginx service.

### Display the Latest Service Logs

```bash
sudo journalctl -u nginx -n 100
```

Displays the latest 100 entries for Nginx.

### Follow Service Logs

```bash
sudo journalctl -u nginx -f
```

Continuously displays new Nginx journal entries.

**Senior DevOps Insight:** When a service fails, checking its own journal entries is usually more useful than immediately searching through every system log.

---

## 4.6 View Logs from the Current Boot

```bash
journalctl -b
```

Displays logs from the current boot session.

### View Logs from the Previous Boot

```bash
journalctl -b -1
```

Displays logs from the previous boot, if they are available and persistent journal storage is configured.

This is useful when investigating failures that occurred before a server rebooted.

---

## 4.7 Filter Logs by Time

### Logs Since Today

```bash
journalctl --since today
```

### Logs from the Last Hour

```bash
journalctl --since "1 hour ago"
```

### Logs Between Two Times

```bash
journalctl --since "2026-11-21 10:00:00" --until "2026-11-21 11:00:00"
```

This helps narrow down a production incident to a specific time window.

---

## 4.8 Display Only Error-Level Logs

```bash
journalctl -p err
```

Displays error-priority journal messages.

### Common Priority Levels

| Priority | Meaning |
|---|---|
| `0` | Emergency |
| `1` | Alert |
| `2` | Critical |
| `3` | Error |
| `4` | Warning |
| `5` | Notice |
| `6` | Informational |
| `7` | Debug |

Example:

```bash
sudo journalctl -p warning
```

Displays warning-level and more severe messages.

---

# 5. Linux Log Directories

## 5.1 Understanding /var/log

Linux stores many system and application logs under:

```bash
/var/log
```

Logs help administrators understand what happened inside the system.

### List Available Log Files

```bash
ls -lh /var/log
```

Displays log files and directories with human-readable file sizes.

### Important Log Files

| File | Purpose |
|---|---|
| `/var/log/syslog` | General system messages on many Debian-based systems |
| `/var/log/messages` | General system messages on many RHEL-based systems |
| `/var/log/auth.log` | Authentication and authorization messages on many Debian-based systems |
| `/var/log/secure` | Authentication-related messages on many RHEL-based systems |
| `/var/log/kern.log` | Kernel messages on systems that maintain this file |
| `/var/log/dmesg` | Boot-time and kernel ring-buffer messages on systems that maintain this file |
| `/var/log/nginx/access.log` | Nginx access log, depending on configuration |
| `/var/log/nginx/error.log` | Nginx error log, depending on configuration |

**Important:** Log file locations differ between distributions and application configurations. Some systems primarily use the systemd journal instead of writing every message to traditional log files.

---

## 5.2 Read System Logs

### Display the Last 50 Lines

```bash
sudo tail -n 50 /var/log/syslog
```

### Follow System Logs

```bash
sudo tail -f /var/log/syslog
```

### Search for Errors

```bash
sudo grep -i "error" /var/log/syslog
```

### Search for Authentication Failures

On Debian-based systems:

```bash
sudo grep -i "failed" /var/log/auth.log
```

On RHEL-based systems:

```bash
sudo grep -i "failed" /var/log/secure
```

### DevOps Insight

Logs are useful for identifying:

- Failed service startup.
- Incorrect configuration.
- Authentication failures.
- Application exceptions.
- Network connection problems.
- Permission issues.
- Unexpected shutdowns.

---

# 6. Log Rotation

## 6.1 What Is Log Rotation?

Applications continuously generate log entries.

Over time, log files can become extremely large.

For example:

```text
/var/log/nginx/access.log
```

If an application receives millions of requests, its access log can consume several gigabytes of disk space.

This can eventually fill the filesystem and affect application availability.

Log rotation solves this problem by managing old and current log files.

It can:

- Rotate log files periodically.
- Rename older logs.
- Compress old logs.
- Retain only a configured number of historical logs.
- Remove logs after a retention period.

### Example Without Log Rotation

```text
access.log
```

One log file continuously grows.

### Example With Log Rotation

```text
access.log
access.log.1
access.log.2.gz
access.log.3.gz
```

Older logs are rotated and may be compressed.

---

## 6.2 What Is logrotate?

`logrotate` is a Linux utility used to automate log file rotation.

### Check Whether It Is Installed

```bash
logrotate --version
```

### Main Configuration File

```bash
/etc/logrotate.conf
```

This contains global log rotation settings.

Additional application-specific configurations are commonly stored under:

```bash
/etc/logrotate.d/
```

### View Configuration Files

```bash
ls -l /etc/logrotate.d/
```

---

## 6.3 Example Log Rotation Configuration

Suppose you want to configure rotation for an application log:

```text
/var/log/myapp/app.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0640 root adm
}
```

### Explanation

| Directive | Meaning |
|---|---|
| `daily` | Rotate the log daily |
| `rotate 7` | Keep seven rotated log files |
| `compress` | Compress older rotated logs |
| `delaycompress` | Delay compression until the next rotation cycle |
| `missingok` | Do not report an error if the log file is missing |
| `notifempty` | Do not rotate an empty log file |
| `create 0640 root adm` | Create a new log file with specified permissions and ownership |

**Important:** The exact owner and group should match the application’s logging requirements. Do not blindly use `root adm` for every application.

---

## 6.4 Test Log Rotation

Before applying a configuration, test it:

```bash
sudo logrotate -d /etc/logrotate.d/myapp
```

The `-d` option runs in debug mode and does not make changes.

### Force Log Rotation

```bash
sudo logrotate -f /etc/logrotate.d/myapp
```

Forces rotation according to the specified configuration.

**Production Warning:** Forced rotation changes log files. Use it carefully, especially when applications hold open file descriptors to their log files.

---

## 6.5 Why Is Log Rotation Important?

Imagine a production server with a 20 GB root filesystem.

The application generates 2 GB of logs every day.

Without log rotation or retention:

- Disk usage continuously increases.
- Applications may fail to write new logs.
- Databases may stop operating correctly if their storage is affected.
- Services may become unstable.
- The server may eventually run out of disk space.

Log rotation helps control disk consumption and maintain operational stability.

**Senior DevOps Insight:** Log rotation is not a replacement for centralized logging. Production systems should also consider log forwarding, retention policies, monitoring, and alerting.

---

# 7. Service Troubleshooting

Troubleshooting services is a critical responsibility for DevOps engineers.

A service may fail because of:

- Incorrect configuration.
- Port conflicts.
- Missing dependencies.
- Permission problems.
- Insufficient memory.
- Disk space exhaustion.
- Application crashes.
- Invalid environment variables.

## 7.1 Check Service Status

```bash
sudo systemctl status nginx
```

Check whether the service is active, inactive, or failed.

---

## 7.2 Inspect Service Logs

```bash
sudo journalctl -u nginx -n 100
```

Displays the latest service logs.

Follow new entries:

```bash
sudo journalctl -u nginx -f
```

---

## 7.3 Check Whether the Required Port Is Already in Use

Suppose Nginx needs port 80.

Run:

```bash
sudo ss -lntp
```

Or:

```bash
sudo lsof -i :80
```

If another application is already listening on port 80, Nginx may fail to start.

---

## 7.4 Validate Application Configuration

For Nginx:

```bash
sudo nginx -t
```

This checks the configuration syntax.

If the configuration is invalid, correct it before restarting or reloading the service.

---

## 7.5 Check Disk Space

```bash
df -h
```

Displays filesystem usage.

If the filesystem is full, investigate large files:

```bash
sudo du -sh /var/log/*
```

This helps identify log directories consuming significant disk space.

---

## 7.6 Check Memory Usage

```bash
free -h
```

Displays memory and swap information.

You can also inspect running processes:

```bash
top
```

---

## 7.7 Inspect Service Dependencies

```bash
systemctl list-dependencies nginx
```

Displays the dependencies of the Nginx service.

This can help identify services or units involved in startup.

---

## 7.8 Check Recent Boot Errors

```bash
sudo journalctl -b -p err
```

Displays error-level messages from the current boot.

This is useful when multiple services are failing after a reboot.

---

# 8. Working with systemd Service Files

## 8.1 What Is a systemd Unit File?

A systemd unit file defines how systemd manages a resource such as:

- A service.
- A mount point.
- A timer.
- A socket.
- A device.

Service unit files commonly use the `.service` extension.

### Common Locations

System-provided service files:

```bash
/usr/lib/systemd/system/
```

On some distributions:

```bash
/lib/systemd/system/
```

Administrator-created service files:

```bash
/etc/systemd/system/
```

For custom services, `/etc/systemd/system/` is commonly used.

---

## 8.2 Create a Custom Service

Suppose you have a Python application located at:

```text
/opt/myapp/app.py
```

You want it to start automatically when the server boots.

Create a service file:

```bash
sudo nano /etc/systemd/system/myapp.service
```

Add:

```ini
[Unit]
Description=My Python Application
After=network.target

[Service]
Type=simple
User=myapp
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### Explanation

**[Unit]**

| Directive | Meaning |
|---|---|
| `Description` | Describes the service |
| `After` | Defines ordering relative to another unit |

**[Service]**

| Directive | Meaning |
|---|---|
| `Type=simple` | Main process is started directly |
| `User` | Account used to run the application |
| `WorkingDirectory` | Application working directory |
| `ExecStart` | Command used to start the application |
| `Restart=on-failure` | Restart when the service exits unsuccessfully |
| `RestartSec=5` | Wait five seconds before a restart attempt |

**[Install]**

| Directive | Meaning |
|---|---|
| `WantedBy=multi-user.target` | Makes the service suitable for enabling under the normal multi-user boot target |

**Important:** The `myapp` user must exist, and it must have permission to access the application files. The Python executable path should also be verified on the target machine.

---

## 8.3 Reload systemd Configuration

After creating or modifying a unit file, run:

```bash
sudo systemctl daemon-reload
```

This makes systemd read the updated unit configuration.

It does not automatically restart the application.

---

## 8.4 Start the Custom Service

```bash
sudo systemctl start myapp
```

Check its status:

```bash
sudo systemctl status myapp
```

---

## 8.5 Enable the Service at Boot

```bash
sudo systemctl enable myapp
```

Or enable and start it immediately:

```bash
sudo systemctl enable --now myapp
```

---

## 8.6 View Custom Service Logs

```bash
sudo journalctl -u myapp -f
```

This is particularly useful when debugging custom applications managed by systemd.

**Senior DevOps Insight:** In production, run applications under dedicated non-root accounts, use explicit working directories, configure restart behavior carefully, and ensure secrets are not exposed in unit files or process arguments.

---

# 🚀 Real-World DevOps Scenarios

## Scenario 1: Nginx Is Not Starting

Your production server reports that the website is unavailable.

### Step 1: Check Service Status

```bash
sudo systemctl status nginx
```

### Step 2: Inspect Service Logs

```bash
sudo journalctl -u nginx -n 100
```

### Step 3: Validate Configuration

```bash
sudo nginx -t
```

### Step 4: Check Port Availability

```bash
sudo ss -lntp | grep ':80'
```

### Step 5: Correct the Problem and Start Nginx

```bash
sudo systemctl restart nginx
```

### Step 6: Verify

```bash
sudo systemctl status nginx
```

The objective is to identify the root cause instead of repeatedly restarting the service.

---

## Scenario 2: Production Server Disk Is Full

Your monitoring system reports that the root filesystem is almost full.

### Step 1: Check Disk Usage

```bash
df -h
```

### Step 2: Investigate Log Directory Size

```bash
sudo du -sh /var/log/*
```

### Step 3: Identify Large Log Files

```bash
sudo find /var/log -type f -size +500M -ls
```

### Step 4: Inspect Log Rotation Configuration

```bash
ls -l /etc/logrotate.d/
```

### Step 5: Test the Relevant Configuration

```bash
sudo logrotate -d /etc/logrotate.d/myapp
```

### Step 6: Apply an Appropriate Retention Policy

Configure rotation, compression, and retention based on operational requirements.

**Important:** Do not blindly delete active log files. Applications may continue writing to deleted files through open file descriptors. Use the application's supported log-reopen mechanism or a properly configured logrotate policy.

---

## Scenario 3: Application Service Keeps Restarting

Your custom application repeatedly crashes and systemd attempts to restart it.

### Step 1: Check Status

```bash
sudo systemctl status myapp
```

### Step 2: Inspect Logs

```bash
sudo journalctl -u myapp -n 200
```

### Step 3: Follow Logs

```bash
sudo journalctl -u myapp -f
```

### Step 4: Inspect the Service Definition

```bash
sudo systemctl cat myapp
```

### Step 5: Check Dependencies and Resources

```bash
systemctl list-dependencies myapp
```

```bash
free -h
```

```bash
df -h
```

### Step 6: Correct the Root Cause

Investigate application exceptions, missing environment variables, permissions, and external dependencies.

Do not simply increase restart frequency without understanding why the application is crashing.

---

# 🔐 Production Best Practices

1. **Use systemctl:** Prefer systemd-native service management on modern Linux systems.
2. **Validate configurations:** Test configuration files before reloading production services.
3. **Monitor service health:** Check service status and application-level health, not just whether a process exists.
4. **Centralize logs:** Forward important logs to systems such as the ELK Stack, OpenSearch, or Loki.
5. **Configure log rotation:** Prevent logs from consuming all available disk space.
6. **Protect sensitive logs:** Restrict access to authentication logs and application logs containing confidential information.
7. **Avoid unnecessary restarts:** Investigate the root cause before restarting a production service.
8. **Use dedicated service accounts:** Avoid running application services as root unless absolutely necessary.
9. **Configure restart policies carefully:** Prevent endless restart loops from hiding application failures.
10. **Set up alerts:** Monitor service failures, disk usage, error rates, and resource utilization.
11. **Preserve incident evidence:** Avoid deleting logs before investigating an incident.
12. **Test changes safely:** Use staging environments before changing critical production service configurations.

---

# 📌 Quick Command Reference

| Command | Purpose |
|---|---|
| `systemctl start nginx` | Start Nginx |
| `systemctl stop nginx` | Stop Nginx |
| `systemctl restart nginx` | Restart Nginx |
| `systemctl reload nginx` | Reload Nginx configuration |
| `systemctl status nginx` | Check service status |
| `systemctl enable nginx` | Enable service at boot |
| `systemctl disable nginx` | Disable service at boot |
| `systemctl enable --now nginx` | Enable and start service |
| `systemctl list-units --type=service` | List loaded service units |
| `systemctl list-unit-files --type=service` | List installed service unit files |
| `service nginx start` | Start service using compatibility command |
| `service nginx stop` | Stop service |
| `service nginx restart` | Restart service |
| `service nginx status` | Check service status |
| `journalctl` | Display system journal |
| `journalctl -n 50` | Display latest 50 journal entries |
| `journalctl -f` | Follow journal entries |
| `journalctl -u nginx` | Display Nginx service logs |
| `journalctl -u nginx -f` | Follow Nginx logs |
| `journalctl -b` | Display current boot logs |
| `journalctl -b -1` | Display previous boot logs |
| `journalctl -p err` | Display error-level and more severe logs |
| `journalctl --since today` | Display today's logs |
| `ls -lh /var/log` | List log files |
| `tail -f /var/log/syslog` | Follow traditional system logs |
| `logrotate --version` | Check logrotate version |
| `logrotate -d /etc/logrotate.conf` | Test logrotate configuration in debug mode |
| `systemctl daemon-reload` | Reload systemd unit definitions |
| `systemctl cat myapp` | Display a service unit and its drop-ins |
| `systemctl list-dependencies nginx` | Display service dependencies |
| `df -h` | Check filesystem usage |
| `du -sh /var/log/*` | Check log directory sizes |
| `ss -lntp` | Inspect listening TCP ports |

---

# 🎯 Interview Questions

## Basic Level

**1. What is a Linux service?**

A Linux service is a background application or system component that performs a task and is commonly managed by a service manager.

**2. What is systemd?**

Systemd is a system and service manager used by many modern Linux distributions to initialize and manage system services.

**3. What is the purpose of systemctl?**

It is used to manage systemd units, including starting, stopping, restarting, enabling, and inspecting services.

**4. What is the difference between start and enable?**

`start` runs a service immediately. `enable` configures it to start automatically during boot.

**5. What is journalctl?**

It is a command-line utility for viewing and filtering logs collected by systemd-journald.

## Intermediate Level

**6. What is the difference between restart and reload?**

Restart stops and starts the service again. Reload asks the application to reload its configuration without a complete restart, when supported.

**7. What is the purpose of /var/log?**

It is a common directory for storing system and application log files.

**8. Why is log rotation important?**

It prevents log files from growing indefinitely and consuming all available disk space.

**9. What is the difference between systemctl and service?**

`systemctl` is the native systemd management interface, while `service` is a traditional compatibility command.

**10. How do you view logs for a particular service?**

Use:

```bash
sudo journalctl -u nginx
```

## Senior DevOps Scenario-Based Questions

**11. Your Nginx service fails to start. How would you troubleshoot it?**

Check service status, inspect journal logs, validate Nginx configuration using `nginx -t`, and check whether another process is using the required port.

**12. A production server is running out of disk space because of logs. What would you do?**

Check filesystem usage, identify large log files, inspect logrotate configuration, configure appropriate retention and compression, and safely manage existing logs.

**13. Your application service keeps restarting. How would you investigate it?**

Inspect systemd status, review service logs, check the unit configuration, examine application dependencies and resource availability, and identify the underlying crash reason.

**14. What is the purpose of systemctl daemon-reload?**

It makes systemd reload unit definitions after a service file or related configuration has changed. It does not automatically restart the service.

**15. How would you make a Python application run automatically after a server reboot?**

Create a systemd service unit with the correct user, working directory, executable path, and restart policy. Run `daemon-reload`, enable the service, and verify its status and logs.

**16. How would you investigate a service failure that happened before the latest reboot?**

Use:

```bash
sudo journalctl -b -1
```

This displays previous-boot logs when they are available.

**17. Why should production services avoid running as root?**

Running as root increases the impact of application vulnerabilities or compromised processes. Dedicated service accounts help enforce least privilege.

---

# 🧪 Hands-On Practice

Complete these tasks on a Linux virtual machine or lab environment.

### Service Management

- [ ] Check the status of the SSH service.
- [ ] Start and stop a test service.
- [ ] Restart Nginx or another available service.
- [ ] Reload a service that supports configuration reload.
- [ ] Enable a service at boot.
- [ ] Disable a service at boot.
- [ ] List running systemd services.
- [ ] Compare `systemctl` and `service`.

### Log Management

- [ ] Display the latest 50 journal entries.
- [ ] Follow system logs in real time.
- [ ] Inspect logs for a particular service.
- [ ] Display logs from the current boot.
- [ ] Inspect previous-boot logs if available.
- [ ] Filter logs by error priority.
- [ ] Explore the `/var/log` directory.
- [ ] Search traditional logs for error messages.

### Log Rotation

- [ ] Check whether logrotate is installed.
- [ ] Inspect `/etc/logrotate.conf`.
- [ ] Explore `/etc/logrotate.d/`.
- [ ] Create a test logrotate configuration.
- [ ] Validate it using debug mode.
- [ ] Understand compression and retention settings.

### Production Troubleshooting

- [ ] Investigate a service that fails to start.
- [ ] Check whether a port is already occupied.
- [ ] Investigate disk space consumption.
- [ ] Find large log files.
- [ ] Create a simple custom systemd service.
- [ ] Enable the custom service at boot.
- [ ] Inspect the custom service logs.

---

# 🏁 Key Takeaways

After completing this topic, you should understand how to:

- Manage Linux services using systemctl.
- Understand the difference between starting and enabling services.
- Use restart and reload appropriately.
- Manage services using the traditional service command.
- Inspect system and application logs using journalctl.
- Understand common Linux log locations.
- Configure log rotation using logrotate.
- Troubleshoot service startup and runtime failures.
- Create custom systemd service files.
- Investigate production incidents using logs and system information.

**Senior DevOps Mindset:** A service being active does not necessarily mean the application is healthy. Always combine service status, application logs, resource monitoring, and health checks to understand what is actually happening.

---

**Roadmap Progress:** Topic 11 of 12

**Next Topic:** Topic 12 – Final Linux Administration Topic.

### Completion Checklist

- [ ] Understand the core concepts
- [ ] Practise in a Linux environment
- [ ] Complete production scenarios
- [ ] Answer interview questions

**Goal:** Become confident managing Linux services, analyzing logs, and troubleshooting production service failures independently.
