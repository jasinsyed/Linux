# Topic 01 — Linux File Management

**Status:** Completed study / ongoing practice

File management is a core Linux administration skill. DevOps engineers use these commands to inspect application directories, create deployment structures, back up configuration, locate files, and investigate storage issues.

## 1. `pwd` — Print Working Directory

Displays the current directory.

```bash
pwd
```

**Scenario:** Before changing or deleting files on a server, confirm where your shell is currently located.

## 2. `ls` — List Directory Contents

```bash
ls
ls -l       # detailed listing
ls -a       # include hidden files
ls -lh      # human-readable sizes
ls -lt      # newest modification times first
ls -la      # detailed listing including hidden files
```

**Scenario:** An application cannot find its `.env` file. Use `ls -la /var/www/myapp` to inspect hidden files.

In `ls -l`, the output commonly shows file type and permissions, link count, owner, group, size, modification time, and name.

## 3. `cd` — Change Directory

```bash
cd /var/www/myapp
cd ..       # parent directory
cd ~        # current user's home directory
cd -        # previous directory
cd /        # filesystem root
```

**Scenario:** Navigate to `/var/log` to inspect system and application logs.

## 4. `mkdir` — Create Directories

```bash
mkdir logs
mkdir logs backups config
mkdir -p /opt/company/backend/logs
mkdir -p /opt/company/backend/{logs,backups,config}
```

`-p` creates missing parent directories and does not report an error merely because the target directory already exists.

**Scenario:** A deployment script prepares a consistent directory structure before copying application files.

## 5. `touch` — Create Files / Update Timestamps

```bash
touch app.log
touch app.log error.log access.log
```

Creates a file if it does not exist. If it already exists, `touch` updates timestamps; it does **not** erase its contents.

**Scenario:** Create an empty placeholder log file before starting a test application.

## 6. `cp` — Copy Files and Directories

```bash
cp config.json config.json.bak
cp -r myapp myapp_backup
cp app.log /tmp/
```

`-r` copies a directory recursively.

**Scenario:** Make a backup before editing a production configuration file. Protect backups that contain secrets.

## 7. `mv` — Move or Rename

```bash
mv app_old.py app.py
mv app.log logs/
```

`mv` moves a file or renames it; unlike `cp`, it does not leave a separate original copy at the source path.

**Scenario:** Organise application logs into a dedicated directory. Check whether the application depends on the old path.

## 8. `rm` and `rmdir` — Remove Files and Directories

```bash
rm app.log
rmdir empty_directory
rm -r old_logs
rm -f old.log
```

- `rmdir` removes empty directories only.
- `rm -r` recursively removes a directory and its contents.
- `rm -f` suppresses prompts/errors for certain missing or protected-path cases; it does not override all permissions.

**Production safety:** Inspect the target path and contents before deletion. Avoid running destructive commands with elevated privileges unless necessary and authorized. Never blindly execute `rm -rf` with an unverified path.

## 9. `find` — Search for Files and Directories

```bash
find /var/www -name "config.json"
find /var/log -name "*.log"
find /var/www -type d
find /var/www -type f
find /var/log -type f -size +100M
```

**Scenario:** A server is running low on disk space. Find large log files first, inspect them, then decide whether to rotate, archive, or remove them. Do not automatically delete every result.

## 10. `file` — Identify File Type

```bash
file backup
file app.py
```

Examines file contents to identify its type. Linux does not rely only on filename extensions.

**Scenario:** Verify the actual format of a downloaded backup before attempting to extract it.

## 11. `stat` — Inspect File Metadata

```bash
stat app.log
```

Displays metadata such as size, permissions, owner, inode, and timestamps.

| Timestamp | Meaning |
|---|---|
| atime | Last access time |
| mtime | Last content modification time |
| ctime | Last inode status or metadata change |

`ctime` is not the file creation time.

**Scenario:** Investigate when a configuration file's contents were last modified and inspect its ownership and permissions.

---

## Production practice scenario

Prepare a sample application directory. Run these commands in a test environment:

```bash
pwd
mkdir -p ~/devops-lab/backend/{config,logs,backups}
touch ~/devops-lab/backend/app.py
touch ~/devops-lab/backend/config/config.json
touch ~/devops-lab/backend/logs/app.log
cp ~/devops-lab/backend/config/config.json    ~/devops-lab/backend/backups/config.json.bak
stat ~/devops-lab/backend/config/config.json
find ~/devops-lab/backend -type f
ls -R ~/devops-lab/backend
```

Expected structure:

```text
backend/
├── app.py
├── backups/
│   └── config.json.bak
├── config/
│   └── config.json
└── logs/
    └── app.log
```

## Quick reference

| Command | Purpose |
|---|---|
| `pwd` | Show current directory |
| `ls` | List directory contents |
| `cd` | Change directory |
| `mkdir` | Create directories |
| `touch` | Create file / update timestamps |
| `cp` | Copy files and directories |
| `mv` | Move or rename |
| `rm` | Remove files/directories |
| `rmdir` | Remove empty directory |
| `find` | Search filesystem |
| `file` | Identify file type |
| `stat` | Display metadata |

## Interview questions

1. What is the difference between `cp` and `mv`?
2. Why would you use `mkdir -p` in a deployment script?
3. Does `touch` erase an existing file's contents?
4. How do `ls -l` and `ls -la` differ?
5. How do you find regular files larger than 100 MB?
6. What is the difference between `rm -r` and `rmdir`?
7. How can you identify a file's actual type?
8. What is the difference between atime, mtime, and ctime?
9. How would you investigate large log files on a server?
10. What precautions should you take before using `rm -rf`?

## Completion checklist

- [x] Understand the purpose of each command
- [x] Review practical examples and production scenarios
- [ ] Practise commands in a Linux environment
- [ ] Answer scenario-based interview questions
