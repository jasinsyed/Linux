# 🐧 Linux Commands for Searching and Filtering

> **Linux & DevOps Learning Journey — Topic 03**

This guide covers essential Linux search and filtering commands from the perspective of a DevOps engineer, focusing on searching application logs, locating files, filtering command output, investigating production issues, and organizing data.

## 📚 Table of Contents

1. [Introduction](#-introduction)
2. [grep – Search for Text Patterns](#1-grep--search-for-text-patterns)
3. [grep -r – Search Recursively](#2-grep--search-recursively)
4. [grep -v – Exclude Matching Lines](#3-grep--exclude-matching-lines)
5. [grep -l – Display Matching Filenames](#4-grep--display-matching-filenames)
6. [grep -i – Case-Insensitive Search](#5-grep--case-insensitive-search)
7. [find – Search for Files and Directories](#6-find--search-for-files-and-directories)
8. [find -name – Search by Filename](#7-find--search-by-filename)
9. [find -size – Search by File Size](#8-find--search-by-file-size)
10. [locate – Find Files by Name](#9-locate--find-files-by-name)
11. [sort – Sort Lines of Text](#10-sort--sort-lines-of-text)
12. [Real-World Production Scenario](#-real-world-production-scenario)
13. [Quick Command Reference](#-quick-command-reference)
14. [Interview Questions](#-interview-questions)
15. [Hands-On Practice](#-hands-on-practice)

---

## 🎯 Introduction

Searching and filtering are essential tasks for Linux administrators and DevOps engineers.

Imagine you're working on a production Linux server that hosts multiple applications.

Your application generates thousands of log entries every minute.

Suddenly, users report that the application is responding slowly.

As a DevOps engineer, you need to investigate:

- Which log entries contain errors?
- Which files contain a particular configuration value?
- Where is a specific shell script located?
- Which files are consuming more than 100 MB of disk space?
- Which application logs contain a particular error message?
- How can you filter unnecessary information from command output?
- How can you arrange server information alphabetically or numerically?

You cannot manually inspect thousands of files and log entries.

This is where Linux searching and filtering commands become important.

The commands covered in this topic are:

| Command | Purpose |
|---|---|
| `grep` | Search for text patterns |
| `find` | Search for files and directories |
| `locate` | Find files and directories by name |
| `sort` | Arrange lines of text alphabetically or numerically |

Let's understand each command through practical examples.

---

## 1. `grep` – Search for Text Patterns

**Purpose:** Searches for a specific text pattern inside a file or input.

The `grep` command is one of the most frequently used commands in Linux troubleshooting.

It is particularly useful when working with application logs, configuration files, and command output.

### Syntax

```bash
grep "pattern" filename
```

Where:

- `grep` is the search command.
- `pattern` is the text you want to find.
- `filename` is the file you want to search.

### Example

Suppose you have a file named `application.log`.

Its contents:

```text
Application started
Database connected
ERROR: Database connection failed
User authenticated
ERROR: Payment service unavailable
Application stopped
```

Now search for the word `ERROR`.

```bash
grep "ERROR" application.log
```

Output:

```text
ERROR: Database connection failed
ERROR: Payment service unavailable
```

The command displays only the lines containing `ERROR`.

### Understanding How It Works

Imagine the log file contains 10,000 lines.

You do not want to read every line manually.

Instead, you execute:

```bash
grep "ERROR" application.log
```

Linux searches the file and displays matching lines.

### Real-World Scenario

Imagine you're responsible for a Java Spring Boot application deployed on an AWS EC2 instance.

Users report that payments are failing.

You need to investigate the application logs.

First, navigate to the log directory:

```bash
cd /var/log/myapp
```

Search for payment-related errors:

```bash
grep "ERROR" application.log
```

You might find:

```text
2026-11-21 10:20:10 ERROR Payment service unavailable
2026-11-21 10:21:15 ERROR Database connection timeout
2026-11-21 10:22:30 ERROR Payment transaction failed
```

You now have a smaller set of information to investigate.

**DevOps Insight:** `grep` is one of the first commands you should think of when troubleshooting application failures. It helps you extract relevant information from large log files quickly.

---

## 2. `grep -r` – Search Recursively

**Purpose:** Searches for a text pattern inside files in a target directory and its subdirectories.

The `-r` option means recursive search.

Without `-r`, you normally specify the individual file or files you want to search.

With `-r`, you can search an entire directory structure.

### Syntax

```bash
grep -r "pattern" /path/to/directory
```

### Example

Suppose your application has the following directory structure:

```text
/var/www/myapp/
│
├── app.py
├── config/
│   ├── database.conf
│   └── application.conf
│
├── logs/
│   ├── application.log
│   └── error.log
│
└── scripts/
    ├── deploy.sh
    └── backup.sh
```

You want to find every occurrence of `DATABASE_URL`.

Execute:

```bash
grep -r "DATABASE_URL" /var/www/myapp
```

Possible output:

```text
/var/www/myapp/config/database.conf:DATABASE_URL=localhost
/var/www/myapp/config/application.conf:DATABASE_URL=production-db
/var/www/myapp/scripts/deploy.sh:export DATABASE_URL=localhost
```

The command searches through files in the directory and its subdirectories.

### Important Concept

Compare:

```bash
grep "DATABASE_URL" database.conf
```

This searches a specific file.

Now:

```bash
grep -r "DATABASE_URL" /var/www/myapp
```

This searches recursively through the directory tree.

### Real-World Scenario

Imagine your company has a large application with multiple configuration files.

Your database team has migrated the application to a new database server.

You need to identify where the old database hostname is still being referenced.

Execute:

```bash
grep -r "old-db.company.internal" /var/www/myapp
```

Possible output:

```text
/var/www/myapp/config/database.conf:DB_HOST=old-db.company.internal
/var/www/myapp/scripts/backup.sh:DB_HOST=old-db.company.internal
```

You have identified two locations that may need updating.

### Another Useful Example

Search for `ERROR` in all files under the application log directory:

```bash
grep -r "ERROR" /var/log/myapp
```

**DevOps Insight:** Recursive searching is useful when investigating configuration changes, finding hardcoded values, and identifying references across large application directories.

**Production caution:** Avoid recursively searching sensitive directories without considering permissions and exposure of secrets. You can also narrow your search to relevant directories or file types.

---

## 3. `grep -v` – Exclude Matching Lines

**Purpose:** Displays lines that do not match the specified pattern.

The `-v` option reverses the matching condition.

Normally, `grep` displays matching lines.

With `-v`, it displays non-matching lines.

### Syntax

```bash
grep -v "pattern" filename
```

### Example

Suppose `application.log` contains:

```text
INFO Application started
ERROR Database connection failed
INFO User authenticated
ERROR Payment service unavailable
INFO Server running
```

Now execute:

```bash
grep -v "ERROR" application.log
```

Output:

```text
INFO Application started
INFO User authenticated
INFO Server running
```

The command excludes every line containing `ERROR`.

### Understanding the Difference

| Command | Behaviour |
|---|---|
| `grep "ERROR" file` | Displays lines containing ERROR |
| `grep -v "ERROR" file` | Displays lines not containing ERROR |

### Real-World Scenario

Imagine you're monitoring a production application.

Your logs contain thousands of entries.

Most of them are normal informational messages, but you want to remove routine health-check messages from your investigation.

Example log:

```text
INFO Health check successful
INFO Health check successful
ERROR Database connection failed
INFO Health check successful
WARNING Memory usage high
INFO Health check successful
```

Execute:

```bash
grep -v "Health check successful" application.log
```

Output:

```text
ERROR Database connection failed
WARNING Memory usage high
```

Now you can focus on messages that may require investigation.

### Another Production Example

Suppose you want to exclude successful HTTP status entries from an access log:

```bash
grep -v "200" access.log
```

This excludes lines containing the text `200`.

**Important:** This is a text match, not an HTTP status-code-aware filter. It could also exclude lines where `200` appears somewhere else. For more precise filtering, use an appropriate log format and field-aware tools.

**DevOps Insight:** `grep -v` is useful when filtering out noise from logs and focusing on potentially important information.

---

## 4. `grep -l` – Display Matching Filenames

**Purpose:** Displays only the names of files that contain the specified pattern.

Normally, `grep` displays matching lines.

With the `-l` option, it displays the filenames instead.

### Syntax

```bash
grep -l "pattern" filename
```

You can also use it with multiple files:

```bash
grep -l "pattern" file1 file2 file3
```

### Example

Suppose you have three files:

```text
application.log
database.log
payment.log
```

Their contents:

**application.log**

```text
Application started
Server running
```

**database.log**

```text
Database connected
ERROR Database connection failed
```

**payment.log**

```text
Payment service started
ERROR Payment gateway unavailable
```

Now execute:

```bash
grep -l "ERROR" *.log
```

Output:

```text
database.log
payment.log
```

Only filenames containing `ERROR` are displayed.

### Understanding the Difference

Normal grep:

```bash
grep "ERROR" *.log
```

Displays matching lines and their filenames when multiple files are searched.

With `-l`:

```bash
grep -l "ERROR" *.log
```

Displays only filenames containing the pattern.

### Real-World Scenario

Imagine you're working on a microservices application.

Your application has separate log files for:

- Authentication service.
- Payment service.
- Notification service.
- Inventory service.

Your team reports that an error is occurring somewhere in the system.

You want to identify which service log files contain the error.

Execute:

```bash
grep -l "Connection timeout" /var/log/myapp/*.log
```

Possible output:

```text
/var/log/myapp/payment.log
/var/log/myapp/inventory.log
```

Now you know which service logs require further investigation.

### Another Useful Example

Search recursively and display only filenames:

```bash
grep -rl "DATABASE_URL" /var/www/myapp
```

This searches recursively and displays filenames containing `DATABASE_URL`.

**DevOps Insight:** `grep -l` is useful when working with large repositories, multiple application logs, and configuration directories where you need to identify affected files without displaying their complete contents.

---

## 5. `grep -i` – Case-Insensitive Search

**Purpose:** Searches for a pattern without considering uppercase or lowercase differences.

By default, grep is case-sensitive.

For example:

```bash
grep "ERROR" application.log
```

This searches for uppercase `ERROR`.

It may not match:

```text
error
Error
ERROR
```

To ignore case differences, use `-i`.

### Syntax

```bash
grep -i "pattern" filename
```

### Example

Suppose `application.log` contains:

```text
ERROR Database failed
error Payment failed
Error Authentication failed
INFO Application started
```

Execute:

```bash
grep -i "error" application.log
```

Output:

```text
ERROR Database failed
error Payment failed
Error Authentication failed
```

All three variations are matched.

### Real-World Scenario

Imagine you're investigating a production server.

Different developers have written application log messages using different capitalization styles.

For example:

```text
ERROR: Database unavailable
Error: Payment failed
error: Authentication failed
```

If you search using:

```bash
grep "ERROR" application.log
```

You may miss some messages.

Instead, execute:

```bash
grep -i "error" application.log
```

Now you can find all three variations.

### Another Production Example

Search for database-related messages without worrying about capitalization:

```bash
grep -i "database" application.log
```

**DevOps Insight:** Case-insensitive searching is useful when applications, developers, or different services use inconsistent capitalization in their log messages.

---

## 6. `find` – Search for Files and Directories

**Purpose:** Searches for files and directories within a specified location.

Unlike `grep`, which searches for text inside files, `find` searches the filesystem for files and directories based on conditions.

### Syntax

```bash
find /starting/path [conditions]
```

### Example

```bash
find /var/www
```

This lists entries found under `/var/www`, including files and directories.

### Understanding How It Works

Suppose your application directory looks like this:

```text
/var/www/myapp/
│
├── app.py
├── config/
│   └── database.conf
│
├── logs/
│   ├── application.log
│   └── error.log
│
└── scripts/
    ├── deploy.sh
    └── backup.sh
```

Execute:

```bash
find /var/www/myapp
```

The command searches the specified directory and its subdirectories.

### Real-World Scenario

Imagine you're responsible for an application deployed on a production server.

A developer tells you that a deployment script exists somewhere under `/var/www`.

You don't know its exact location.

You can use:

```bash
find /var/www
```

Then inspect the results to identify the required file.

However, searching the entire directory can produce a large amount of output.

The next commands help narrow down the search.

**DevOps Insight:** `find` is useful for filesystem discovery, maintenance tasks, cleanup operations, and locating application resources.

---

## 7. `find -name` – Search by Filename

**Purpose:** Finds files and directories whose names match a specified pattern.

The `-name` option allows you to search by filename.

### Syntax

```bash
find /path/to/directory -name "filename"
```

### Example

Suppose you want to find all shell scripts under `/var/www/myapp`.

Execute:

```bash
find /var/www/myapp -name "*.sh"
```

Possible output:

```text
/var/www/myapp/scripts/deploy.sh
/var/www/myapp/scripts/backup.sh
```

### Understanding `*.sh`

Let's understand the pattern:

| Pattern | Meaning |
|---|---|
| `*` | Matches any number of characters |
| `.sh` | Matches the `.sh` extension |

Therefore:

```bash
-name "*.sh"
```

finds names ending with `.sh`.

**Important:** Keep the pattern inside quotes. Otherwise, your shell may expand the wildcard before `find` receives it.

### Another Example

Find all files named `application.log`:

```bash
find /var/log -name "application.log"
```

Find all files ending in `.conf`:

```bash
find /etc -name "*.conf"
```

### Case-Insensitive Filename Search

You can also use `-iname`:

```bash
find /var/www -iname "*.SH"
```

This matches filenames with `.sh`, `.SH`, and other capitalization variations.

### Real-World Scenario

Imagine you're a DevOps engineer maintaining CI/CD deployment scripts.

Your team has several shell scripts distributed across application directories.

You need to identify all deployment scripts before updating their configuration.

Execute:

```bash
find /var/www/myapp -name "*.sh"
```

Possible output:

```text
/var/www/myapp/deploy.sh
/var/www/myapp/scripts/backup.sh
/var/www/myapp/scripts/start.sh
```

You can now review the scripts before modifying them.

**DevOps Insight:** Filename searching is useful when managing deployment scripts, configuration files, backups, and application resources across large directory structures.

---

## 8. `find -size` – Search by File Size

**Purpose:** Finds files based on their size.

This is particularly useful when a Linux server is running out of disk space.

### Syntax

```bash
find /path/to/directory -size +100M
```

### Understanding the Size Options

| Expression | Meaning |
|---|---|
| `+100M` | Greater than 100 MiB |
| `-100M` | Less than 100 MiB |
| `100M` | Matches the size rounded to the specified unit |

The `+` and `-` signs are used to specify size comparisons.

### Example

```bash
find /home -size +100M
```

This searches under `/home` for filesystem entries larger than 100 MiB.

For regular files only, use:

```bash
find /home -type f -size +100M
```

### Important Concept: `-type f`

The `-type f` condition restricts the search to regular files.

Other useful file types include:

| Option | Meaning |
|---|---|
| `-type f` | Regular files |
| `-type d` | Directories |
| `-type l` | Symbolic links |

### Real-World Scenario

Imagine your AWS EC2 server is running out of disk space.

Your monitoring system sends an alert:

```text
WARNING: Disk usage above 90%
```

You log into the server and need to identify large files.

Execute:

```bash
find /home -type f -size +100M
```

Possible output:

```text
/home/ubuntu/application-backup.tar.gz
/home/ubuntu/logs/application.log
/home/ubuntu/database-backup.sql
```

You have identified three large files that may be contributing to storage consumption.

### What Should You Do Next?

Do not immediately delete these files.

First, inspect their sizes:

```bash
ls -lh /home/ubuntu/logs/application.log
```

Then determine whether the files are:

- Required application logs.
- Old backups.
- Temporary files.
- Important database backups.

You may need to archive, rotate, transfer, or remove files depending on their purpose and retention requirements.

### Another Useful Command

Find regular files larger than 500 MiB:

```bash
find /var/log -type f -size +500M
```

**DevOps Insight:** Finding large files is a common production troubleshooting task. Before deleting anything, understand why the file exists and whether the application or another service still depends on it.

---

## 9. `locate` – Find Files by Name

**Purpose:** Finds files and directories by name using a prebuilt database of filesystem locations.

Unlike `find`, which searches the filesystem when you execute it, `locate` normally searches an indexed database.

### Syntax

```bash
locate filename
```

### Example

```bash
locate application.log
```

Possible output:

```text
/var/log/myapp/application.log
/home/ubuntu/application.log
/opt/company/backend/application.log
```

The command displays indexed paths matching the requested name.

### Understanding How It Works

Imagine your server contains thousands of files.

You need to find a file named:

```text
deploy.sh
```

Using `find`:

```bash
find / -name "deploy.sh"
```

This searches the filesystem from the root directory and can take time.

Using `locate`:

```bash
locate deploy.sh
```

This searches its location database and is often much faster.

### Important Concept: Database Updates

`locate` relies on an index.

If a file was recently created, moved, or deleted, the database may not reflect the latest filesystem state.

On systems using the `updatedb` utility, you can refresh the database with:

```bash
sudo updatedb
```

Then search again:

```bash
locate deploy.sh
```

The exact package and update command depend on the Linux distribution.

### Real-World Scenario

Imagine you're working on a Linux server with multiple application directories.

A developer asks you to locate a configuration file named:

```text
nginx.conf
```

Instead of manually checking multiple directories, execute:

```bash
locate nginx.conf
```

Possible output:

```text
/etc/nginx/nginx.conf
/usr/share/nginx/nginx.conf
```

You can now investigate the relevant configuration file.

### Difference Between `find` and `locate`

| `find` | `locate` |
|---|---|
| Searches the filesystem directly | Searches a prebuilt database |
| Can search using many conditions | Primarily searches indexed names and paths |
| Reflects current filesystem state during the search | May return outdated results |
| Can be slower for broad searches | Often faster for filename searches |

**DevOps Insight:** Use `locate` when you need to quickly identify a known filename. Use `find` when you need current filesystem results or more specific conditions such as file size, type, or modification time.

---

## 10. `sort` – Sort Lines of Text

**Purpose:** Arranges lines of text alphabetically or numerically.

The `sort` command is useful when organizing data, processing command output, and analyzing logs.

### Syntax

```bash
sort filename
```

### Example

Suppose you have a file named `names.txt`.

Its contents:

```text
David
Alex
Jasin
Charlie
```

Execute:

```bash
sort names.txt
```

Output:

```text
Alex
Charlie
David
Jasin
```

The lines are arranged alphabetically.

### Important Concept: Alphabetical Sorting

By default, `sort` arranges lines according to their text values and the current locale.

Example:

```bash
sort application_names.txt
```

This helps organize lists of application names.

### Numeric Sorting

Suppose you have a file named `numbers.txt`.

Its contents:

```text
100
20
5
50
```

If you execute:

```bash
sort numbers.txt
```

You may get:

```text
100
20
5
50
```

Why?

Because the default sort compares text rather than interpreting each line as a number.

Use the `-n` option:

```bash
sort -n numbers.txt
```

Output:

```text
5
20
50
100
```

The `-n` option performs numeric sorting.

### Important Options

| Command | Description |
|---|---|
| `sort file` | Sorts lines alphabetically |
| `sort -n file` | Sorts lines numerically |
| `sort -r file` | Reverses the sort order |
| `sort -u file` | Sorts and removes duplicate lines |
| `sort -nr file` | Sorts numbers in descending order |

### Example: Reverse Sorting

```bash
sort -r names.txt
```

Output:

```text
Jasin
David
Charlie
Alex
```

### Example: Remove Duplicates

Suppose `servers.txt` contains:

```text
server1
server2
server1
server3
server2
```

Execute:

```bash
sort -u servers.txt
```

Output:

```text
server1
server2
server3
```

The output is sorted and duplicate lines are removed.

### Real-World Scenario

Imagine you're a DevOps engineer maintaining multiple application servers.

You have a file containing server response times in milliseconds:

```text
450
120
800
250
100
```

You need to identify the slowest response times.

Execute:

```bash
sort -nr response_times.txt
```

Output:

```text
800
450
250
120
100
```

Now the largest response times appear first.

You can quickly identify the slowest values for further investigation.

### Another Production Example

Suppose you have a list of server names:

```text
web-02
web-01
database-01
web-02
database-02
```

Execute:

```bash
sort -u servers.txt
```

This produces an alphabetically sorted list without duplicates.

**DevOps Insight:** `sort` is frequently combined with commands such as `grep`, `uniq`, `cut`, and `awk` to process logs, server lists, and operational data.

---

# 🚀 Real-World Production Scenario

## Situation: Investigating High Disk Usage and Application Errors on an AWS EC2 Server

Imagine you're responsible for a production application running on an AWS EC2 Ubuntu instance.

Your monitoring system sends two alerts:

```text
ALERT 1: Disk usage above 90%
ALERT 2: Application error rate increased
```

Your manager asks you to investigate.

You need to:

1. Search application logs for errors.
2. Search recursively for database configuration references.
3. Identify which files contain a particular error.
4. Exclude normal informational messages.
5. Find large files consuming storage.
6. Locate deployment scripts.
7. Identify the relevant configuration files.
8. Sort operational data.

Let's perform the investigation step by step.

### Step 1: Navigate to the Application Log Directory

```bash
cd /var/log/myapp
```

### Step 2: Search for Application Errors

```bash
grep "ERROR" application.log
```

This displays lines containing `ERROR`.

### Step 3: Search for Errors Without Considering Capitalization

```bash
grep -i "error" application.log
```

This also finds `error` and `Error`.

### Step 4: Exclude Routine Health-Check Messages

```bash
grep -v "Health check successful" application.log
```

This removes matching health-check lines from the displayed results.

### Step 5: Find Which Log Files Contain Database Errors

```bash
grep -rl "Database connection failed" /var/log/myapp
```

This recursively searches the directory and displays only filenames containing the error.

### Step 6: Search for Database Configuration References

```bash
grep -r "DATABASE_URL" /var/www/myapp
```

This identifies files containing the database configuration variable.

### Step 7: Find Large Files

```bash
find /home -type f -size +100M
```

This identifies regular files larger than 100 MiB under `/home`.

### Step 8: Locate Deployment Scripts

```bash
find /var/www/myapp -name "*.sh"
```

This finds shell scripts in the application directory and its subdirectories.

### Step 9: Quickly Locate Nginx Configuration

```bash
locate nginx.conf
```

This searches the locate database for matching paths.

If results appear outdated, refresh the database where the system supports it:

```bash
sudo updatedb
```

### Step 10: Sort Application Response Times

Suppose you have a file called `response_times.txt`:

```text
450
120
800
250
100
```

Execute:

```bash
sort -nr response_times.txt
```

The largest response times appear first.

### What Did You Accomplish?

You used:

- `grep` to identify application errors.
- `grep -r` to search application directories recursively.
- `grep -v` to exclude unwanted messages.
- `grep -l` to identify affected files.
- `grep -i` to search without case sensitivity.
- `find` to search the filesystem.
- `find -name` to locate shell scripts.
- `find -size` to identify large files.
- `locate` to quickly find known filenames.
- `sort` to organize operational data.

**DevOps Insight:** These commands are essential when investigating production incidents. A DevOps engineer must be able to narrow down thousands of files and log entries into a small set of useful information before deciding what to fix.

---

# 📌 Quick Command Reference

| Command | Description |
|---|---|
| `grep "patt" file` | Search for a text pattern |
| `grep -r "patt" directory` | Search recursively |
| `grep -v "patt" file` | Display lines not matching the pattern |
| `grep -l "patt" files` | Display filenames containing the pattern |
| `grep -i "patt" file` | Perform case-insensitive search |
| `find directory` | Search for filesystem entries |
| `find directory -name "*.sh"` | Find files matching a filename pattern |
| `find /home -size +100M` | Find entries larger than 100 MiB |
| `locate filename` | Find paths using an indexed database |
| `sort file` | Sort lines alphabetically |
| `sort -n file` | Sort lines numerically |
| `sort -r file` | Reverse sort order |
| `sort -u file` | Sort and remove duplicate lines |
| `sort -nr file` | Sort numbers in descending order |

---

# 🎯 Interview Questions

### Basic Questions

1. What is the purpose of the `grep` command?
2. What is the difference between `grep` and `find`?
3. What does the `-r` option do in grep?
4. What is the purpose of `grep -v`?
5. What does `grep -l` display?
6. Why would you use `grep -i`?
7. What is the purpose of the `find` command?
8. How do you find all `.sh` files inside a directory?
9. What does `find /home -size +100M` do?
10. What is the purpose of `locate`?
11. What is the difference between `find` and `locate`?
12. What is the purpose of the `sort` command?

### Intermediate Questions

13. What is the difference between `sort` and `sort -n`?
14. How do you sort numerical values in descending order?
15. How do you remove duplicate lines while sorting?
16. How can you recursively search an application directory for a particular configuration variable?
17. How would you identify which log files contain a particular error message?
18. Why can `locate` sometimes return outdated file paths?

### Senior DevOps Engineer Scenario Questions

19. Your production application generates thousands of log entries. How would you extract only the error messages?

20. A database hostname has changed. How would you find every application configuration file that still references the old hostname?

21. Your EC2 instance is running out of disk space. How would you identify regular files larger than 100 MB?

22. Your microservices application has 20 different log files. How would you identify which files contain the message `Connection timeout`?

23. Your application logs contain `ERROR`, `Error`, and `error`. How would you find all three variations?

24. Your application logs contain thousands of successful health checks. How would you exclude those messages during troubleshooting?

25. You have a file containing server response times. How would you display the slowest response times first?

26. Your team has multiple deployment scripts across several directories. How would you locate every `.sh` file?

27. You need to locate `nginx.conf` quickly, but you don't know which directory contains it. Which command would you use?

28. You have a list of server names containing duplicates. How would you produce a sorted list of unique servers?

---

# 🧪 Hands-On Practice

Try these commands in your Linux environment.

- [ ] Create a file named `application.log` containing several INFO, ERROR, and WARNING messages.
- [ ] Use `grep` to find all lines containing `ERROR`.
- [ ] Use `grep -i` to find errors regardless of capitalization.
- [ ] Use `grep -v` to exclude INFO messages.
- [ ] Create three log files and use `grep -l` to identify which contain `ERROR`.
- [ ] Use `grep -r` to search for `DATABASE_URL` in an application directory.
- [ ] Use `find` to list files under a directory.
- [ ] Use `find -name "*.sh"` to locate shell scripts.
- [ ] Use `find -type f -size +100M` to find large regular files.
- [ ] Use `locate` to find `nginx.conf`.
- [ ] Create a file containing names and sort them alphabetically.
- [ ] Create a file containing numbers and sort them numerically.
- [ ] Use `sort -nr` to display the largest numbers first.
- [ ] Use `sort -u` to remove duplicate entries.

---

# 🏁 Key Takeaway

Linux searching and filtering commands help you find relevant information quickly without manually inspecting every file.

For a DevOps engineer, these commands are essential for:

- Production log investigation.
- Filesystem troubleshooting.
- Application configuration management.
- Disk-space investigation.
- CI/CD script discovery.
- Server administration.
- Operational data processing.

**Learning approach:** Understand the command, practise it in a Linux environment, and then apply it to a realistic production scenario.

---

**Progress:** Topic 03 — Linux Searching and Filtering 🔄 In Progress

**Next:** Continue with the remaining Linux Text Processing commands.

*Learning Linux one topic at a time, from fundamentals to production-level DevOps engineering.*
