# Linux File Permissions – chmod, chown & chgrp

> **Domain:** Linux System Administration / DevOps  
> **Difficulty:** Beginner to Intermediate  
> **Objective:** Understand Linux file permissions, modify access rights, and manage file ownership and groups.

---

# 1. Understanding Linux File Permissions

## 1.1 What are File Permissions?

Linux is a multi-user operating system. Multiple users can access the same system, but not everyone should have permission to modify or execute every file.

Linux uses file permissions to control who can:
- Read a file.
- Modify a file.
- Execute a file.
- Access a directory.

### Real-world DevOps scenario

Imagine a company running an application on an AWS EC2 Linux server.

Different users have different responsibilities:

- Developer: Writes application code.
- DevOps Engineer: Manages deployment scripts.
- Administrator: Manages server configurations.
- Application user: Runs the application.

You don't want every user to modify production configuration files.

Linux permissions help enforce these access restrictions.

## 1.2 Three Permission Categories

Linux divides file permissions into three categories:

| Category | Symbol | Description |
|---|---|---|
| User | `u` | Owner of the file |
| Group | `g` | Users belonging to the file's group |
| Others | `o` | All other users |
| All | `a` | User, group, and others |

## 1.3 Three Permission Types

| Permission | Symbol | Numeric Value | Description |
|---|---|---:|---|
| Read | `r` | 4 | Read file contents |
| Write | `w` | 2 | Modify file contents |
| Execute | `x` | 1 | Execute a file or access a directory |

### Understanding permissions using ls -l

```bash
ls -l app.sh
```

Example output:

```text
-rwxr-xr-- 1 jasin developers 1024 Nov 21 app.sh
```

Breakdown:

```text
-   rwx   r-x   r--
|    |     |     |
|    |     |     └── Others
|    |     └──────── Group
|    └────────────── Owner
└─────────────────── File type
```

Explanation:

- Owner (`jasin`): Read, write, execute.
- Group (`developers`): Read and execute.
- Others: Read only.

---

# 2. chmod – Change File Permissions

## 2.1 What is chmod?

`chmod` stands for Change Mode.

It is used to change the permissions of files and directories.

### Syntax

```bash
chmod [permissions] filename
```

There are two main ways to modify permissions:

1. Symbolic representation.
2. Numeric (octal) representation.

## 2.2 Symbolic Permissions

Symbolic permissions use letters to represent users and permission types.

### Syntax

```bash
chmod [who][operator][permission] filename
```

| Component | Meaning |
|---|---|
| `u` | User (owner) |
| `g` | Group |
| `o` | Others |
| `a` | All |
| `+` | Add permission |
| `-` | Remove permission |
| `=` | Set exact permissions |
| `r` | Read |
| `w` | Write |
| `x` | Execute |

## 2.3 Adding Execute Permission

```bash
chmod +x testfile
```

This adds execute permission to all applicable user categories: owner, group, and others, subject to the existing permissions.

### Real-world DevOps scenario

You have created a deployment script:

```bash
deploy.sh
```

When you try to execute it:

```bash
./deploy.sh
```

You receive:

```text
Permission denied
```

You can add execute permission:

```bash
chmod +x deploy.sh
```

Then execute:

```bash
./deploy.sh
```

**Why is this important?**

Deployment scripts, automation scripts, and maintenance scripts often need execute permissions.

## 2.4 Removing Write Permission

```bash
chmod u-w testfile
```

Explanation:

- `u`: File owner.
- `-`: Remove permission.
- `w`: Write permission.

This removes write permission from the file owner.

### Real-world scenario

A production configuration file should not be modified by its owner accidentally.

You can remove the owner's write permission:

```bash
chmod u-w production.conf
```

Note: The file owner or a privileged administrator may change the permissions again. This is an access restriction, not an immutable-file guarantee.

## 2.5 Adding and Removing Multiple Permissions

```bash
chmod u+wx,g-x,o=rx testfile
```

Explanation:

| Expression | Meaning |
|---|---|
| `u+wx` | Add write and execute permissions to owner |
| `g-x` | Remove execute permission from group |
| `o=rx` | Set others' permissions to read and execute only |

Multiple permission changes can be performed in one command by separating expressions with commas.

## 2.6 Using the Equal Operator (=)

The `=` operator sets exact permissions for the specified category.

Example:

```bash
chmod u=rw,g=r,o= testfile
```

Result:

- Owner: Read and write.
- Group: Read only.
- Others: No permissions.

This is useful when you want to explicitly define access instead of adding or removing individual permissions.

---

# 3. Numeric (Octal) Permission Representation

## 3.1 Understanding Numeric Permissions

Linux also allows permissions to be represented numerically.

Each permission has a numeric value:

| Permission | Value |
|---|---:|
| Read (`r`) | 4 |
| Write (`w`) | 2 |
| Execute (`x`) | 1 |

