# 🐧 Linux User and Group Management

> **Linux & DevOps Learning Journey — User and Group Management**

In Linux, user and group management is an essential responsibility for system administrators and DevOps engineers.

Every user accessing a Linux server has an identity, permissions, and group memberships. Managing these correctly helps maintain server security, control access, and prevent unauthorized operations.

This topic covers commands used to identify logged-in users, inspect user and group information, and create, modify, or delete users and groups.

---

## 📚 Table of Contents

1. 👥 [Checking Logged-in Users](#1--checking-logged-in-users)
2. 🪪 [Identifying the Current User](#2--identifying-the-current-user)
3. 🔍 [Understanding User and Group IDs](#3--understanding-user-and-group-ids)
4. 🕒 [Checking Login History](#4--checking-login-history)
5. 👨‍👩‍👧 [Creating and Managing Groups](#5--creating-and-managing-groups)
6. 👤 [Creating Linux Users](#6--creating-linux-users)
7. 🗑️ [Deleting Linux Users](#7--deleting-linux-users)
8. 🔧 [Modifying User Accounts](#8--modifying-user-accounts)
9. 🚀 [Real-World Production Scenario](#9--real-world-production-scenario)
10. 📌 [Quick Command Reference](#10--quick-command-reference)
11. 🎯 [Interview Questions](#11--interview-questions)
12. 🧪 [Hands-On Practice](#12--hands-on-practice)

---

## 🎯 Introduction

Linux is a multi-user operating system. Multiple users can access the same server simultaneously, and each user can have different permissions.

For example, imagine a production server where:

- Developers need access to application files.
- DevOps engineers need permission to manage deployments.
- Database administrators need access to database-related resources.
- System administrators need elevated privileges.

Instead of giving everyone unrestricted access, Linux allows administrators to create individual accounts and organize users into groups.

**DevOps Insight:**

In production environments, user and group management is important for access control, auditing, and the principle of least privilege.

---

## 1. 👥 Checking Logged-in Users

These commands help administrators identify who is currently connected to a Linux system.

### 1.1 `who` — Display Logged-in Users

**Syntax:**

```bash
who
```

Displays information about users currently logged into the system.

**Example:**

```bash
who
```

**Output:**

```text
alice    pts/0        2026-11-21 09:30
bob      pts/1        2026-11-21 09:45
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| alice / bob | Username |
| pts/0 / pts/1 | Terminal session |
| Date | Login date |
| Time | Login time |

**DevOps Insight:**

When investigating unexpected server activity, `who` helps identify users who currently have active login sessions.

### 1.2 `w` — Display What Users Are Doing

**Syntax:**

```bash
w
```

Displays currently logged-in users along with their activity and system information.

**Example:**

```bash
w
```

**Output:**

```text
20:30:10 up 12 days,  4:15,  2 users, load average: 0.25, 0.18, 0.12

USER   TTY    FROM       LOGIN@   IDLE   JCPU   PCPU  WHAT
alice  pts/0  10.0.0.10  09:30    2:00   0.10s  0.02s  bash
bob    pts/1  10.0.0.11  09:45    0.00s  0.15s  0.05s  top
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| USER | Logged-in username |
| TTY | Terminal |
| FROM | Remote host or IP address |
| LOGIN@ | Login time |
| IDLE | Time since last activity |
| JCPU | CPU time used by terminal-related processes |
| PCPU | CPU time used by the current process |
| WHAT | Current command or activity |

**DevOps Insight:**

Suppose a production server is experiencing high CPU usage. Running `w` helps you see which users are logged in and what commands they are running.

### 1.3 `users` — List Current Users

**Syntax:**

```bash
users
```

Displays the usernames of users currently logged in.

**Example:**

```bash
users
```

**Output:**

```text
alice bob root
```

Unlike `who` and `w`, this command provides a simple list of usernames without detailed session information.

**DevOps Insight:**

Useful for quickly checking which user accounts have active sessions.

---

## 2. 🪪 Identifying the Current User

### 2.1 `whoami` — Display Current Username

**Syntax:**

```bash
whoami
```

Displays the username of the account running the current command.

**Example:**

```bash
whoami
```

**Output:**

```text
jasin
```

If you switch to another user, the output changes.

```bash
sudo -i
whoami
```

**Output:**

```text
root
```

**DevOps Insight:**

Before executing administrative commands or deployment scripts, checking the current user helps prevent accidentally running operations with the wrong account.

---

## 3. 🔍 Understanding User and Group IDs

### 3.1 `id` — Display User and Group Information

**Syntax:**

```bash
id
```

Displays the current user's UID, primary group ID, and supplementary group memberships.

**Example:**

```bash
id
```

**Output:**

```text
uid=1000(jasin) gid=1000(jasin) groups=1000(jasin),27(sudo),1001(devops)
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| uid | User ID (UID) |
| gid | Primary Group ID (GID) |
| groups | All groups the user belongs to |

**Important concepts:**

- **UID:** A numerical identifier assigned to a Linux user.
- **GID:** A numerical identifier assigned to a Linux group.
- **Primary group:** The main group associated with the user.
- **Supplementary groups:** Additional groups that provide extra permissions.

You can also inspect another user:

```bash
id alice
```

**Example output:**

```text
uid=1001(alice) gid=1001(alice) groups=1001(alice),1002(developers)
```

**DevOps Insight:**

When a developer cannot access an application directory, checking their group memberships using `id username` is a useful troubleshooting step.

---

## 4. 🕒 Checking Login History

### 4.1 `last` — Display Previous Login Sessions

**Syntax:**

```bash
last
```

Displays information about previous login sessions.

**Example:**

```bash
last
```

**Output:**

```text
alice    pts/0    10.0.0.10    Fri Nov 21 09:30   still logged in
bob      pts/1    10.0.0.11    Fri Nov 21 08:45 - 10:15
reboot   system boot            Fri Nov 21 06:00
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| Username | Account that logged in |
| Terminal | Login terminal |
| IP address | Remote connection address, when available |
| Login time | When the session started |
| Session status | Whether the session is still active or has ended |

**Useful variations:**

```bash
last -n 5
```

Displays the five most recent login records.

```bash
last alice
```

Displays login history for the user `alice`.

**DevOps Insight:**

When investigating suspicious access to a server, `last` can help administrators review historical login sessions.

---

## 5. 👨‍👩‍👧 Creating and Managing Groups

Linux groups allow administrators to manage permissions for multiple users together.

For example, instead of granting access to every developer individually, you can create a `developers` group and assign users to it.

### 5.1 `groupadd` — Create a New Group

**Syntax:**

```bash
groupadd group_name
```

**Example:**

```bash
sudo groupadd gp1
```

Creates a new group named `gp1`.

**Verify the group:**

```bash
getent group gp1
```

**Example output:**

```text
gp1:x:1002:
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| gp1 | Group name |
| x | Group password placeholder |
| 1002 | Group ID (GID) |
| Empty field | Supplementary group members |

**DevOps Insight:**

For a production application, you might create groups such as `developers`, `devops`, and `monitoring` to organize access to different resources.

---

## 6. 👤 Creating Linux Users

### 6.1 `useradd` — Create a New User Account

**Syntax:**

```bash
useradd [options] username
```

Creates a new Linux user account.

**Example:**

```bash
sudo useradd -c "Alice Bob" -m ab1
```

Creates a user account named `ab1`, adds the comment `Alice Bob`, and creates the user's home directory.

**Understanding the options:**

| Option | Meaning |
|---|---|
| `-c` | Adds a comment or description to the user account |
| `-m` | Creates the user's home directory |
| `ab1` | Username |

**What happens internally?**

When you execute:

```bash
sudo useradd -c "Alice Bob" -m ab1
```

Linux generally:

1. Creates a user account named `ab1`.
2. Assigns a UID.
3. Creates a primary group according to the system's user-creation configuration.
4. Creates `/home/ab1`.
5. Copies default configuration files into the home directory when configured.
6. Stores the account information in system account databases.

**Verify the user:**

```bash
id ab1
```

**Check the home directory:**

```bash
ls -ld /home/ab1
```

**Important:** Creating a user account does not necessarily set a usable login password.

To assign a password:

```bash
sudo passwd ab1
```

**DevOps Insight:**

When a new developer joins a company, a system administrator can create an individual Linux account instead of sharing a common server login.

---

## 7. 🗑️ Deleting Linux Users

### 7.1 `userdel` — Delete a User Account

**Syntax:**

```bash
userdel username
```

Deletes the specified user account.

**Example:**

```bash
sudo userdel ab1
```

Deletes the account named `ab1`.

**Important:** By default, `userdel` does not remove the user's home directory.

To delete the account and its home directory:

```bash
sudo userdel -r ab1
```

The `-r` option removes the user's home directory and mail spool where applicable.

**Verify deletion:**

```bash
id ab1
```

If the account has been deleted, the command reports that the user does not exist.

<blockquote>
**⚠️ Production Warning**

Be careful when deleting users from production servers. Their home directories may contain SSH keys, scripts, configuration files, or important data. Always verify ownership and back up required files before deletion.
</blockquote>

**DevOps Insight:**

When an employee leaves an organization, administrators must remove or disable their access according to the company's offboarding process.

---

## 8. 🔧 Modifying User Accounts

### 8.1 `usermod` — Modify an Existing User

**Syntax:**

```bash
usermod [options] username
```

Modifies an existing Linux user account.

### Add a User to a Group

**Example:**

```bash
sudo usermod -aG gp1 ab1
```

Adds the user `ab1` to the supplementary group `gp1`.

**Understanding the options:**

| Option | Meaning |
|---|---|
| `-a` | Append the group instead of replacing existing supplementary groups |
| `-G` | Specify supplementary group membership |
| `gp1` | Group name |
| `ab1` | Username |

**Why are `-aG` important?**

Consider this command:

```bash
sudo usermod -G gp1 ab1
```

It replaces the user's existing supplementary group memberships with `gp1`.

However:

```bash
sudo usermod -aG gp1 ab1
```

Adds `gp1` while preserving existing supplementary groups.

**Verify group membership:**

```bash
id ab1
```

Or:

```bash
groups ab1
```

**Important:** The user may need to log out and log back in for the new group membership to become active in an existing session.

**DevOps Insight:**

Imagine a developer needs access to a deployment directory owned by the `devops` group. Instead of changing permissions for everyone, an administrator can add that developer to the appropriate group.

```bash
sudo usermod -aG devops alice
```

This is a common way to manage access through group-based permissions.

---

## 9. 🚀 Real-World Production Scenario

### Scenario: Onboarding a New Developer

Imagine you are a DevOps engineer responsible for managing a Linux application server.

A new developer named Alice joins your team. She needs access to application files managed by the `developers` group.

**Step 1: Check currently logged-in users**

```bash
who
```

**Step 2: Check your administrative identity**

```bash
whoami
```

**Step 3: Create the developers group**

```bash
sudo groupadd developers
```

If the group already exists, verify it rather than creating it again.

```bash
getent group developers
```

**Step 4: Create Alice's account**

```bash
sudo useradd -c "Alice Bob" -m alice
```

**Step 5: Set Alice's password**

```bash
sudo passwd alice
```

**Step 6: Add Alice to the developers group**

```bash
sudo usermod -aG developers alice
```

**Step 7: Verify her account and group memberships**

```bash
id alice
```

**Step 8: Review login history when required**

```bash
last alice
```

**Result:**

Alice now has an individual Linux account and membership in the `developers` group. Access to application files can be managed through group ownership and file permissions.

---

## 10. 📌 Quick Command Reference

| Command | Description |
|---|---|
| `who` | Display currently logged-in users |
| `w` | Display logged-in users and their activities |
| `users` | List current users |
| `whoami` | Display the current username |
| `id` | Display user and group IDs |
| `last` | Display previous login sessions |
| `groupadd gp1` | Create a group named `gp1` |
| `useradd -c "Alice Bob" -m ab1` | Create user `ab1` with a comment and home directory |
| `userdel ab1` | Delete user `ab1` |
| `usermod -aG gp1 ab1` | Add user `ab1` to group `gp1` |
| `passwd ab1` | Set or change the password for `ab1` |
| `groups ab1` | Display groups associated with `ab1` |
| `getent group gp1` | Display information about group `gp1` |

---

## 11. 🎯 Interview Questions

### Basic Level

**Q1. What is the difference between `who` and `whoami`?**

- `who` displays users currently logged into the system.
- `whoami` displays the username of the account executing the command.

**Q2. What does the `id` command display?**

It displays the user's UID, primary GID, and supplementary group memberships.

**Q3. What is the purpose of `groupadd`?**

It creates a new Linux group.

**Q4. What is the purpose of `useradd -m`?**

It creates a user account and its home directory.

### Intermediate Level

**Q5. What is the difference between `userdel` and `userdel -r`?**

- `userdel` removes the user account.
- `userdel -r` also removes the user's home directory and mail spool where applicable.

**Q6. What is the difference between `-G` and `-aG` in `usermod`?**

- `-G` replaces supplementary group memberships.
- `-aG` appends a group while preserving existing supplementary memberships.

**Q7. What is the difference between UID and GID?**

- UID identifies a Linux user.
- GID identifies a Linux group.

### Senior DevOps Scenario-Based Questions

**Q8. A developer says they cannot access a directory even after you added them to the required group. What would you check?**

Possible troubleshooting steps:

```bash
id alice
groups alice
ls -ld /var/www/application
```

Check whether:

- The user belongs to the correct group.
- The directory has the correct group ownership.
- Group permissions allow the required operation.
- The user has started a new login session to refresh group membership.

**Q9. A new employee joins your company. How would you provide secure Linux server access?**

I would:

1. Create an individual user account.
2. Assign a secure authentication method.
3. Add the user to only the required groups.
4. Configure appropriate directory permissions.
5. Verify access using `id` and permission checks.
6. Follow the organization's access-review and auditing procedures.

**Q10. Why should developers not share a common Linux account?**

Individual accounts provide accountability, easier auditing, separate permissions, and better access revocation when someone leaves the organization.

---

## 12. 🧪 Hands-On Practice

Complete these exercises on a Linux virtual machine or a test environment.

- [ ] Run `who` and identify logged-in users.
- [ ] Run `w` and inspect user activity.
- [ ] Run `users` and list current users.
- [ ] Run `whoami` to identify your current account.
- [ ] Run `id` and identify your UID and GID.
- [ ] Run `last` and inspect login history.
- [ ] Create a group named `gp1`.
- [ ] Create a user named `ab1` with a home directory.
- [ ] Assign a password to `ab1`.
- [ ] Add `ab1` to `gp1` using `usermod -aG`.
- [ ] Verify the user's group membership using `id ab1`.
- [ ] Delete the test user after completing the exercises.

**Practice commands:**

```bash
sudo groupadd gp1

sudo useradd -c "Alice Bob" -m ab1

sudo passwd ab1

sudo usermod -aG gp1 ab1

id ab1

groups ab1

sudo userdel -r ab1
```

---

## 🏁 Key Takeaway

User and group management is one of the fundamental responsibilities of a Linux administrator and DevOps engineer.

Understanding these commands helps you:

- Identify users accessing a server.
- Monitor login activity.
- Understand user and group identities.
- Create and manage user accounts.
- Organize permissions through groups.
- Control access to production infrastructure.

**Remember:** In production environments, follow the principle of least privilege. Give every user only the access required to perform their job.

---

**Next Topic:** Continue with the next Linux command category in your learning roadmap.