These values are added together to calculate the final permission number.

## 3.2 Numeric Permission Table

| Octal | Permission | Symbolic |
|---:|---|---|
| 0 | No permissions | `---` |
| 1 | Execute only | `--x` |
| 2 | Write only | `-w-` |
| 3 | Write + Execute | `-wx` |
| 4 | Read only | `r--` |
| 5 | Read + Execute | `r-x` |
| 6 | Read + Write | `rw-` |
| 7 | Read + Write + Execute | `rwx` |

### How does calculation work?

Read = 4  
Write = 2  
Execute = 1

Examples:

```text
7 = 4 + 2 + 1 = rwx
6 = 4 + 2     = rw-
5 = 4 + 1     = r-x
4 = 4         = r--
3 = 2 + 1     = -wx
2 = 2         = -w-
1 = 1         = --x
0 = 0         = ---
```

## 3.3 Understanding Three-Digit Permissions

A three-digit permission number represents:

```text
chmod XYZ filename

X = Owner
Y = Group
Z = Others
```

Example:

```bash
chmod 755 app.sh
```

Breakdown:

| Category | Value | Permissions |
|---|---:|---|
| Owner | 7 | `rwx` |
| Group | 5 | `r-x` |
| Others | 5 | `r-x` |

Final permission:

```text
-rwxr-xr-x
```

## 3.4 Common Numeric Permission Examples

### Example 1: chmod 777

```bash
chmod 777 testfile
```

Permissions:

```text
-rwxrwxrwx
```

All users receive read, write, and execute permissions.

**Security warning:** Avoid using `777` on production systems unless there is a very specific and justified requirement. It allows everyone to modify the file.

### Example 2: chmod 177

```bash
chmod 177 testfile
```

Permissions:

```text
---rwxrwx
```

Breakdown:

- Owner: No permissions.
- Group: Read, write, execute.
- Others: Read, write, execute.

**Important correction:** `177` does not mean execute-only for the owner. It means the owner gets no permissions, group and others get all permissions, and the leading `1` sets the sticky special permission bit.

### Example 3: chmod 365

```bash
chmod 365 testfile
```

Permissions:

```text
-wxr--r-x
```

Breakdown:

| Category | Value | Permissions |
|---|---:|---|
| Owner | 3 | Write + Execute |
| Group | 6 | Read + Write |
| Others | 5 | Read + Execute |

### Example 4: chmod 644

```bash
chmod 644 config.txt
```

Permissions:

```text
-rw-r--r--
```

- Owner: Read and write.
- Group: Read only.
- Others: Read only.

**Real-world use:** Common for non-sensitive configuration files and ordinary web content.

### Example 5: chmod 600

```bash
chmod 600 private.key
```

Permissions:

```text
-rw-------
```

- Owner: Read and write.
- Group: No permissions.
- Others: No permissions.

**Real-world use:** Protecting private key files and other sensitive files.

### Example 6: chmod 755

```bash
chmod 755 deploy.sh
```

Permissions:

```text
-rwxr-xr-x
```

- Owner: Read, write, execute.
- Group: Read and execute.
- Others: Read and execute.

**Real-world use:** Executable scripts that should be runnable by other users.

---

# 4. chown – Change File Ownership

## 4.1 What is chown?

`chown` stands for Change Owner.

It is used to change the owner of a file or directory.

### Syntax

```bash
chown username filename
```

Example:

```bash
sudo chown jasin app.py
```

This changes the owner of `app.py` to `jasin`.

### Real-world DevOps scenario

Imagine a developer creates an application file using the root account.

The application is supposed to run under a dedicated account named `appuser`.

However, the file is owned by root.

You can change ownership:

```bash
sudo chown appuser app.py
```

Now `appuser` owns the file.

## 4.2 Change Owner and Group Together

```bash
sudo chown appuser:developers app.py
```

Explanation:

- `appuser`: New owner.
- `developers`: New group.
- `app.py`: Target file.

This changes both ownership and group.

## 4.3 Change Ownership Recursively

```bash
sudo chown -R appuser:developers /var/www/app/
```

The `-R` option means recursive.

It changes ownership of the directory and its contents.

### Real-world scenario

You deploy a website containing hundreds of files.

All application files should belong to the application service account.

Instead of changing ownership individually, use:

```bash
sudo chown -R www-data:www-data /var/www/html/
```

This is a common example on Debian/Ubuntu web servers.

**Warning:** Recursive ownership changes can affect application security and service operation. Always verify the target path and intended service account.

---

# 5. chgrp – Change Group Ownership

## 5.1 What is chgrp?

`chgrp` stands for Change Group.

It changes the group associated with a file or directory without changing its owner.

### Syntax

```bash
chgrp groupname filename
```

Example:

```bash
sudo chgrp developers app.py
```

This changes the file's group to `developers`.

## 5.2 Change Group Recursively

```bash
sudo chgrp -R developers /var/www/app/
```

This changes the group ownership of the directory and its contents.

### Real-world DevOps scenario

Imagine a development team shares an application directory.

The directory should be accessible to members of the `developers` group.

You can assign the directory to that group:

```bash
sudo chgrp -R developers /var/www/app/
```

Now the group ownership is updated.

Group membership and actual permissions must also be configured appropriately for team members to access the files.

---

# 6. Understanding chmod vs chown vs chgrp

| Command | Purpose | Example |
|---|---|---|
| `chmod` | Change permissions | `chmod 755 app.sh` |
| `chown` | Change owner | `chown jasin app.py` |
| `chgrp` | Change group | `chgrp developers app.py` |

Remember:

- `chmod` controls access permissions.
- `chown` controls file ownership.
- `chgrp` controls group ownership.

---

# 7. Real-World DevOps Scenario: Permission Denied

### Situation

You are deploying a web application on an Ubuntu server.

Your application files are located at:

```text
/var/www/html/
```

You attempt to execute a deployment script:

```bash
./deploy.sh
```

But Linux returns:

```text
Permission denied
```

### Step 1: Inspect permissions

```bash
ls -l deploy.sh
```

Example output:

```text
-rw-r--r-- 1 root root 1024 Nov 21 deploy.sh
```

The file does not have execute permission.

### Step 2: Add execute permission

```bash
chmod u+x deploy.sh
```

### Step 3: Verify permissions

```bash
ls -l deploy.sh
```

Expected output:

```text
-rwxr--r-- 1 root root 1024 Nov 21 deploy.sh
```

### Step 4: Execute the script

```bash
./deploy.sh
```

Now the owner has execute permission.

If another user needs to execute the script, the permissions must allow that user as well.

---

# 8. Useful Permission Management Commands

| Command | Purpose |
|---|---|
| `ls -l` | View file permissions and ownership |
| `ls -ld directory` | View directory permissions |
| `chmod 755 file` | Set numeric permissions |
| `chmod +x file` | Add execute permissions |
| `chmod u-w file` | Remove owner write permission |
| `chown user file` | Change file owner |
| `chown user:group file` | Change owner and group |
| `chown -R user:group dir` | Recursively change ownership |
| `chgrp group file` | Change group ownership |
| `chgrp -R group dir` | Recursively change group ownership |
| `id username` | Display user and group information |
| `groups username` | Display a user's groups |

---

# 9. Important Security Best Practices

1. Avoid `chmod 777` on production files.
2. Use the least privilege principle: give users only the permissions they need.
3. Restrict access to SSH private keys using appropriate permissions such as `600` or stricter where required.
4. Avoid unnecessary recursive permission and ownership changes.
5. Use dedicated application service accounts.
6. Verify permissions using `ls -l` after making changes.
7. Use `sudo` only when administrative privileges are necessary.
8. Remember that directory execute permission is required to traverse the directory.

---

# 10. Senior DevOps Interview Questions

### Basic Level

1. What are Linux file permissions?
2. What is the difference between user, group, and others?
3. What do read, write, and execute permissions mean?
4. What is the purpose of `chmod`?
5. What is the difference between `chown` and `chgrp`?

### Intermediate Level

6. Explain `chmod 755`.
7. Explain `chmod 644`.
8. What is the difference between symbolic and numeric permissions?
9. What does `chmod u-w file` do?
10. What is the purpose of the `-R` option?

### Real-World DevOps Scenarios

11. Your deployment script returns `Permission denied`. How would you troubleshoot it?
12. Your web server cannot read application files. How would you investigate ownership and permissions?
13. A private SSH key has overly permissive access. How would you secure it?
14. Your development team needs shared access to a project directory. How would you configure ownership and permissions?
15. Why is `chmod 777` considered dangerous in production?

---

# 11. Final Command Cheat Sheet

```bash
# View permissions
ls -l

# Add execute permission
chmod +x file

# Remove owner write permission
chmod u-w file

# Set exact symbolic permissions
chmod u=rw,g=r,o= file

# Numeric permissions
chmod 755 file
chmod 644 file
chmod 600 file

# Change owner
sudo chown jasin file

# Change owner and group
sudo chown jasin:developers file

# Change ownership recursively
sudo chown -R appuser:developers directory/

# Change group
sudo chgrp developers file

# Change group recursively
sudo chgrp -R developers directory/

# Inspect user and group information
id jasin
groups jasin
```

---

# 12. Learning Outcomes

After completing this topic, you should understand:

- Linux permission categories and permission types.
- Symbolic permission management.
- Numeric and octal permission calculations.
- How to use `chmod`, `chown`, and `chgrp`.
- How to troubleshoot permission-related errors.
- How Linux permissions protect production applications and server resources.

**End of Topic: Linux File Permissions**

Next: Practise permission management using real Linux scenarios and troubleshoot access-control problems like a DevOps engineer.
