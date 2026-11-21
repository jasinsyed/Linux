# 🐧 Linux Shell Scripting – Part 1: Fundamentals, Shebang, Variables & Arguments

> **Roadmap:** Topic 12 of 12  
> **Topic:** Shell Scripting  
> **Part:** 1 of 3  
> **Domain:** Linux Administration / DevOps / Cloud Engineering  
> **Difficulty:** Beginner to Intermediate  
> **Objective:** Understand shell scripting fundamentals, write your first Bash scripts, work with variables, accept user input, process command-line arguments, and understand exit codes.

---

## 📚 Table of Contents

1. Introduction to Shell Scripting
2. Understanding the Shell
3. What Is a Shell Script?
4. Shebang – `#!/bin/bash`
5. Creating and Executing Shell Scripts
6. Comments in Shell Scripts
7. Variables in Bash
8. Environment Variables
9. User Input – `read`
10. Displaying Output – `echo` and `printf`
11. Command-Line Arguments
12. Special Variables in Bash
13. Exit Codes and Script Status
14. Practical DevOps Project – Server Information Script
15. Common Shell Scripting Mistakes
16. Production Best Practices
17. Quick Command Reference
18. Interview Questions
19. Hands-On Practice
20. Part 1 Completion Checklist

---

# 🎯 1. Introduction to Shell Scripting

## 1.1 What Is Shell Scripting?

Shell scripting is the process of writing a series of Linux commands inside a file and executing them together.

Instead of manually running commands one by one, you can write them in a script and execute the entire workflow with a single command.

### Example: Without Shell Scripting

Imagine you are checking a Linux server manually.

You execute:

```bash
date
```

Then:

```bash
hostname
```

Then:

```bash
uptime
```

Then:

```bash
free -h
```

Then:

```bash
df -h
```

You need to execute five different commands.

### Example: With Shell Scripting

Create a file named:

```text
server-info.sh
```

Write:

```bash
#!/bin/bash

date
hostname
uptime
free -h
df -h
```

Execute:

```bash
bash server-info.sh
```

All five commands execute automatically.

**This is the basic idea of shell scripting: automate repetitive Linux tasks.**

## 1.2 Why Is Shell Scripting Important for DevOps?

DevOps engineers frequently perform repetitive operational tasks.

Examples include:

- Checking server health.
- Monitoring disk space.
- Backing up application data.
- Deploying applications.
- Cleaning temporary files.
- Checking whether services are running.
- Collecting system information.
- Automating infrastructure operations.
- Executing scheduled maintenance tasks.

Instead of performing these operations manually, you can automate them using Bash scripts.

### Real-World DevOps Example

Imagine you manage 20 Linux servers.

Every morning, you need to check:

1. CPU usage.
2. Memory availability.
3. Disk utilization.
4. Running services.
5. System uptime.

Checking each server manually is time-consuming.

A shell script can automate these checks and generate a report.

That is why Bash scripting is an important skill for Linux administration, DevOps, and cloud engineering.

---

# 2. Understanding the Shell

## 2.1 What Is a Shell?

A shell is a command-line interpreter that accepts commands from users and executes them.

When you type a Linux command:

```bash
ls
```

The shell interprets the command and arranges for it to execute.

The shell acts as an interface between the user and the operating system.

### Basic Flow

```text
User
  |
  v
Shell
  |
  v
Command / Program
  |
  v
Linux Operating System
  |
  v
Output
```

## 2.2 Different Types of Shells

Linux supports several shells.

| Shell | Description |
|---|---|
| Bash | Bourne Again Shell; widely used for Linux scripting |
| sh | Traditional Unix shell interface |
| Zsh | Interactive shell with extensive customization |
| Fish | User-friendly interactive shell |
| Ksh | KornShell, another Unix shell |

### Check Your Current Shell

```bash
echo $SHELL
```

Example output:

```text
/bin/bash
```

This usually indicates your configured login shell.

To identify the shell currently interpreting your commands:

```bash
ps -p $$ -o comm=
```

### List Available Shells

```bash
cat /etc/shells
```

This displays shells configured as valid login shells on the system.

**Senior DevOps Insight:** We will primarily use Bash because it is widely available across Linux servers and commonly used in DevOps automation.

---

# 3. What Is a Shell Script?

A shell script is a text file containing commands that a shell executes.

Shell scripts commonly use the `.sh` extension.

Examples:

```text
backup.sh
deploy.sh
server-monitor.sh
cleanup.sh
database-backup.sh
```

The `.sh` extension is a naming convention. Bash does not require it to execute a script.

## 3.1 How Does a Shell Script Work?

Consider:

```bash
#!/bin/bash

echo "Starting application"

mkdir -p /tmp/myapp

echo "Application directory created"
```

When executed, Bash processes the commands in sequence.

Execution flow:

1. Read the script.
2. Interpret the commands.
3. Execute the first command.
4. Execute the second command.
5. Execute the third command.

This makes it possible to combine Linux commands into automated workflows.

---

# 4. Shebang – `#!/bin/bash`

## 4.1 What Is a Shebang?

The shebang is the first line of a script.

It tells the operating system which interpreter should execute the script when it is launched directly.

### Syntax

```bash
#!/path/to/interpreter
```

For Bash:

```bash
#!/bin/bash
```

### Example

Create:

```bash
nano hello.sh
```

Write:

```bash
#!/bin/bash

echo "Hello, DevOps Engineer!"
```

Save the file.

### Why Do We Need a Shebang?

Without a shebang, directly executing a script may not work as expected because the operating system does not have an explicit interpreter specified in the script.

The shebang makes the intended interpreter clear.

## 4.2 What Is `#!/usr/bin/env bash`?

You may also see:

```bash
#!/usr/bin/env bash
```

This asks the system to locate Bash using the `PATH` environment variable.

### Difference

| Shebang | Meaning |
|---|---|
| `#!/bin/bash` | Uses Bash at the specified path |
| `#!/usr/bin/env bash` | Locates Bash through the environment's PATH |

For scripts targeting a known Linux environment, `#!/bin/bash` is common.

For scripts intended to work across environments where Bash may be installed in different locations, `#!/usr/bin/env bash` can be useful.

**Senior DevOps Insight:** The shebang does not make a script executable. File permissions still matter.

---

# 5. Creating and Executing Shell Scripts

## 5.1 Create a Script

Use the `touch` command:

```bash
touch hello.sh
```

Or create and edit it using:

```bash
nano hello.sh
```

Add:

```bash
#!/bin/bash

echo "Welcome to Linux Shell Scripting"
```

## 5.2 Method 1: Execute Using Bash

```bash
bash hello.sh
```

Bash reads and executes the script.

This method does not require the executable permission bit.

## 5.3 Method 2: Execute Using sh

```bash
sh hello.sh
```

This invokes the `sh` interpreter.

**Important:** Do not assume `sh` and Bash are interchangeable. Bash-specific syntax may fail when executed through `sh`.

## 5.4 Method 3: Make the Script Executable

First, add execute permission:

```bash
chmod +x hello.sh
```

Then execute:

```bash
./hello.sh
```

### Understanding `./`

The `./` means the file is located in the current directory.

Linux generally does not search the current directory for commands automatically.

Therefore:

```bash
hello.sh
```

may fail if the current directory is not in your `PATH`.

But:

```bash
./hello.sh
```

explicitly executes the file from the current directory.

### Complete Example

```bash
touch hello.sh
nano hello.sh
chmod +x hello.sh
./hello.sh
```

Output:

```text
Welcome to Linux Shell Scripting
```

---

# 6. Comments in Shell Scripts

Comments are lines that explain what the script does.

The shell does not execute ordinary comments.

## 6.1 Single-Line Comments

Use `#`.

Example:

```bash
#!/bin/bash

# Display a welcome message
echo "Welcome to DevOps"
```

Here:

```bash
# Display a welcome message
```

is a comment.

## 6.2 Multiple Comments

```bash
#!/bin/bash

# This script collects
# basic Linux server
# information.

echo "Server Information"
hostname
uptime
```

Each comment line begins with `#`.

### Why Are Comments Important?

In production environments, scripts may be maintained by multiple engineers.

Comments help explain:

- What the script does.
- Why a particular operation exists.
- Which configuration is required.
- What an unusual command or workaround means.

**Senior DevOps Insight:** Comments should explain intent, especially when the reason for a command is not obvious. Avoid comments that simply repeat the command.

---

# 7. Variables in Bash

## 7.1 What Is a Variable?

A variable stores a value that can be reused throughout a script.

For example:

```bash
name="Jasin"
```

Here:

- `name` is the variable.
- `Jasin` is the value.

To access a variable, use `$`.

```bash
echo $name
```

Output:

```text
Jasin
```

## 7.2 Creating Variables

### Syntax

```bash
variable_name=value
```

Example:

```bash
name="Jasin"
age=22
city="Bangalore"
```

Display the values:

```bash
echo $name
echo $age
echo $city
```

Output:

```text
Jasin
22
Bangalore
```

### Important Rule: No Spaces Around `=`

Correct:

```bash
name="Jasin"
```

Incorrect:

```bash
name = "Jasin"
```

Why?

Bash interprets spaces as command and argument separators.

In the incorrect example, Bash attempts to execute a command named `name`.

## 7.3 Accessing Variables Using `${}`

You can access variables using either:

```bash
echo $name
```

or:

```bash
echo ${name}
```

Both work.

However, braces are especially useful when combining variables with other text.

Example:

```bash
name="Jasin"

echo "Welcome ${name}DevOps"
```

Output:

```text
Welcome JasinDevOps
```

Without braces, Bash would interpret `$nameDevOps` as one variable name.

## 7.4 Variables in a Script

Create:

```bash
nano user.sh
```

Write:

```bash
#!/bin/bash

name="Jasin"
role="DevOps Engineer"

echo "Name: $name"
echo "Role: $role"
```

Execute:

```bash
bash user.sh
```

Output:

```text
Name: Jasin
Role: DevOps Engineer
```

## 7.5 Read-Only Variables

You can prevent a variable from being reassigned using `readonly`.

Example:

```bash
#!/bin/bash

readonly SERVER="production-server-01"

echo "$SERVER"
```

Attempting to change it:

```bash
SERVER="development-server"
```

causes a shell error.

### Why Is This Useful?

Some values should remain unchanged during script execution.

For example:

- Critical configuration identifiers.
- Fixed environment labels.
- Constant directory paths.

Use read-only variables when appropriate.

## 7.6 Removing Variables

Use `unset`.

Example:

```bash
name="Jasin"

echo "$name"

unset name

echo "$name"
```

After `unset`, the variable no longer has its previous value.

**Important:** `unset` is different from assigning an empty string.

```bash
name=""
```

The variable still exists, but its value is empty.

---

# 8. Environment Variables

## 8.1 What Are Environment Variables?

Environment variables are variables made available to processes started from a shell.

They are commonly used to configure applications and scripts.

Examples include:

- `PATH`
- `HOME`
- `USER`
- `SHELL`
- `PWD`

### Display an Environment Variable

```bash
echo "$HOME"
```

Displays the current user's home directory.

```bash
echo "$USER"
```

Displays the username associated with the environment.

```bash
echo "$PATH"
```

Displays directories searched when executing commands.

## 8.2 Difference Between Shell Variables and Environment Variables

Consider:

```bash
name="Jasin"
```

This creates a shell variable.

It is available in the current shell but is not automatically passed to child processes.

Now consider:

```bash
export name="Jasin"
```

This creates an exported variable that is inherited by child processes.

### Example

Create a script:

```bash
#!/bin/bash

echo "User: $name"
```

Save it as:

```text
child.sh
```

Now, in your terminal:

```bash
name="Jasin"
bash child.sh
```

The child script generally will not receive the unexported variable.

Now execute:

```bash
export name="Jasin"
bash child.sh
```

Output:

```text
User: Jasin
```

The exported variable is inherited by the child process.

## 8.3 Export Existing Variables

```bash
name="Jasin"

export name
```

This exports an already-created variable.

## 8.4 Temporary Environment Variables

You can pass a variable to one command:

```bash
APP_ENV=production bash deploy.sh
```

This makes `APP_ENV` available to that command and its child processes without permanently exporting it in the current shell.

### Real-World DevOps Example

Suppose an application behaves differently in development and production.

You can configure:

```bash
APP_ENV=production
```

Then execute:

```bash
export APP_ENV
./deploy.sh
```

Inside the script:

```bash
echo "Environment: $APP_ENV"
```

Output:

```text
Environment: production
```

**Security Insight:** Environment variables are convenient for configuration, but they are not a perfect secret-storage mechanism. Sensitive values can sometimes be exposed through process environments, diagnostics, or logs. Use an appropriate secrets manager for production credentials.

---

# 9. User Input – read

## 9.1 What Is the read Command?

The `read` command accepts input from the user and stores it in a variable.

It allows scripts to interact with users.

### Syntax

```bash
read variable_name
```

### Example

```bash
#!/bin/bash

echo "Enter your name:"
read name

echo "Welcome $name"
```

Execute:

```bash
bash input.sh
```

Example interaction:

```text
Enter your name:
Jasin
Welcome Jasin
```

## 9.2 Using read with a Prompt

Instead of using a separate `echo`, use `-p`.

```bash
#!/bin/bash

read -p "Enter your name: " name

echo "Welcome $name"
```

This displays the prompt and reads the input.

## 9.3 Reading Multiple Values

```bash
#!/bin/bash

read -p "Enter your first and last name: " first last

echo "First Name: $first"
echo "Last Name: $last"
```

If the user enters:

```text
Jasin Syed
```

Output:

```text
First Name: Jasin
Last Name: Syed
```

## 9.4 Reading Passwords Securely

Use `-s` to suppress terminal echo.

```bash
#!/bin/bash

read -s -p "Enter password: " password

echo
echo "Password received"
```

The typed password will not be displayed in the terminal.

**Important:** This hides terminal input, but does not automatically encrypt or securely store the password. Never print passwords into logs or output.

## 9.5 Reading Input with a Timeout

Use `-t`.

```bash
#!/bin/bash

if read -t 10 -p "Enter your name within 10 seconds: " name; then
    echo "Welcome $name"
else
    echo "Input timed out"
fi
```

This waits up to ten seconds for input.

**Senior DevOps Insight:** Interactive input is useful for manual tools. Automated pipelines and cron jobs should generally use arguments or configuration files instead, because they may not have an interactive terminal.

---

# 10. Displaying Output – echo and printf

## 10.1 The echo Command

`echo` displays text and variable values.

Example:

```bash
echo "Hello Linux"
```

Output:

```text
Hello Linux
```

Display a variable:

```bash
name="Jasin"

echo "Welcome $name"
```

## 10.2 The printf Command

`printf` provides more controlled formatting.

Example:

```bash
name="Jasin"
age=22

printf "Name: %s\nAge: %s\n" "$name" "$age"
```

Output:

```text
Name: Jasin
Age: 22
```

### Common Format Specifiers

| Specifier | Meaning |
|---|---|
| `%s` | String |
| `%d` | Integer |
| `%f` | Floating-point number |
| `\n` | New line |
| `\t` | Tab |

### Example: Formatted Server Report

```bash
#!/bin/bash

server="production-01"
status="Running"

printf "Server: %s\nStatus: %s\n" "$server" "$status"
```

Output:

```text
Server: production-01
Status: Running
```

**Senior DevOps Insight:** Prefer `printf` for predictable formatting in scripts, especially when generating reports or structured output.

---

# 11. Command-Line Arguments

## 11.1 What Are Command-Line Arguments?

Command-line arguments are values passed to a script when executing it.

They allow a script to receive input without asking the user interactively.

### Example

Suppose you have:

```bash
#!/bin/bash

echo "Hello $1"
```

Save it as:

```text
welcome.sh
```

Execute:

```bash
bash welcome.sh Jasin
```

Output:

```text
Hello Jasin
```

Here:

- `$1` represents the first argument.
- `Jasin` is the first argument supplied to the script.

## 11.2 Multiple Arguments

Script:

```bash
#!/bin/bash

echo "Name: $1"
echo "Role: $2"
echo "City: $3"
```

Execute:

```bash
bash welcome.sh Jasin DevOps Bangalore
```

Output:

```text
Name: Jasin
Role: DevOps
City: Bangalore
```

### Argument Mapping

| Variable | Value |
|---|---|
| `$1` | Jasin |
| `$2` | DevOps |
| `$3` | Bangalore |

## 11.3 Why Are Arguments Important?

Imagine creating a deployment script.

Without arguments, you might need to edit the script every time you deploy to a different environment.

Instead, you can accept the environment as an argument.

Example:

```bash
#!/bin/bash

echo "Deploying application to: $1"
```

Execute:

```bash
bash deploy.sh development
```

Or:

```bash
bash deploy.sh production
```

The same script works for multiple environments.

---

# 12. Special Variables in Bash

Bash provides special variables that contain information about script execution.

These are extremely important for automation.

| Variable | Meaning |
|---|---|
| `$0` | Script name or invocation path |
| `$1` | First positional argument |
| `$2` | Second positional argument |
| `$#` | Number of positional arguments |
| `$@` | All positional arguments as separate words when quoted correctly |
| `$*` | All positional arguments, with different quoted behavior |
| `$?` | Exit status of the previous command |
| `$$` | PID of the current shell |
| `$!` | PID of the most recently started background job |

## 12.1 Understanding `$0`

```bash
#!/bin/bash

echo "Script Name: $0"
```

Execute:

```bash
bash welcome.sh
```

Output may be:

```text
Script Name: welcome.sh
```

The exact value depends on how the script was invoked.

## 12.2 Understanding `$#`

Displays the number of positional arguments.

Script:

```bash
#!/bin/bash

echo "Total Arguments: $#"
```

Execute:

```bash
bash test.sh Jasin DevOps Bangalore
```

Output:

```text
Total Arguments: 3
```

## 12.3 Understanding `$@`

Represents all positional arguments.

Script:

```bash
#!/bin/bash

echo "Arguments: $@"
```

Execute:

```bash
bash test.sh Jasin DevOps Bangalore
```

Output:

```text
Arguments: Jasin DevOps Bangalore
```

### Important Difference: `"$@"` and `"$*"`

Both can appear similar when printed, but they behave differently when quoted.

- `"$@"` expands into separate arguments while preserving each argument boundary.
- `"$*"` expands into one combined string using the first character of `IFS` as the separator.

Example:

```bash
#!/bin/bash

for arg in "$@"; do
    printf 'Argument: %s\n' "$arg"
done
```

Execute:

```bash
bash test.sh "Jasin Syed" DevOps Bangalore
```

Output:

```text
Argument: Jasin Syed
Argument: DevOps
Argument: Bangalore
```

Notice that `"Jasin Syed"` remains one argument.

**Senior DevOps Insight:** Use `"$@"` when forwarding all arguments to another command or script. It preserves argument boundaries correctly.

## 12.4 Understanding `$$`

Displays the PID of the current shell.

```bash
#!/bin/bash

echo "Current Shell PID: $$"
```

Useful when investigating script execution and process relationships.

## 12.5 Understanding `$!`

Stores the PID of the most recently started background process.

Example:

```bash
#!/bin/bash

sleep 100 &

echo "Background Process PID: $!"
```

Useful when scripts start background jobs and need to track them.

---

# 13. Exit Codes and Script Status

## 13.1 What Is an Exit Code?

An exit code is a numeric value returned by a command or script when it finishes.

It indicates whether the operation succeeded or failed according to the program's own behavior.

Common convention:

- `0` means success.
- Non-zero generally means failure or another special status.

### Example

```bash
ls /tmp
```

Check its exit code:

```bash
echo $?
```

If the command succeeds, the output is normally:

```text
0
```

Now try:

```bash
ls /directory_that_does_not_exist
```

Then:

```bash
echo $?
```

You will generally receive a non-zero exit status.

## 13.2 Understanding exit codes

| Exit Code | General Meaning |
|---|---|
| `0` | Success |
| `1` | General error in many programs |
| `2` | Misuse of shell builtins in Bash; other programs may define it differently |
| Other non-zero values | Failure or program-specific status |

**Important:** Exit-code meanings are application-specific except for common conventions. Do not assume every program uses `1` and `2` identically.

## 13.3 The exit Command

Use `exit` to terminate a script and return a status code.

Example:

```bash
#!/bin/bash

echo "Deployment completed"

exit 0
```

This exits successfully.

Failure example:

```bash
#!/bin/bash

echo "Deployment failed"

exit 1
```

This terminates the script with a non-zero status.

## 13.4 Why Are Exit Codes Important in DevOps?

Imagine a CI/CD pipeline.

A deployment script executes:

1. Download application.
2. Install dependencies.
3. Build application.
4. Deploy application.

If the build fails, the pipeline must not continue as if everything succeeded.

Exit codes allow CI/CD tools to detect failure.

Example:

```bash
#!/bin/bash

mkdir -p /tmp/deployment

if [ $? -eq 0 ]; then
    echo "Deployment directory created"
else
    echo "Directory creation failed"
    exit 1
fi
```

This checks the previous command's exit code.

A more reliable way to write this particular example is:

```bash
#!/bin/bash

if mkdir -p /tmp/deployment; then
    echo "Deployment directory created"
else
    echo "Directory creation failed"
    exit 1
fi
```

The second version directly tests the command and avoids accidentally overwriting `$?` with another command.

**Senior DevOps Insight:** In production scripts, check the result of important commands explicitly. A script should not report success if a critical operation failed.

---

# 14. Practical DevOps Project – Server Information Script

Now combine the concepts you learned.

## Project: Basic Linux Server Information Collector

### Objective

Create a script that collects basic information about a Linux server.

### Step 1: Create the Script

```bash
nano server-info.sh
```

### Step 2: Add the Following Code

```bash
#!/bin/bash

# Basic Linux Server Information Script

echo "================================"
echo "       SERVER INFORMATION"
echo "================================"

echo

echo "Hostname:"
hostname

echo

echo "Current Date:"
date

echo

echo "Current User:"
whoami

echo

echo "System Uptime:"
uptime

echo

echo "Memory Information:"
free -h

echo

echo "Disk Usage:"
df -h

echo

echo "================================"
echo "       REPORT COMPLETED"
echo "================================"

exit 0
```

### Step 3: Make It Executable

```bash
chmod +x server-info.sh
```

### Step 4: Execute It

```bash
./server-info.sh
```

### Expected Output Structure

```text
================================
       SERVER INFORMATION
================================

Hostname:
linux-server

Current Date:
Fri Nov 21 20:00:00 IST 2026

Current User:
jasin

System Uptime:
20:00:00 up 5 days, 2 users, load average: ...

Memory Information:
...

Disk Usage:
...

================================
       REPORT COMPLETED
================================
```

The actual values will depend on your machine.

### What Did You Practise?

- Shebang.
- Comments.
- Commands inside scripts.
- Output using `echo`.
- Executing scripts.
- Exit codes.

### Production Extension

A production-ready version could write the report to a file:

```bash
./server-info.sh > server-report.txt
```

Or append the report:

```bash
./server-info.sh >> server-report.txt
```

You can later schedule this script using cron, which we will cover in Part 3.

---

# 15. Common Shell Scripting Mistakes

| Mistake | Problem | Correct Approach |
|---|---|---|
| `name = "Jasin"` | Spaces around assignment | `name="Jasin"` |
| Using `$name` without defining it | May produce an empty value | Define or validate the variable |
| Forgetting the shebang | Direct execution may use an unintended interpreter | Add `#!/bin/bash` |
| Forgetting execute permission | Direct execution may fail | `chmod +x script.sh` |
| Using `sh` for Bash-specific scripts | Bash syntax may fail | Execute using `bash script.sh` |
| Using `$@` unquoted | Arguments containing spaces can split | Use `"$@"` |
| Ignoring exit codes | Failures may go unnoticed | Check command success |
| Printing passwords | Secrets may leak into logs | Avoid exposing sensitive input |
| Using relative paths carelessly | Script may fail from another working directory | Use reliable paths |

---

# 🔐 16. Production Best Practices

1. **Use a clear shebang:** Explicitly define the interpreter.
2. **Quote variables:** Prefer `"$variable"` to avoid unexpected word splitting and glob expansion.
3. **Validate inputs:** Do not blindly trust command-line arguments.
4. **Use meaningful variable names:** Prefer `backup_directory` over unclear names such as `x`.
5. **Check exit codes:** Critical commands must be checked for success.
6. **Avoid hardcoded secrets:** Use approved secret-management solutions.
7. **Use absolute paths where appropriate:** Scheduled jobs may run with a different working directory and PATH.
8. **Document scripts:** Explain important operational decisions.
9. **Use least privilege:** Do not execute every script as root.
10. **Test before production:** Test scripts with non-production data and safe environments.

---

# 📌 17. Quick Command Reference

| Command / Variable | Purpose |
|---|---|
| `echo $SHELL` | Display configured login shell |
| `ps -p $$ -o comm=` | Identify current shell process |
| `cat /etc/shells` | List valid login shells |
| `touch script.sh` | Create an empty script file |
| `nano script.sh` | Create or edit a script |
| `bash script.sh` | Execute a script using Bash |
| `sh script.sh` | Execute using sh |
| `chmod +x script.sh` | Add execute permission |
| `./script.sh` | Execute script from current directory |
| `#` | Start a comment |
| `name="Jasin"` | Assign a variable |
| `echo "$name"` | Display a variable |
| `readonly NAME=value` | Declare a read-only variable |
| `unset name` | Remove a variable |
| `export name` | Export a variable to child processes |
| `read name` | Read user input |
| `read -p "Name: " name` | Read input with a prompt |
| `read -s password` | Read input without displaying it |
| `printf` | Display formatted output |
| `$0` | Script invocation name/path |
| `$1` | First argument |
| `$2` | Second argument |
| `$#` | Number of arguments |
| `"$@"` | All arguments, preserving boundaries |
| `$?` | Previous command's exit status |
| `$$` | Current shell PID |
| `$!` | Most recently started background job PID |
| `exit 0` | Exit successfully |
| `exit 1` | Exit with a failure status |

---

# 🎯 18. Interview Questions

## Basic Level

**1. What is shell scripting?**

Shell scripting is writing a sequence of shell commands in a file so that they can be executed together to automate tasks.

**2. What is Bash?**

Bash stands for Bourne Again Shell. It is a widely used command-line interpreter and scripting language on Linux.

**3. What is a shebang?**

It is the first line of a script that specifies the interpreter used for direct execution.

**4. What is the difference between a shell and a shell script?**

A shell is a command interpreter. A shell script is a file containing commands executed by that interpreter.

**5. How do you execute a shell script?**

```bash
bash script.sh
```

Or make it executable and run:

```bash
chmod +x script.sh
./script.sh
```

## Intermediate Level

**6. What is a variable in Bash?**

A variable stores a value that can be accessed and reused in a script.

**7. What is the difference between a shell variable and an environment variable?**

A shell variable belongs to the current shell unless exported. An exported environment variable is inherited by child processes.

**8. What is the purpose of the read command?**

It accepts input from standard input and stores it in variables.

**9. What is the difference between `$1` and `$#`?**

`$1` contains the first positional argument, whereas `$#` contains the number of positional arguments.

**10. What is the difference between `$@` and `$*`?**

When quoted, `"$@"` preserves each argument separately, while `"$*"` combines all arguments into one string.

**11. What is an exit code?**

It is a numeric status returned by a command or script. Conventionally, zero indicates success and non-zero indicates failure or another status.

## Senior DevOps Scenario-Based Questions

**12. You have a script that works when executed manually but fails in a CI/CD pipeline. What would you investigate?**

Check the interpreter, working directory, PATH, file permissions, environment variables, user permissions, and command exit codes. CI/CD environments may differ significantly from interactive terminals.

**13. Your deployment script continues even after a build command fails. What could be wrong?**

The script may not be checking the command's exit status. Critical operations should be tested explicitly, and failures should cause the script to return a non-zero status.

**14. How would you pass a production environment name into a deployment script?**

Use positional arguments:

```bash
./deploy.sh production
```

Then access it through `$1`.

**15. Why should shell variables generally be quoted?**

Quoting prevents unintended word splitting and filename expansion, especially when values contain spaces or wildcard characters.

**16. How would you pass all arguments from one script to another without losing spaces?**

Use:

```bash
./another-script.sh "$@"
```

This preserves the original argument boundaries.

**17. Why should production scripts avoid hardcoded passwords?**

Secrets in scripts can leak through source control, file access, logs, or process inspection. Use an approved secrets-management mechanism.

---

# 🧪 19. Hands-On Practice

Complete these tasks in your Linux environment.

### Fundamentals

- [ ] Check your current shell using `echo $SHELL`.
- [ ] List available shells using `cat /etc/shells`.
- [ ] Create your first script named `hello.sh`.
- [ ] Add the Bash shebang.
- [ ] Execute it using `bash hello.sh`.
- [ ] Make it executable using `chmod +x`.
- [ ] Execute it using `./hello.sh`.

### Variables and Input

- [ ] Create variables for your name, role, and city.
- [ ] Print the variables using `echo`.
- [ ] Use `${variable}` inside a message.
- [ ] Create a read-only variable.
- [ ] Remove a variable using `unset`.
- [ ] Export a variable and access it from a child script.
- [ ] Accept user input using `read`.
- [ ] Accept password input using `read -s`.
- [ ] Display formatted output using `printf`.

### Arguments and Exit Codes

- [ ] Create a script that accepts one argument.
- [ ] Create a script that accepts three arguments.
- [ ] Display the script name using `$0`.
- [ ] Display the argument count using `$#`.
- [ ] Print all arguments using `"$@"`.
- [ ] Display the previous command's exit code using `$?`.
- [ ] Return success using `exit 0`.
- [ ] Return failure using `exit 1`.

### Mini Project

- [ ] Build the Linux server information script.
- [ ] Collect hostname, date, uptime, memory, and disk information.
- [ ] Save its output to a report file.
- [ ] Run the script and verify the report.

---

# 🏁 20. Part 1 Completion Checklist

- [ ] Understand what shell scripting is.
- [ ] Understand the difference between a shell and a script.
- [ ] Understand the Bash interpreter.
- [ ] Understand the shebang.
- [ ] Create and execute shell scripts.
- [ ] Understand comments.
- [ ] Create and use variables.
- [ ] Understand environment variables.
- [ ] Accept user input with `read`.
- [ ] Display output with `echo` and `printf`.
- [ ] Understand positional arguments.
- [ ] Understand Bash special variables.
- [ ] Understand exit codes.
- [ ] Complete the server information script.
- [ ] Answer the Part 1 interview questions.

---

## 🏁 Part 1 Key Takeaway

You have learned the foundation of Bash scripting.

You can now write scripts, store information in variables, accept input, receive command-line arguments, and understand whether commands succeed or fail.

These fundamentals are the building blocks for more advanced automation.

**Next: Part 2 – Conditions, Loops, Functions, Arrays, File Operations & Error Handling.**

We will move from simple scripts to writing actual automation logic used in DevOps environments.






# 🐧 Linux Shell Scripting – Part 2: Conditions, Loops, Functions, Arrays & Error Handling

> **Roadmap:** Topic 12 of 12  
> **Topic:** Shell Scripting  
> **Part:** 2 of 3  
> **Domain:** Linux Administration / DevOps / Cloud Engineering  
> **Difficulty:** Intermediate to Advanced  
> **Objective:** Learn decision-making, loops, functions, arrays, file operations, string manipulation, error handling, and debugging to build reliable Bash automation scripts.

---

## 📚 Table of Contents

1. Introduction to Part 2
2. Conditional Statements – `if`
3. `if-else` Statements
4. `if-elif-else` Statements
5. Test Operators
6. String Comparison
7. Numeric Comparison
8. File and Directory Conditions
9. Logical Operators
10. `case` Statements
11. Loops in Bash
12. `for` Loops
13. `while` Loops
14. `until` Loops
15. `break` and `continue`
16. Functions in Bash
17. Function Arguments and Return Values
18. Arrays in Bash
19. String Manipulation
20. File Operations
21. Error Handling
22. Debugging Shell Scripts
23. Practical DevOps Project – Service Health Checker
24. Production Best Practices
25. Quick Command Reference
26. Interview Questions
27. Hands-On Practice
28. Part 2 Completion Checklist

---

# 🎯 1. Introduction to Part 2

In Part 1, you learned how to:

- Create and execute Bash scripts.
- Use variables and environment variables.
- Accept user input.
- Pass command-line arguments.
- Understand exit codes.

Now we will learn how to make scripts intelligent.

Consider a production Linux server.

You want a script that:

1. Checks whether disk usage exceeds 80%.
2. If usage exceeds 80%, prints a warning.
3. Otherwise, reports that disk usage is normal.
4. Repeats the check every five minutes.
5. Sends an alert if the condition continues.

This requires:

- Conditions to make decisions.
- Loops to repeat operations.
- Functions to organize code.
- Error handling to detect failures.

These are the foundations of real-world DevOps automation.

---

# 2. Conditional Statements – `if`

## 2.1 What Is an if Statement?

An `if` statement allows a script to make decisions based on a condition.

It executes a block of commands only when a condition is true.

### Syntax

```bash
if [ condition ]; then
    commands
fi
```

### Understanding the Syntax

| Component | Meaning |
|---|---|
| `if` | Starts the condition |
| `[ condition ]` | Tests whether the condition is true |
| `then` | Starts the commands to execute |
| `fi` | Ends the if statement |

Notice that `fi` is simply `if` written backward.

## 2.2 Basic Example

```bash
#!/bin/bash

age=22

if [ "$age" -ge 18 ]; then
    echo "You are eligible"
fi
```

Output:

```text
You are eligible
```

Explanation:

- `age=22` stores the value.
- `-ge` means greater than or equal to.
- The condition checks whether age is at least 18.
- Since the condition is true, the message is printed.

If the condition is false, the commands inside the `if` block are skipped.

### Important

The spaces inside `[ ]` are mandatory.

Correct:

```bash
if [ "$age" -ge 18 ]; then
```

Incorrect:

```bash
if ["$age" -ge 18]; then
```

---

# 3. if-else Statements

## 3.1 What Is if-else?

An `if-else` statement provides two possible execution paths.

- If the condition is true, execute the `if` block.
- If the condition is false, execute the `else` block.

### Syntax

```bash
if [ condition ]; then
    commands_if_true
else
    commands_if_false
fi
```

## 3.2 Example

```bash
#!/bin/bash

disk_usage=90

if [ "$disk_usage" -gt 80 ]; then
    echo "WARNING: Disk usage is high"
else
    echo "Disk usage is normal"
fi
```

Output:

```text
WARNING: Disk usage is high
```

### Execution Flow

```text
        Start
          |
          v
   Check disk usage
          |
          v
    Is usage > 80?
       /       \
     Yes        No
      |          |
      v          v
   Warning    Normal
      |          |
      +----+-----+
           |
           v
          End
```

## 3.3 Real-World DevOps Example

Imagine an engineer wants to check whether a directory exists before creating an application deployment directory.

```bash
#!/bin/bash

directory="/opt/myapp"

if [ -d "$directory" ]; then
    echo "Application directory already exists"
else
    mkdir -p "$directory"
    echo "Application directory created"
fi
```

This prevents the script from unnecessarily trying to create a directory that already exists.

`mkdir -p` is also safe when the directory already exists, so the condition is useful mainly when the script needs to perform different actions depending on its existence.

---

# 4. if-elif-else Statements

## 4.1 What Is if-elif-else?

When you need to evaluate multiple conditions, use `elif`.

`elif` means "else if".

### Syntax

```bash
if [ condition1 ]; then
    commands1
elif [ condition2 ]; then
    commands2
elif [ condition3 ]; then
    commands3
else
    default_commands
fi
```

## 4.2 Example: Disk Usage Monitoring

```bash
#!/bin/bash

disk_usage=75

if [ "$disk_usage" -ge 90 ]; then
    echo "CRITICAL: Disk usage is above 90%"
elif [ "$disk_usage" -ge 80 ]; then
    echo "WARNING: Disk usage is above 80%"
elif [ "$disk_usage" -ge 60 ]; then
    echo "NOTICE: Disk usage is above 60%"
else
    echo "Disk usage is healthy"
fi
```

Output:

```text
NOTICE: Disk usage is above 60%
```

### Why Does Order Matter?

Conditions are checked from top to bottom.

Once a true condition is found, the remaining conditions are skipped.

For example, if disk usage is 95%, the first condition is true.

The script does not continue checking the remaining conditions.

**Senior DevOps Insight:** Always arrange threshold conditions from highest severity to lowest severity. Otherwise, a critical condition may be caught by a less severe condition first.

---

# 5. Test Operators

Test operators are used to compare values and check conditions.

Bash provides several categories of operators.

## 5.1 Numeric Comparison Operators

| Operator | Meaning |
|---|---|
| `-eq` | Equal to |
| `-ne` | Not equal to |
| `-gt` | Greater than |
| `-lt` | Less than |
| `-ge` | Greater than or equal to |
| `-le` | Less than or equal to |

### Example

```bash
#!/bin/bash

a=20
b=10

if [ "$a" -gt "$b" ]; then
    echo "A is greater than B"
fi
```

Output:

```text
A is greater than B
```

### More Examples

```bash
[ 10 -eq 10 ]
```

True because both values are equal.

```bash
[ 10 -ne 5 ]
```

True because the values are different.

```bash
[ 5 -lt 10 ]
```

True because 5 is less than 10.

---

# 6. String Comparison

String operators compare text values.

| Operator | Meaning |
|---|---|
| `=` | Strings are equal |
| `!=` | Strings are different |
| `-z` | String length is zero |
| `-n` | String length is non-zero |

## 6.1 Compare Two Strings

```bash
#!/bin/bash

username="admin"

if [ "$username" = "admin" ]; then
    echo "Welcome administrator"
else
    echo "Access denied"
fi
```

Output:

```text
Welcome administrator
```

## 6.2 Check Whether a String Is Empty

```bash
#!/bin/bash

name=""

if [ -z "$name" ]; then
    echo "Name is empty"
else
    echo "Name is provided"
fi
```

Output:

```text
Name is empty
```

## 6.3 Check Whether a String Is Not Empty

```bash
#!/bin/bash

name="Jasin"

if [ -n "$name" ]; then
    echo "Name is available"
fi
```

Output:

```text
Name is available
```

### Real-World DevOps Example: Validate Environment

```bash
#!/bin/bash

environment="$1"

if [ -z "$environment" ]; then
    echo "ERROR: Environment argument is required"
    exit 1
fi

echo "Deploying to $environment"
```

Execute:

```bash
./deploy.sh production
```

If no argument is provided, the script stops with an error.

---

# 7. File and Directory Conditions

One of the most important uses of conditions in Linux administration is checking files and directories.

Bash provides test operators for file-related checks.

## 7.1 Common File Test Operators

| Operator | Meaning |
|---|---|
| `-e` | Path exists |
| `-f` | Regular file exists |
| `-d` | Directory exists |
| `-r` | File is readable |
| `-w` | File is writable |
| `-x` | File is executable |
| `-s` | File exists and has a size greater than zero |
| `-L` | Path is a symbolic link |

## 7.2 Check Whether a File Exists

```bash
#!/bin/bash

file="/etc/passwd"

if [ -f "$file" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

Output:

```text
File exists
```

## 7.3 Check Whether a Directory Exists

```bash
#!/bin/bash

directory="/var/log"

if [ -d "$directory" ]; then
    echo "Directory exists"
else
    echo "Directory not found"
fi
```

## 7.4 Check File Permissions

```bash
#!/bin/bash

file="backup.sh"

if [ -r "$file" ]; then
    echo "File is readable"
fi

if [ -w "$file" ]; then
    echo "File is writable"
fi

if [ -x "$file" ]; then
    echo "File is executable"
fi
```

## 7.5 Check Whether a File Is Empty

```bash
#!/bin/bash

file="application.log"

if [ -s "$file" ]; then
    echo "Log file contains data"
else
    echo "Log file is empty or missing"
fi
```

**Senior DevOps Insight:** Use `-f` when you specifically need a regular file. Use `-e` when any existing filesystem path is acceptable.

---

# 8. Logical Operators

Logical operators allow you to combine multiple conditions.

## 8.1 AND Operator – `&&`

AND means both conditions must be true.

Example:

```bash
#!/bin/bash

age=22
country="India"

if [ "$age" -ge 18 ] && [ "$country" = "India" ]; then
    echo "Both conditions are satisfied"
fi
```

Both conditions must be true for the message to execute.

## 8.2 OR Operator – `||`

OR means at least one condition must be true.

Example:

```bash
#!/bin/bash

status="failed"

if [ "$status" = "failed" ] || [ "$status" = "error" ]; then
    echo "Application requires investigation"
fi
```

## 8.3 NOT Operator – `!`

NOT reverses a condition.

Example:

```bash
#!/bin/bash

file="backup.tar"

if [ ! -f "$file" ]; then
    echo "Backup file does not exist"
fi
```

### Real-World DevOps Example

Check whether a service is unavailable or its process is missing:

```bash
#!/bin/bash

if ! systemctl is-active --quiet nginx; then
    echo "WARNING: Nginx is not active"
fi
```

This uses `!` to reverse the success or failure result of `systemctl is-active`.

**Important:** In Bash, `&&` and `||` are also command-control operators. They execute the next command based on the exit status of the previous command. We will use them in practical scripts.

---

# 9. case Statements

## 9.1 What Is a case Statement?

A `case` statement is useful when a variable can have several possible values.

It is often cleaner than writing many `if-elif` conditions.

### Syntax

```bash
case "$variable" in
    value1)
        commands
        ;;
    value2)
        commands
        ;;
    *)
        default_commands
        ;;
esac
```

### Understanding the Syntax

| Component | Meaning |
|---|---|
| `case` | Starts the selection statement |
| `in` | Starts the matching patterns |
| `value1)` | Matches a particular value or pattern |
| `;;` | Ends that case branch |
| `*)` | Default branch |
| `esac` | Ends the case statement |

## 9.2 Example: Service Management Menu

```bash
#!/bin/bash

read -p "Enter service action (start/stop/restart/status): " action

case "$action" in
    start)
        echo "Starting service"
        ;;
    stop)
        echo "Stopping service"
        ;;
    restart)
        echo "Restarting service"
        ;;
    status)
        echo "Checking service status"
        ;;
    *)
        echo "Invalid action"
        ;;
esac
```

Example:

```text
Enter service action (start/stop/restart/status): restart
Restarting service
```

## 9.3 Production Example: Manage Nginx

```bash
#!/bin/bash

action="$1"

case "$action" in
    start)
        sudo systemctl start nginx
        ;;
    stop)
        sudo systemctl stop nginx
        ;;
    restart)
        sudo systemctl restart nginx
        ;;
    status)
        systemctl status nginx
        ;;
    *)
        echo "Usage: $0 {start|stop|restart|status}"
        exit 1
        ;;
esac
```

Execute:

```bash
./nginx-manager.sh status
```

This is a basic service-management automation tool.

In a production implementation, also validate the action, check command success, and handle privileges explicitly.

---

# 10. Loops in Bash

## 10.1 What Is a Loop?

A loop repeats a block of commands.

Without loops, you would have to write the same command many times.

Imagine checking five servers.

Without a loop:

```bash
ping -c 1 server1
ping -c 1 server2
ping -c 1 server3
ping -c 1 server4
ping -c 1 server5
```

With a loop, you can process a list of servers automatically.

Bash commonly provides:

- `for`
- `while`
- `until`

---

# 11. for Loops

## 11.1 What Is a for Loop?

A `for` loop executes commands for each item in a list.

### Syntax

```bash
for variable in list
do
    commands
done
```

## 11.2 Basic Example

```bash
#!/bin/bash

for name in Jasin Ahmed Ali
do
    echo "Welcome $name"
done
```

Output:

```text
Welcome Jasin
Welcome Ahmed
Welcome Ali
```

The loop executes three times.

Each time, the variable `name` receives the next value.

## 11.3 Numeric for Loop

```bash
#!/bin/bash

for i in {1..5}
do
    echo "Number: $i"
done
```

Output:

```text
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
```

## 11.4 Using C-Style for Loops

Bash supports C-style syntax.

```bash
#!/bin/bash

for ((i=1; i<=5; i++))
do
    echo "Iteration: $i"
done
```

Explanation:

| Expression | Meaning |
|---|---|
| `i=1` | Initial value |
| `i<=5` | Continue while condition is true |
| `i++` | Increase value by one |

## 11.5 Real-World DevOps Example: Check Multiple Servers

```bash
#!/bin/bash

servers=("server1" "server2" "server3")

for server in "${servers[@]}"
do
    echo "Checking $server"
    ping -c 1 -W 2 "$server"
done
```

This checks each server in the list.

The names here are examples. Replace them with real hostnames or IP addresses in an actual environment.

**Senior DevOps Insight:** In production, avoid relying only on `ping` to decide whether an application is healthy. A host may block ICMP while its application is functioning. Test the relevant service or endpoint as well.

---

# 12. while Loops

## 12.1 What Is a while Loop?

A `while` loop repeatedly executes commands as long as its condition remains true.

### Syntax

```bash
while [ condition ]
do
    commands
done
```

## 12.2 Basic Example

```bash
#!/bin/bash

count=1

while [ "$count" -le 5 ]
do
    echo "Count: $count"

    count=$((count + 1))
done
```

Output:

```text
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

### Understanding Arithmetic Expansion

```bash
count=$((count + 1))
```

This calculates the new value and assigns it to `count`.

## 12.3 Real-World DevOps Example: Continuous Service Monitoring

```bash
#!/bin/bash

while true
do
    if systemctl is-active --quiet nginx; then
        echo "Nginx is running"
    else
        echo "WARNING: Nginx is not running"
    fi

    sleep 10
done
```

This checks Nginx every ten seconds.

Stop it using:

```text
Ctrl + C
```

**Production Warning:** This is a basic demonstration. It runs continuously and prints output indefinitely. For production monitoring, use proper monitoring systems and alerting. Avoid creating uncontrolled monitoring loops on servers.

---

# 13. until Loops

## 13.1 What Is an until Loop?

An `until` loop works in the opposite way to a `while` loop.

- `while`: Runs while the condition is true.
- `until`: Runs until the condition becomes true.

### Syntax

```bash
until [ condition ]
do
    commands
done
```

## 13.2 Example

```bash
#!/bin/bash

count=1

until [ "$count" -gt 5 ]
do
    echo "Count: $count"

    count=$((count + 1))
done
```

Output:

```text
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

The loop continues until `count` becomes greater than 5.

## 13.3 Real-World DevOps Example: Wait for a Service

```bash
#!/bin/bash

attempt=1
max_attempts=5

until systemctl is-active --quiet nginx
do
    if [ "$attempt" -ge "$max_attempts" ]; then
        echo "ERROR: Nginx did not become active"
        exit 1
    fi

    echo "Waiting for Nginx..."

    sleep 2
    attempt=$((attempt + 1))
done

echo "Nginx is active"
```

This script waits for Nginx to become active, with a maximum number of attempts.

It avoids waiting forever.

---

# 14. break and continue

These statements control loop execution.

## 14.1 break

`break` immediately exits the current loop.

Example:

```bash
#!/bin/bash

for i in {1..10}
do
    if [ "$i" -eq 5 ]; then
        break
    fi

    echo "$i"
done
```

Output:

```text
1
2
3
4
```

When `i` reaches 5, the loop stops.

## 14.2 continue

`continue` skips the rest of the current iteration and moves to the next iteration.

Example:

```bash
#!/bin/bash

for i in {1..5}
do
    if [ "$i" -eq 3 ]; then
        continue
    fi

    echo "$i"
done
```

Output:

```text
1
2
4
5
```

The number 3 is skipped.

### Real-World DevOps Example

Imagine processing a list of log files but skipping files that do not exist.

```bash
#!/bin/bash

for file in /var/log/*.log
do
    [ -f "$file" ] || continue

    echo "Processing: $file"
done
```

Here, `continue` skips any path that is not a regular file.

---

# 15. Functions in Bash

## 15.1 What Is a Function?

A function is a reusable block of code that performs a specific task.

Functions help organize scripts and reduce repeated code.

Imagine you need to print server information in several places.

Instead of writing the same commands repeatedly, create one function.

### Syntax

```bash
function_name() {
    commands
}
```

You can also use:

```bash
function function_name {
    commands
}
```

The first style is commonly used.

## 15.2 Basic Function Example

```bash
#!/bin/bash

greet() {
    echo "Welcome to DevOps"
}

greet
```

Output:

```text
Welcome to DevOps
```

The function is defined first and executed by calling its name.

## 15.3 Function with Arguments

Functions can accept positional arguments.

Example:

```bash
#!/bin/bash

greet() {
    echo "Hello $1"
}

greet "Jasin"
```

Output:

```text
Hello Jasin
```

Inside the function:

- `$1` is the first function argument.
- `$2` is the second function argument.
- `$#` is the number of function arguments.

These positional parameters are local to the function call.

## 15.4 Function with Multiple Arguments

```bash
#!/bin/bash

server_info() {
    echo "Server: $1"
    echo "Environment: $2"
}

server_info "web-server-01" "production"
```

Output:

```text
Server: web-server-01
Environment: production
```

## 15.5 Return Values in Functions

In Bash, `return` is primarily used to return an exit status, normally from 0 to 255.

Example:

```bash
#!/bin/bash

check_status() {
    return 0
}

check_status

if [ "$?" -eq 0 ]; then
    echo "Function succeeded"
else
    echo "Function failed"
fi
```

Output:

```text
Function succeeded
```

## 15.6 Returning Data from a Function

To return text or other data, use command substitution.

Example:

```bash
#!/bin/bash

get_hostname() {
    hostname
}

server=$(get_hostname)

echo "Server: $server"
```

The output of `hostname` is captured in the `server` variable.

### Important Difference

| Method | Purpose |
|---|---|
| `return 0` | Return success status |
| `return 1` | Return failure status |
| `echo "value"` | Produce text output |
| `variable=$(function_name)` | Capture function output |

**Senior DevOps Insight:** Do not use `return` to return arbitrary strings. Use command substitution for data and return status codes for success or failure.

## 15.7 Local Variables

Variables created inside Bash functions are global by default unless declared local.

Use `local` to limit a variable to the function.

Example:

```bash
#!/bin/bash

show_server() {
    local server="production-01"

    echo "$server"
}

show_server

echo "$server"
```

The variable `server` is available inside the function but is not defined in the outer shell scope.

This helps avoid accidentally overwriting variables used elsewhere.

---

# 16. Arrays in Bash

## 16.1 What Is an Array?

An array stores multiple values under one variable name.

Instead of creating separate variables:

```bash
server1="web01"
server2="web02"
server3="web03"
```

You can create one array:

```bash
servers=("web01" "web02" "web03")
```

Arrays are useful when processing multiple servers, files, services, or environments.

## 16.2 Create an Array

```bash
#!/bin/bash

servers=("web01" "web02" "web03")

echo "${servers[0]}"
echo "${servers[1]}"
echo "${servers[2]}"
```

Output:

```text
web01
web02
web03
```

Bash array indexes start at zero.

## 16.3 Access All Array Elements

```bash
#!/bin/bash

servers=("web01" "web02" "web03")

echo "${servers[@]}"
```

Output:

```text
web01 web02 web03
```

## 16.4 Count Array Elements

```bash
#!/bin/bash

servers=("web01" "web02" "web03")

echo "Total servers: ${#servers[@]}"
```

Output:

```text
Total servers: 3
```

## 16.5 Loop Through an Array

```bash
#!/bin/bash

servers=("web01" "web02" "web03")

for server in "${servers[@]}"
do
    echo "Checking $server"
done
```

Output:

```text
Checking web01
Checking web02
Checking web03
```

## 16.6 Add Elements to an Array

```bash
#!/bin/bash

servers=("web01" "web02")

servers+=("web03")

echo "${servers[@]}"
```

Output:

```text
web01 web02 web03
```

## 16.7 Remove an Array Element

Use `unset` with the array index.

```bash
#!/bin/bash

servers=("web01" "web02" "web03")

unset 'servers[1]'

echo "${servers[@]}"
```

Output:

```text
web01 web03
```

The array now has a gap at index 1. Removing an element does not automatically re-index the remaining elements.

---

# 17. String Manipulation

Bash supports several ways to manipulate strings.

## 17.1 Find String Length

Use `${#variable}`.

```bash
#!/bin/bash

name="Jasin"

echo "${#name}"
```

Output:

```text
5
```

## 17.2 Extract Part of a String

Use substring expansion.

```bash
#!/bin/bash

text="DevOpsEngineer"

echo "${text:0:6}"
```

Output:

```text
DevOps
```

Explanation:

- `0` is the starting position.
- `6` is the number of characters.

## 17.3 Replace Text in a String

```bash
#!/bin/bash

text="Hello Linux"

echo "${text/Linux/Bash}"
```

Output:

```text
Hello Bash
```

This replaces the first matching occurrence.

To replace all matching occurrences:

```bash
text="Linux Linux Linux"

echo "${text//Linux/Bash}"
```

Output:

```text
Bash Bash Bash
```

## 17.4 Convert Case

```bash
#!/bin/bash

text="devops"

echo "${text^^}"
```

Output:

```text
DEVOPS
```

Convert to lowercase:

```bash
text="DEVOPS"

echo "${text,,}"
```

Output:

```text
devops
```

These case-conversion expansions are Bash features.

---

# 18. File Operations

Shell scripts frequently create, read, modify, and process files.

## 18.1 Create a File

```bash
touch application.log
```

## 18.2 Write to a File

```bash
echo "Application started" > application.log
```

This overwrites the file.

## 18.3 Append to a File

```bash
echo "Application running" >> application.log
```

This adds text to the end of the file.

## 18.4 Read a File

```bash
cat application.log
```

Or:

```bash
while IFS= read -r line
do
    echo "$line"
done < application.log
```

This reads the file line by line.

Explanation:

| Component | Meaning |
|---|---|
| `IFS=` | Prevents leading/trailing whitespace from being trimmed by the default field separator |
| `read -r` | Prevents backslashes from being interpreted as escape characters |
| `line` | Stores each line |
| `< application.log` | Supplies the file as standard input |

## 18.5 Copy a File

```bash
cp application.log backup.log
```

## 18.6 Move or Rename a File

```bash
mv application.log old-application.log
```

## 18.7 Delete a File

```bash
rm old-application.log
```

**Production Warning:** Be extremely careful when using `rm` in scripts. Validate paths and variables before deleting files. Avoid dangerous patterns involving empty variables or unvalidated user input.

---

# 19. Error Handling

Error handling is one of the most important concepts in production scripting.

A script should detect failures instead of continuing as if every operation succeeded.

## 19.1 Using `set -e`

`set -e` tells Bash to exit when certain commands fail.

Example:

```bash
#!/bin/bash

set -e

echo "Starting deployment"

mkdir -p /tmp/myapp

echo "Directory created"

echo "Deployment completed"
```

If an eligible command fails, the script exits instead of continuing.

### Important Limitation

`set -e` has exceptions and context-dependent behavior, especially around conditions, pipelines, functions, and compound commands.

It is not a replacement for explicit error handling.

## 19.2 Using `set -u`

`set -u` treats the expansion of an unset variable as an error.

Example:

```bash
#!/bin/bash

set -u

echo "$application_name"
```

If `application_name` has not been defined, Bash reports an error.

This helps detect misspelled or missing variables.

## 19.3 Using `set -o pipefail`

Normally, a pipeline's exit status is the status of its last command.

Example:

```bash
command1 | command2
```

If `command1` fails but `command2` succeeds, the pipeline may appear successful.

Use:

```bash
set -o pipefail
```

Now the pipeline reports failure if a command in the pipeline fails, with the status of the rightmost failing command.

## 19.4 Common Strict Mode

You will often see:

```bash
#!/usr/bin/env bash

set -euo pipefail
```

Meaning:

- `-e`: Exit on certain command failures.
- `-u`: Treat unset variables as errors.
- `-o pipefail`: Detect failures in pipelines.

This is a useful starting point for many scripts, but it still requires deliberate error handling and testing.

## 19.5 Using `||` for Error Handling

Example:

```bash
#!/bin/bash

mkdir -p /opt/myapp || {
    echo "ERROR: Could not create application directory"
    exit 1
}
```

If the `mkdir` command fails, the commands after `||` execute.

## 19.6 Using if to Handle Errors

```bash
#!/bin/bash

if systemctl restart nginx; then
    echo "Nginx restarted successfully"
else
    echo "ERROR: Nginx restart failed"
    exit 1
fi
```

This is a clear way to handle an important operation.

## 19.7 Create an Error Function

Functions help standardize error messages.

```bash
#!/bin/bash

error_exit() {
    echo "ERROR: $1" >&2
    exit 1
}

mkdir -p /tmp/myapp || error_exit "Failed to create directory"

echo "Directory created successfully"
```

Here:

- `>&2` sends the message to standard error.
- `exit 1` terminates the script with a failure status.

**Senior DevOps Insight:** Use standard error for diagnostic messages and standard output for normal results. This makes logging and automation easier.

---

# 20. Debugging Shell Scripts

Debugging means identifying and correcting problems in scripts.

Bash provides useful debugging options.

## 20.1 Use `bash -x`

The `-x` option prints commands as Bash executes them.

Example:

```bash
bash -x script.sh
```

This is called execution tracing.

It helps you understand which command is being executed and what values are expanded.

## 20.2 Enable Debugging Inside a Script

```bash
#!/bin/bash

set -x

name="Jasin"

echo "Welcome $name"

set +x
```

- `set -x` enables tracing.
- `set +x` disables tracing.

## 20.3 Check Script Syntax

Use:

```bash
bash -n script.sh
```

This checks syntax without executing the script.

It is useful before deploying automation.

## 20.4 Check Which Bash Is Being Used

```bash
which bash
```

Or:

```bash
command -v bash
```

This helps identify the Bash executable available in the environment.

### Production Best Practice

Before deploying a script:

1. Check its syntax.
2. Execute it in a test environment.
3. Run it with tracing when troubleshooting.
4. Validate its inputs.
5. Check important command results.
6. Confirm that it returns the expected exit status.

---

# 🚀 21. Practical DevOps Project – Service Health Checker

Now combine conditions, loops, functions, and error handling.

## Project Objective

Create a script that checks whether a Linux service is running.

If the service is not running, the script should report the problem.

We will use Nginx as an example.

## Step 1: Create the Script

```bash
nano service-check.sh
```

## Step 2: Add the Code

```bash
#!/usr/bin/env bash

set -u

SERVICE="${1:-nginx}"

check_service() {

    echo "Checking service: $SERVICE"

    if systemctl is-active --quiet "$SERVICE"; then
        echo "STATUS: $SERVICE is running"
        return 0
    else
        echo "WARNING: $SERVICE is not running" >&2
        return 1
    fi
}

check_service

status=$?

if [ "$status" -ne 0 ]; then
    echo "Service health check failed"
    exit 1
fi

echo "Service health check completed successfully"

exit 0
```

## Step 3: Make It Executable

```bash
chmod +x service-check.sh
```

## Step 4: Execute It

```bash
./service-check.sh nginx
```

If Nginx is running, you should see:

```text
Checking service: nginx
STATUS: nginx is running
Service health check completed successfully
```

If Nginx is not running, the script reports a warning and returns a non-zero exit code.

You can also check another service:

```bash
./service-check.sh ssh
```

On some distributions, the SSH service is named `sshd` instead of `ssh`.

## Step 5: Check the Exit Code

Immediately after running the script:

```bash
echo $?
```

- `0` means the health check succeeded.
- `1` means the service health check failed.

## What Did You Practise?

- Command-line arguments.
- Default parameter values.
- Functions.
- Conditions.
- Return statuses.
- Error messages.
- Exit codes.

**Production Extension:** A production health checker should also validate that the service responds to its actual application endpoint. A systemd service being active does not always mean the application is healthy.

---

# 🔐 22. Production Best Practices

1. **Use meaningful functions:** Break large scripts into smaller reusable operations.
2. **Validate arguments:** Check required parameters before performing actions.
3. **Quote variables:** Use `"$variable"` to prevent word splitting and wildcard expansion.
4. **Use arrays safely:** Prefer `"${array[@]}"` when processing individual elements.
5. **Avoid infinite loops:** Use timeouts, retry limits, and delays.
6. **Handle errors:** Check the result of critical commands.
7. **Use strict mode carefully:** Understand the behavior of `set -euo pipefail`.
8. **Use local variables:** Prevent accidental changes to global variables.
9. **Avoid unsafe deletion:** Validate paths before using `rm`.
10. **Use debugging tools:** Run `bash -n` and `bash -x` when necessary.
11. **Separate output streams:** Send errors to standard error.
12. **Avoid unnecessary root privileges:** Run scripts with the minimum required permissions.
13. **Use shellcheck:** Static analysis helps identify common Bash mistakes.
14. **Test failure scenarios:** Test what happens when a file, service, command, or network connection is unavailable.

---

# 📌 23. Quick Command Reference

| Command / Syntax | Purpose |
|---|---|
| `if [ condition ]; then` | Start a conditional statement |
| `else` | Define the alternative branch |
| `elif` | Check another condition |
| `fi` | End an if statement |
| `-eq` | Numeric equality |
| `-ne` | Numeric inequality |
| `-gt` | Greater than |
| `-lt` | Less than |
| `-ge` | Greater than or equal |
| `-le` | Less than or equal |
| `=` | String equality |
| `!=` | String inequality |
| `-z` | Check empty string |
| `-n` | Check non-empty string |
| `-f` | Check regular file |
| `-d` | Check directory |
| `-e` | Check path existence |
| `-r` | Check read permission |
| `-w` | Check write permission |
| `-x` | Check execute permission |
| `&&` | Logical AND / conditional command execution |
| `\|\|` | Logical OR / fallback command execution |
| `!` | Negate a condition |
| `case` | Match a value against patterns |
| `for` | Iterate over a list |
| `while` | Repeat while a condition is true |
| `until` | Repeat until a condition becomes true |
| `break` | Exit a loop |
| `continue` | Skip the current iteration |
| `function_name()` | Define a function |
| `return` | Return a function status |
| `local` | Declare a function-local variable |
| `array=(...)` | Create an indexed array |
| `${array[@]}` | Access all array elements |
| `${#array[@]}` | Count array elements |
| `${#variable}` | Get string length |
| `${variable^^}` | Convert string to uppercase |
| `${variable,,}` | Convert string to lowercase |
| `set -e` | Exit on certain command failures |
| `set -u` | Treat unset variables as errors |
| `set -o pipefail` | Detect failures within pipelines |
| `bash -x script.sh` | Execute with tracing |
| `bash -n script.sh` | Check script syntax |

---

# 🎯 24. Interview Questions

## Basic Level

**1. What is a conditional statement in Bash?**

A conditional statement allows a script to execute different commands depending on whether a condition is true or false.

**2. What is the difference between `if` and `case`?**

`if` is useful for evaluating conditions and expressions. `case` is useful for matching a value against multiple patterns.

**3. What is the difference between `while` and `until`?**

`while` executes while a condition is true. `until` executes until a condition becomes true.

**4. What is the purpose of `break`?**

It immediately terminates the current loop.

**5. What is the purpose of `continue`?**

It skips the remaining commands in the current iteration and moves to the next iteration.

## Intermediate Level

**6. What is a function in Bash?**

A function is a reusable block of commands that performs a specific task.

**7. What is the difference between `return` and `echo` inside a function?**

`return` supplies an exit status. `echo` produces output that can be captured using command substitution.

**8. What is the purpose of `local`?**

It declares a variable whose scope is limited to the function.

**9. What is an array?**

An array stores multiple values under one variable name.

**10. What is the difference between `${array[@]}` and `${#array[@]}`?**

The first expands the array elements; the second returns the number of elements.

**11. What is the purpose of `set -euo pipefail`?**

It enables several useful failure-detection behaviors: exit on certain command failures, detect unset variables, and detect pipeline failures.

**12. How do you debug a Bash script?**

Use `bash -x script.sh` to trace execution and `bash -n script.sh` to check syntax.

## Senior DevOps Scenario-Based Questions

**13. Your deployment script must check whether a directory exists before copying application files. How would you implement it?**

Use an `if` statement with the `-d` test operator, then create the directory if necessary.

**14. You need to monitor ten servers using one script. Which Bash feature would you use?**

Use an array to store server names and a `for` loop to iterate over them.

**15. Your script keeps running even when a command fails. How would you improve it?**

Check important command statuses explicitly, use appropriate error handling, and consider `set -euo pipefail` while understanding its limitations.

**16. Your script must wait for a service to become active but should not wait forever. What would you use?**

Use a loop with a maximum retry count, a delay, and a final failure status.

**17. Why are functions useful in large deployment scripts?**

They separate operations into reusable units, improve readability, reduce duplication, and simplify troubleshooting.

**18. Your script processes filenames containing spaces. How do you prevent incorrect splitting?**

Quote variables and use safe iteration patterns. For example, use `"${array[@]}"` for arrays and `read -r` when reading lines.

**19. A service is active according to systemd, but the application is not responding. What would you do?**

Check the application's actual health endpoint, inspect service logs with `journalctl`, examine network listeners, and verify dependencies. Systemd's active state alone does not guarantee application health.

**20. How would you design a reliable service monitoring script?**

Use reusable functions, validated inputs, bounded retries, meaningful exit codes, structured logging, and an application-level health check.

---

# 🧪 25. Hands-On Practice

## Conditions

- [ ] Write a script that checks whether a number is positive or negative.
- [ ] Write a script that checks whether a user is root.
- [ ] Write a script that checks whether a file exists.
- [ ] Write a script that checks whether a directory exists.
- [ ] Write a script that checks disk usage and prints a warning above 80%.
- [ ] Practise `if`, `if-else`, and `if-elif-else`.
- [ ] Practise numeric and string comparison operators.
- [ ] Practise `&&`, `||`, and `!`.
- [ ] Create a service-management menu using `case`.

## Loops

- [ ] Print numbers from 1 to 10 using a `for` loop.
- [ ] Print numbers from 1 to 10 using a `while` loop.
- [ ] Print numbers from 1 to 10 using an `until` loop.
- [ ] Use `break` to stop a loop.
- [ ] Use `continue` to skip an iteration.
- [ ] Loop through a list of server names.
- [ ] Create a bounded retry loop for a service check.

## Functions and Arrays

- [ ] Create a function that displays the hostname.
- [ ] Create a function that accepts two arguments.
- [ ] Create a function that returns a success or failure status.
- [ ] Use `local` variables inside a function.
- [ ] Create an array containing five server names.
- [ ] Print all array elements.
- [ ] Count the number of array elements.
- [ ] Loop through the array.

## Error Handling and Debugging

- [ ] Practise `set -e`.
- [ ] Practise `set -u`.
- [ ] Practise `set -o pipefail`.
- [ ] Handle a failed command using `||`.
- [ ] Send an error message to standard error.
- [ ] Check syntax using `bash -n`.
- [ ] Debug a script using `bash -x`.

## Mini Project

- [ ] Build the service health checker.
- [ ] Accept a service name as an argument.
- [ ] Check whether the service is active.
- [ ] Return a meaningful exit status.
- [ ] Test it with an active service.
- [ ] Test it with an inactive or nonexistent service.

---

# 🏁 26. Part 2 Completion Checklist

- [ ] Understand conditional statements.
- [ ] Understand `if`, `else`, and `elif`.
- [ ] Understand numeric and string comparison.
- [ ] Understand file and directory test operators.
- [ ] Understand logical operators.
- [ ] Understand `case` statements.
- [ ] Understand `for` loops.
- [ ] Understand `while` loops.
- [ ] Understand `until` loops.
- [ ] Understand `break` and `continue`.
- [ ] Understand functions.
- [ ] Understand function arguments and return statuses.
- [ ] Understand local variables.
- [ ] Understand arrays.
- [ ] Understand string manipulation.
- [ ] Understand file operations.
- [ ] Understand error handling.
- [ ] Understand debugging.
- [ ] Complete the service health checker.
- [ ] Answer the Part 2 interview questions.

---

## 🏁 Part 2 Key Takeaway

You have now moved beyond basic shell scripting.

You can build scripts that:

- Make decisions.
- Repeat operations.
- Process multiple servers.
- Organize code using functions.
- Handle arrays and strings.
- Read and manipulate files.
- Detect errors.
- Debug execution problems.

These skills are directly useful in deployment automation, server monitoring, maintenance scripts, and CI/CD workflows.

**Next: Part 3 – Advanced Shell Scripting, Cron Jobs, Automation, Logging, Security, Production Projects & Senior DevOps Interview Scenarios.**







# 🐧 Linux Shell Scripting – Part 3: Advanced Automation, Cron Jobs & Production DevOps

> **Roadmap:** Topic 12 of 12  
> **Topic:** Shell Scripting  
> **Part:** 3 of 3 – Final Part  
> **Domain:** DevOps / Cloud Engineering / Linux Administration / Automation  
> **Difficulty:** Intermediate to Advanced  
> **Objective:** Learn to schedule scripts, build production-ready automation, implement logging and monitoring, handle signals, secure scripts, and automate real-world DevOps operations.

---

## 📚 Table of Contents

1. Introduction to Part 3
2. Cron Jobs
3. Understanding Crontab Syntax
4. Managing Cron Jobs
5. Cron Job Examples
6. Cron Environment and Troubleshooting
7. Logging in Shell Scripts
8. Log Levels
9. Log Rotation
10. Signals in Linux
11. The `trap` Command
12. Temporary Files and Cleanup
13. Process Management in Scripts
14. Lock Files and Preventing Duplicate Execution
15. Shell Script Security
16. ShellCheck and Script Quality
17. Production-Ready Bash Script Structure
18. Project 1 – Automated Backup System
19. Project 2 – Disk Monitoring and Alerting
20. Project 3 – Application Deployment Automation
21. Scheduling Production Automation
22. Troubleshooting Production Scripts
23. Senior DevOps Best Practices
24. Quick Command Reference
25. Senior DevOps Interview Questions
26. Hands-On Practice
27. Linux Shell Scripting – Final Completion Checklist

---

# 1. Introduction to Part 3

In Part 1, you learned:

- Shebangs
- Variables
- User input
- Command-line arguments
- Exit codes

In Part 2, you learned:

- Conditions
- Comparison operators
- Loops
- Functions
- Arrays
- File operations
- Error handling
- Debugging

Now we will combine those concepts into practical automation.

Imagine you manage a production Linux server.

You need to:

1. Back up application data every night.
2. Monitor disk usage every 10 minutes.
3. Record script execution in log files.
4. Prevent two backup processes from running simultaneously.
5. Clean temporary files even if a script fails.
6. Automatically execute maintenance tasks.

This is where advanced shell scripting becomes valuable.

---

# 2. Cron Jobs

## 2.1 What Is Cron?

Cron is a time-based job scheduler in Linux.

It allows you to execute commands or scripts automatically at scheduled times.

For example:

- Back up databases every night.
- Clean temporary files every Sunday.
- Check disk usage every 10 minutes.
- Generate reports every morning.
- Execute maintenance scripts every month.

Instead of manually executing these tasks, Cron handles them automatically.

### Real-World DevOps Scenario

Suppose an application generates log files continuously.

You want to run a cleanup script every day at midnight.

Without Cron:

You must manually execute the cleanup command every day.

With Cron:

Linux automatically executes the script according to the schedule.

---

# 3. Understanding Crontab Syntax

## 3.1 What Is Crontab?

Crontab stands for cron table.

It contains scheduled commands and their execution times.

### Syntax

```bash
* * * * * command
```

There are five time fields.

| Field | Meaning | Allowed Values |
|---|---|---|
| 1st | Minute | 0–59 |
| 2nd | Hour | 0–23 |
| 3rd | Day of month | 1–31 |
| 4th | Month | 1–12 |
| 5th | Day of week | 0–7 |

For day of week, both 0 and 7 generally represent Sunday.

### Visual Representation

```text
 ┌───────────── minute (0–59)
 │ ┌─────────── hour (0–23)
 │ │ ┌───────── day of month (1–31)
 │ │ │ ┌─────── month (1–12)
 │ │ │ │ ┌───── day of week (0–7)
 │ │ │ │ │
 * * * * * command
```

## 3.2 Special Symbols

| Symbol | Meaning |
|---|---|
| `*` | Every possible value |
| `,` | Multiple specific values |
| `-` | Range of values |
| `/` | Step or interval |

### Examples

Every minute:

```bash
* * * * * command
```

Every hour:

```bash
0 * * * * command
```

Every day at midnight:

```bash
0 0 * * * command
```

Every Sunday:

```bash
0 0 * * 0 command
```

Every 10 minutes:

```bash
*/10 * * * * command
```

Every weekday at 9 AM:

```bash
0 9 * * 1-5 command
```

Every day at 6:30 PM:

```bash
30 18 * * * command
```

---

# 4. Managing Cron Jobs

## 4.1 Open Your Crontab

```bash
crontab -e
```

This opens your personal crontab for editing.

You can add scheduled commands here.

## 4.2 List Existing Cron Jobs

```bash
crontab -l
```

Displays your current user's scheduled cron jobs.

## 4.3 Remove Your Crontab

```bash
crontab -r
```

Removes the current user's entire crontab.

**Warning:** This is destructive. It removes all scheduled jobs for that user.

## 4.4 Edit Another User's Crontab

```bash
sudo crontab -u username -e
```

This edits the specified user's crontab.

## 4.5 Check Cron Service

On many Debian and Ubuntu systems:

```bash
sudo systemctl status cron
```

On many RHEL-family systems:

```bash
sudo systemctl status crond
```

Service names differ between Linux distributions.

To start the service on a system using `cron`:

```bash
sudo systemctl start cron
```

On a system using `crond`:

```bash
sudo systemctl start crond
```

---

# 5. Cron Job Examples

## Example 1: Run a Backup Script Every Day at 2 AM

```bash
0 2 * * * /home/user/backup.sh
```

## Example 2: Monitor Disk Usage Every 10 Minutes

```bash
*/10 * * * * /home/user/disk-monitor.sh
```

## Example 3: Run a Cleanup Script Every Sunday at Midnight

```bash
0 0 * * 0 /home/user/cleanup.sh
```

## Example 4: Execute a Script Every Hour

```bash
0 * * * * /home/user/health-check.sh
```

## Example 5: Run a Script at 8 AM Every Weekday

```bash
0 8 * * 1-5 /home/user/report.sh
```

## Example 6: Redirect Output to a Log File

```bash
0 2 * * * /home/user/backup.sh >> /home/user/backup.log 2>&1
```

Explanation:

| Component | Meaning |
|---|---|
| `>>` | Append standard output |
| `2>&1` | Send standard error to standard output |
| `backup.log` | Stores the script output |

---

# 6. Cron Environment and Troubleshooting

One of the most common problems in production is that a script works manually but fails when executed through Cron.

Why?

Cron runs jobs with a limited environment.

For example, the PATH available in an interactive terminal may be different from the PATH available to Cron.

## 6.1 Use Absolute Paths

Avoid:

```bash
backup.sh
```

Prefer:

```bash
/home/user/scripts/backup.sh
```

Inside scripts, prefer full executable paths when reliability requires them.

You can identify executable paths using:

```bash
command -v tar
```

Example output:

```text
/usr/bin/tar
```

## 6.2 Define PATH Explicitly

At the beginning of a script:

```bash
#!/usr/bin/env bash

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

export PATH
```

Use a PATH appropriate to your system.

## 6.3 Check Cron Logs

On Debian/Ubuntu:

```bash
sudo grep CRON /var/log/syslog
```

On many RHEL-family systems:

```bash
sudo grep CRON /var/log/cron
```

On systemd-based systems:

```bash
sudo journalctl -u cron
```

Or:

```bash
sudo journalctl -u crond
```

### Senior DevOps Scenario

Your backup script works perfectly when executed manually but does not run at 2 AM.

Investigation:

1. Check whether the Cron service is active.
2. Check `crontab -l`.
3. Check the Cron logs.
4. Verify the script's absolute path.
5. Verify executable permissions.
6. Check the PATH and environment variables.
7. Check the user running the job.
8. Check file and directory permissions.
9. Capture script output and errors in a log file.

This is a practical troubleshooting workflow.

---

# 7. Logging in Shell Scripts

## 7.1 What Is Logging?

Logging means recording information about script execution.

Production scripts should not rely only on terminal output.

Imagine a backup script runs every night.

You need to know:

- When did the backup start?
- Did it complete?
- Did it fail?
- How long did it take?
- Which files were backed up?

Logs provide this information.

## 7.2 Basic Logging

```bash
#!/usr/bin/env bash

echo "Backup started" >> backup.log

tar -czf backup.tar.gz /home/user/data

echo "Backup completed" >> backup.log
```

This records the messages in `backup.log`.

However, this basic example does not check whether the backup command succeeded.

## 7.3 Logging with Timestamps

```bash
#!/usr/bin/env bash

echo "$(date '+%Y-%m-%d %H:%M:%S') Backup started" >> backup.log
```

Example:

```text
2026-11-21 02:00:00 Backup started
```

Timestamps make troubleshooting much easier.

## 7.4 Create a Logging Function

```bash
#!/usr/bin/env bash

LOG_FILE="/var/log/myapp.log"

log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
}

log "Application started"
log "Backup completed"
```

A function avoids repeating timestamp logic throughout the script.

Note that writing to `/var/log` may require appropriate permissions.

---

# 8. Log Levels

Production applications commonly use different log levels.

| Level | Meaning |
|---|---|
| DEBUG | Detailed diagnostic information |
| INFO | Normal operational events |
| WARN | Something unexpected that may need attention |
| ERROR | An operation failed |
| CRITICAL | A severe failure requiring urgent attention |

Example:

```bash
#!/usr/bin/env bash

log_info() {
    echo "$(date '+%F %T') [INFO] $1"
}

log_warn() {
    echo "$(date '+%F %T') [WARN] $1" >&2
}

log_error() {
    echo "$(date '+%F %T') [ERROR] $1" >&2
}

log_info "Deployment started"
log_warn "Disk usage is above 80%"
log_error "Deployment failed"
```

Output:

```text
2026-11-21 10:00:00 [INFO] Deployment started
2026-11-21 10:00:01 [WARN] Disk usage is above 80%
2026-11-21 10:00:02 [ERROR] Deployment failed
```

A more advanced logging implementation can send logs to journald or a centralized logging system.

---

# 9. Log Rotation

## 9.1 What Is Log Rotation?

Log rotation manages log files so they do not grow indefinitely.

Imagine a script writes 100 MB of logs every day.

After 30 days, the log file may consume approximately 3 GB.

This can create disk-space problems.

Log rotation helps by:

- Renaming old log files.
- Creating new log files.
- Compressing older logs.
- Removing logs beyond a retention period.

## 9.2 logrotate

Linux commonly uses `logrotate` to manage application and system logs.

Check whether it is installed:

```bash
logrotate --version
```

Example configuration:

```text
/var/log/myapp.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    create 0640 root adm
}
```

Explanation:

| Directive | Meaning |
|---|---|
| `daily` | Rotate daily |
| `rotate 7` | Keep seven rotated files |
| `compress` | Compress rotated logs |
| `missingok` | Do not fail if the log is missing |
| `notifempty` | Do not rotate an empty log |
| `create` | Create a new log with specified permissions and ownership |

The exact owner and group should match the application and logging design.

**Senior DevOps Insight:** Avoid implementing your own log rotation when the standard Linux `logrotate` utility already handles the requirement.

---

# 10. Signals in Linux

## 10.1 What Is a Signal?

A signal is a notification sent to a process to inform it that an event has occurred or that it should perform an action.

Signals are important when managing long-running scripts and services.

Common signals:

| Signal | Number | Meaning |
|---|---:|---|
| SIGHUP | 1 | Hangup |
| SIGINT | 2 | Interrupt |
| SIGQUIT | 3 | Quit |
| SIGKILL | 9 | Force termination |
| SIGTERM | 15 | Request termination |
| SIGCONT | 18 | Continue a stopped process |
| SIGSTOP | 19 | Stop a process |

Signal numbers can vary by architecture, so symbolic names are generally clearer.

## 10.2 SIGINT

Usually generated when the user presses:

```text
Ctrl + C
```

It requests interruption of the running process.

## 10.3 SIGTERM

Requests that a process terminate.

Example:

```bash
kill -TERM PID
```

This gives the process an opportunity to clean up.

## 10.4 SIGKILL

Forces the process to terminate immediately.

```bash
kill -9 PID
```

Unlike SIGTERM, SIGKILL cannot be caught or handled by the process.

**Senior DevOps Insight:** Prefer SIGTERM first. Use SIGKILL only when a process does not terminate appropriately and forceful termination is justified.

---

# 11. The trap Command

## 11.1 What Is trap?

`trap` allows a shell script to execute commands when it receives specific signals or when certain shell events occur.

This is especially useful for cleanup operations.

For example:

- Delete temporary files.
- Release lock files.
- Print shutdown messages.
- Clean up temporary directories.

### Syntax

```bash
trap 'commands' SIGNAL
```

## 11.2 Example: Catch Ctrl + C

```bash
#!/usr/bin/env bash

trap 'echo "Script interrupted"; exit 130' INT

while true
do
    echo "Running..."
    sleep 2
done
```

When you press Ctrl + C, the script executes the trap handler.

## 11.3 Example: Cleanup Temporary Files

```bash
#!/usr/bin/env bash

TEMP_FILE="/tmp/myapp-temp.txt"

cleanup() {
    echo "Cleaning temporary files"
    rm -f -- "$TEMP_FILE"
}

trap cleanup EXIT

touch "$TEMP_FILE"

echo "Script is running"
```

The cleanup function executes when the shell exits normally or through many exit paths.

## 11.4 Why Is This Important?

Imagine a deployment script:

1. Creates a temporary directory.
2. Downloads an application package.
3. Extracts files.
4. Deploys the application.
5. Removes temporary files.

If the deployment fails at step 3, temporary files may remain.

Using `trap` helps clean up resources even when the script exits early.

**Important:** A trap cannot guarantee cleanup after SIGKILL, sudden power loss, or every possible system failure.

---

# 12. Temporary Files and Cleanup

## 12.1 What Is a Temporary File?

A temporary file is used to store intermediate data during script execution.

For example:

- Downloaded packages.
- Intermediate command output.
- Temporary configuration files.
- Data processing results.

## 12.2 Using mktemp

Instead of manually choosing a temporary filename, use:

```bash
mktemp
```

Example output:

```text
/tmp/tmp.XYZ123
```

The filename is generated uniquely.

## 12.3 Create a Temporary Directory

```bash
TEMP_DIR=$(mktemp -d)
```

This creates a temporary directory and stores its path in `TEMP_DIR`.

## 12.4 Complete Cleanup Example

```bash
#!/usr/bin/env bash

set -euo pipefail

TEMP_DIR=$(mktemp -d)

cleanup() {
    rm -rf -- "$TEMP_DIR"
}

trap cleanup EXIT

echo "Temporary directory: $TEMP_DIR"

touch "$TEMP_DIR/test.txt"

echo "Processing completed"
```

Explanation:

1. Creates a unique temporary directory.
2. Stores the path in a variable.
3. Registers a cleanup function.
4. Executes the required operations.
5. Removes the temporary directory when the script exits.

**Security Warning:** Never run `rm -rf` on an unvalidated variable. In production, validate that the path is non-empty and matches the expected temporary-directory location before deleting it.

---

# 13. Process Management in Scripts

Scripts sometimes need to start, monitor, and manage background processes.

## 13.1 Run a Command in the Background

```bash
sleep 100 &
```

The `&` runs the command in the background.

## 13.2 Get the Background Process ID

```bash
sleep 100 &

PID=$!

echo "Background process ID: $PID"
```

`$!` stores the PID of the most recently started background process in the current shell.

## 13.3 Wait for a Process

```bash
sleep 5 &

PID=$!

wait "$PID"

echo "Background process completed"
```

`wait` waits for the specified child process and returns its exit status.

## 13.4 Check Process Status

```bash
ps -p "$PID"
```

This checks whether a process with that PID exists at the time of the check.

For a reliable script, also consider the process's exit status using `wait`.

---

# 14. Lock Files and Preventing Duplicate Execution

## 14.1 What Is a Lock File?

A lock prevents multiple instances of the same script from running simultaneously.

Imagine a backup script scheduled every five minutes.

The previous backup takes eight minutes.

Without protection, another backup may start before the previous one finishes.

This can cause:

- Duplicate operations.
- Resource contention.
- Inconsistent backup results.
- Unexpected load on the server.

## 14.2 Using flock

`flock` is a Linux utility for managing file locks.

Example:

```bash
#!/usr/bin/env bash

LOCK_FILE="/tmp/backup.lock"

exec 9>"$LOCK_FILE"

if ! flock -n 9; then
    echo "Another backup process is already running"
    exit 1
fi

echo "Backup started"

sleep 10

echo "Backup completed"
```

Explanation:

| Command | Purpose |
|---|---|
| `exec 9>` | Opens the lock file using file descriptor 9 |
| `flock -n 9` | Attempts to obtain the lock without waiting |
| `!` | Handles failure to obtain the lock |
| `exit 1` | Stops the duplicate execution |

The lock is released when the file descriptor closes, normally when the script exits.

For production, choose a lock-file location with appropriate permissions and test how your scheduler invokes the script.

---

# 15. Shell Script Security

Security is essential when writing automation that runs on production servers.

## 15.1 Avoid Running Everything as Root

Do not execute every script using:

```bash
sudo ./script.sh
```

unless elevated privileges are necessary.

Use the minimum permissions required.

## 15.2 Never Hardcode Passwords

Avoid:

```bash
PASSWORD="MySecretPassword"
```

Hardcoded credentials can leak through source control, logs, backups, and script access.

Use an appropriate secret manager or protected credential mechanism.

## 15.3 Quote Variables

Unsafe:

```bash
rm -rf $directory
```

Safer:

```bash
rm -rf -- "$directory"
```

Quoting prevents unwanted word splitting and wildcard expansion. The `--` prevents a path beginning with `-` from being interpreted as an option.

Quoting alone does not make an untrusted path safe to delete.

## 15.4 Validate User Input

Example:

```bash
#!/usr/bin/env bash

environment="${1:-}"

case "$environment" in
    development|testing|production)
        echo "Valid environment: $environment"
        ;;
    *)
        echo "Invalid environment"
        exit 1
        ;;
esac
```

This prevents arbitrary environment values from being accepted.

## 15.5 Avoid eval

`eval` executes text as shell code.

Untrusted input passed to `eval` can cause command injection.

Avoid it unless you have a specific, carefully reviewed requirement.

## 15.6 Protect Script Permissions

Example:

```bash
chmod 750 deployment.sh
```

This gives:

- Owner: read, write, execute.
- Group: read and execute.
- Others: no permissions.

Choose permissions according to your operational requirements.

## 15.7 Secure Temporary Files

Use `mktemp` instead of predictable temporary filenames.

Avoid creating temporary files in shared directories with insecure permissions.

## 15.8 Use ShellCheck

ShellCheck is a static analysis tool that identifies common shell scripting problems.

Check installation:

```bash
shellcheck --version
```

Run it:

```bash
shellcheck deployment.sh
```

It can identify issues such as:

- Unquoted variables.
- Incorrect condition syntax.
- Unused variables.
- Common portability problems.
- Suspicious command substitutions.

It is widely useful in DevOps code reviews and CI pipelines.

---

# 16. ShellCheck and Script Quality

## 16.1 What Is ShellCheck?

ShellCheck is a static analysis tool for shell scripts.

Static analysis means examining code without executing it.

Example:

```bash
#!/usr/bin/env bash

name="Jasin"

echo $name
```

ShellCheck may warn about the unquoted variable expansion.

A safer version:

```bash
echo "$name"
```

## 16.2 Why DevOps Engineers Use It

Consider a CI/CD pipeline that runs deployment scripts.

A small scripting mistake can:

- Delete the wrong files.
- Deploy to the wrong environment.
- Cause an incomplete backup.
- Break a scheduled job.

ShellCheck helps catch common mistakes before execution.

## 16.3 Include It in CI/CD

A simple CI validation step could be:

```bash
shellcheck scripts/*.sh
```

If ShellCheck reports errors or warnings, the pipeline can fail according to your team's quality rules.

Also run syntax checks:

```bash
bash -n scripts/deploy.sh
```

---

# 17. Production-Ready Bash Script Structure

A maintainable script should have a consistent structure.

Example:

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

# -------------------------
# Configuration
# -------------------------

APP_NAME="myapp"
ENVIRONMENT="${1:-development}"

# -------------------------
# Logging
# -------------------------

log_info() {
    echo "$(date '+%F %T') [INFO] $*"
}

log_error() {
    echo "$(date '+%F %T') [ERROR] $*" >&2
}

# -------------------------
# Validation
# -------------------------

validate_environment() {
    case "$ENVIRONMENT" in
        development|testing|production)
            return 0
            ;;
        *)
            log_error "Invalid environment"
            return 1
            ;;
    esac
}

# -------------------------
# Main Operation
# -------------------------

main() {
    validate_environment

    log_info "Starting $APP_NAME"
    log_info "Environment: $ENVIRONMENT"

    log_info "Operation completed"
}

# -------------------------
# Execution
# -------------------------

main "$@"
```

This example demonstrates organization rather than a complete deployment workflow.

A mature production script should also document dependencies, permissions, configuration, failure behavior, and recovery steps.

---

# 🚀 18. Project 1 – Automated Backup System

## Project Objective

Build a script that:

1. Creates a backup of a directory.
2. Adds a timestamp to the backup filename.
3. Stores the backup in a destination directory.
4. Logs the operation.
5. Checks whether the backup succeeded.
6. Removes old backups according to a retention policy.

## Step 1: Create a Script

```bash
nano backup.sh
```

## Step 2: Add the Code

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

SOURCE_DIR="/home/user/data"
BACKUP_DIR="/home/user/backups"
LOG_FILE="/home/user/backup.log"

TIMESTAMP=$(date '+%Y%m%d_%H%M%S')
BACKUP_FILE="$BACKUP_DIR/backup_$TIMESTAMP.tar.gz"

log() {
    echo "$(date '+%F %T') [INFO] $*" >> "$LOG_FILE"
}

error() {
    echo "$(date '+%F %T') [ERROR] $*" >> "$LOG_FILE"
}

main() {

    if [[ ! -d "$SOURCE_DIR" ]]; then
        error "Source directory does not exist"
        exit 1
    fi

    mkdir -p "$BACKUP_DIR"

    log "Backup started"

    if tar -czf "$BACKUP_FILE" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"; then
        log "Backup completed: $BACKUP_FILE"
    else
        error "Backup failed"
        exit 1
    fi

    find "$BACKUP_DIR" -type f -name 'backup_*.tar.gz' -mtime +7 -delete

    log "Old backups cleanup completed"
}

main "$@"
```

## Step 3: Make It Executable

```bash
chmod +x backup.sh
```

## Step 4: Execute It

```bash
./backup.sh
```

## Step 5: Verify Backups

```bash
ls -lh /home/user/backups
```

Check logs:

```bash
cat /home/user/backup.log
```

### What You Practised

- Variables.
- Timestamps.
- Functions.
- Conditions.
- Logging.
- Exit codes.
- File operations.
- Archive creation.
- Retention cleanup.

**Production Note:** This is a learning implementation. In production, configure source and destination paths carefully, validate backup integrity, test restoration, protect backup permissions, and consider off-host or encrypted storage. The example's retention command deletes matching old files; verify the retention policy before using it on important data.

---

# 🚀 19. Project 2 – Disk Monitoring and Alerting

## Project Objective

Build a script that:

- Checks filesystem usage.
- Detects high disk utilization.
- Prints a warning when usage exceeds a threshold.
- Returns a meaningful exit code.

## Step 1: Create the Script

```bash
nano disk-monitor.sh
```

## Step 2: Add the Code

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

THRESHOLD=80

check_disk() {

    while read -r filesystem usage mountpoint; do

        if [[ "$usage" =~ ^[0-9]+%$ ]]; then

            percentage="${usage%\%}"

            if (( percentage >= THRESHOLD )); then
                echo "WARNING: $mountpoint usage is $usage"
                return 1
            fi

            echo "OK: $mountpoint usage is $usage"
        fi

    done < <(df -P -x tmpfs -x devtmpfs | tail -n +2)
}

check_disk
```

## Step 3: Execute

```bash
chmod +x disk-monitor.sh

./disk-monitor.sh
```

Example output:

```text
OK: / usage is 45%
OK: /home usage is 61%
WARNING: /data usage is 89%
```

Actual output depends on your system.

### Important Design Consideration

This basic implementation returns a failure when it encounters the first filesystem above the threshold.

A more advanced implementation should continue checking every filesystem, record all warning mounts, and return a failure status after completing the full scan if any threshold was exceeded.

You can extend it to send notifications through an approved alerting system.

---

# 🚀 20. Project 3 – Application Deployment Automation

## Project Objective

Build a basic deployment script that:

1. Validates the target environment.
2. Checks whether the application directory exists.
3. Creates a deployment backup.
4. Copies new application files.
5. Reports deployment success or failure.

## Example Script

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

APP_DIR="/opt/myapp"
SOURCE_DIR="/tmp/myapp-build"
BACKUP_DIR="/opt/myapp-backups"

ENVIRONMENT="${1:-}"

log() {
    echo "$(date '+%F %T') [INFO] $*"
}

error() {
    echo "$(date '+%F %T') [ERROR] $*" >&2
}

validate_environment() {

    case "$ENVIRONMENT" in
        development|testing|production)
            log "Environment validated: $ENVIRONMENT"
            ;;
        *)
            error "Invalid environment"
            return 1
            ;;
    esac
}

deploy_application() {

    if [[ ! -d "$SOURCE_DIR" ]]; then
        error "Build directory not found"
        return 1
    fi

    mkdir -p "$BACKUP_DIR"

    if [[ -d "$APP_DIR" ]]; then

        timestamp=$(date '+%Y%m%d_%H%M%S')

        tar -czf "$BACKUP_DIR/app_$timestamp.tar.gz" \
            -C "$(dirname "$APP_DIR")" "$(basename "$APP_DIR")"

        log "Existing application backed up"
    fi

    mkdir -p "$APP_DIR"

    cp -a "$SOURCE_DIR/." "$APP_DIR/"

    log "Application files deployed"
}

main() {

    validate_environment
    deploy_application

    log "Deployment completed successfully"
}

main "$@"
```

### Example Execution

```bash
./deploy.sh production
```

### Important Production Considerations

This is not a complete zero-downtime deployment strategy.

A real deployment pipeline should also include:

- Artifact validation and integrity checks.
- Dependency installation.
- Configuration management.
- Secret management.
- Application health checks.
- Controlled service restarts or rolling deployments.
- Rollback procedures.
- Deployment audit logs.

For complex deployments, use appropriate tools such as Ansible, Docker, Kubernetes, or a CI/CD platform rather than building everything into one large Bash script.

---

# 21. Scheduling Production Automation

Now combine Cron with the projects.

## Schedule Daily Backup at 2 AM

Open:

```bash
crontab -e
```

Add:

```bash
0 2 * * * /home/user/scripts/backup.sh >> /home/user/scripts/backup-cron.log 2>&1
```

## Schedule Disk Monitoring Every 10 Minutes

```bash
*/10 * * * * /home/user/scripts/disk-monitor.sh >> /home/user/scripts/disk-monitor.log 2>&1
```

## Schedule Weekly Cleanup

```bash
0 0 * * 0 /home/user/scripts/cleanup.sh >> /home/user/scripts/cleanup.log 2>&1
```

### Production Checklist

Before scheduling:

- Confirm the script works manually.
- Use absolute paths.
- Confirm executable permissions.
- Confirm the Cron service is running.
- Check the scheduled user's permissions.
- Configure required environment variables.
- Capture standard output and errors.
- Prevent duplicate execution where needed.
- Test the scheduled execution.
- Monitor failures and alert on them.

---

# 22. Troubleshooting Production Scripts

| Problem | Possible Cause | Investigation |
|---|---|---|
| Script does not execute | Missing execute permission | `ls -l script.sh` |
| Command not found | PATH issue | `command -v command_name` |
| Script works manually but not in Cron | Limited Cron environment | Inspect Cron logs and PATH |
| Permission denied | Insufficient permissions | `id`, `ls -l`, `namei -l` |
| Script exits unexpectedly | Error or strict-mode behavior | `bash -x script.sh` |
| Pipeline hides failure | Missing `pipefail` | `set -o pipefail` |
| Duplicate backup execution | No locking | Use `flock` |
| Log file grows continuously | No rotation | Configure `logrotate` |
| Temporary files remain | Missing cleanup | Use `trap` |
| Wrong file is deleted | Unsafe path or variable | Validate path before deletion |

### Senior DevOps Troubleshooting Workflow

When a production automation script fails:

1. Identify the exact script and execution time.
2. Check its exit status.
3. Inspect the log file.
4. Reproduce the issue in a safe environment.
5. Run Bash tracing if necessary.
6. Check permissions and environment variables.
7. Check dependencies and external services.
8. Identify the root cause.
9. Apply a controlled fix.
10. Test both success and failure scenarios.

---

# 23. Senior DevOps Best Practices

A senior engineer should think beyond whether a script works once.

The important question is whether it is safe, repeatable, observable, and maintainable.

### Reliability

- Use bounded retries.
- Check important command exit statuses.
- Avoid uncontrolled infinite loops.
- Use locking for tasks that must not overlap.
- Implement recovery and rollback where appropriate.

### Security

- Never hardcode secrets.
- Avoid unnecessary root privileges.
- Validate user input.
- Quote variable expansions.
- Use secure temporary files.
- Restrict script permissions.

### Maintainability

- Write small reusable functions.
- Use descriptive variable names.
- Document required inputs.
- Keep configuration separate from logic where appropriate.
- Avoid unnecessary complexity.

### Observability

- Add timestamps to logs.
- Use meaningful log levels.
- Return useful exit statuses.
- Monitor scheduled jobs.
- Integrate with centralized logging and alerting where needed.

### Quality

- Run `bash -n`.
- Run ShellCheck.
- Test in development before production.
- Test failure conditions.
- Review destructive commands carefully.
- Test backup restoration, not just backup creation.

---

# 24. Quick Command Reference

| Command | Purpose |
|---|---|
| `crontab -e` | Edit current user's Cron jobs |
| `crontab -l` | List current user's Cron jobs |
| `crontab -r` | Remove current user's Crontab |
| `systemctl status cron` | Check Cron service on applicable systems |
| `systemctl status crond` | Check crond service on applicable systems |
| `journalctl -u cron` | Inspect Cron service logs |
| `journalctl -u crond` | Inspect crond service logs |
| `date` | Display current date and time |
| `logrotate` | Manage log rotation |
| `trap` | Handle signals and shell events |
| `mktemp` | Create a temporary file |
| `mktemp -d` | Create a temporary directory |
| `kill -TERM PID` | Request process termination |
| `kill -9 PID` | Force process termination |
| `wait PID` | Wait for a background child process |
| `$!` | Most recently started background process ID |
| `flock -n` | Attempt a non-blocking file lock |
| `shellcheck script.sh` | Analyze shell script quality |
| `bash -n script.sh` | Check script syntax |
| `bash -x script.sh` | Trace script execution |
| `chmod 750 script.sh` | Set restricted executable permissions |

---

# 25. Senior DevOps Interview Questions

## Basic

**1. What is Cron?**

Cron is a time-based job scheduler used to automatically execute commands or scripts at specified intervals.

**2. What is Crontab?**

Crontab is the configuration table containing scheduled Cron jobs.

**3. What are the five fields in a Cron expression?**

Minute, hour, day of month, month, and day of week.

**4. What is the difference between `>` and `>>`?**

`>` overwrites a file, while `>>` appends output to a file.

**5. What is log rotation?**

It manages log files by rotating, compressing, and removing older logs according to a configured policy.

## Intermediate

**6. What is the purpose of `trap`?**

It allows a script to execute specific commands when it receives signals or when certain shell events occur.

**7. What is the difference between SIGTERM and SIGKILL?**

SIGTERM requests termination and can be handled. SIGKILL forces termination and cannot be caught or ignored.

**8. Why do we use `mktemp`?**

It creates uniquely named temporary files or directories, reducing filename collisions and security risks.

**9. What is `flock`?**

It provides file-based locking to prevent multiple processes from executing a protected operation simultaneously.

**10. What is ShellCheck?**

ShellCheck is a static analysis tool that identifies common shell scripting errors and questionable practices.

## Senior DevOps Scenario-Based Questions

**11. Your backup script works manually but fails when scheduled through Cron. What would you investigate?**

I would check the Cron service, the user's crontab, absolute paths, PATH, environment variables, file permissions, execution user, and Cron logs.

**12. A backup script takes longer than its scheduling interval. How would you prevent overlapping executions?**

I would use a lock mechanism such as `flock` to ensure that only one instance executes the protected backup operation at a time.

**13. Your deployment script fails halfway through. How would you prevent temporary files from remaining?**

I would register a cleanup function with `trap` and use a temporary directory created with `mktemp`.

**14. Your application log file consumes several gigabytes of disk space. What would you do?**

I would configure log rotation using `logrotate`, establish retention and compression policies, and investigate why the application generates so much log data.

**15. How would you design a reliable automated backup system?**

I would validate source and destination paths, use timestamped backups, check archive creation, log execution, prevent overlapping runs, define retention, protect backup storage, and regularly test restoration.

**16. A script returns success even though an earlier command in its pipeline failed. Why?**

By default, a Bash pipeline normally returns the status of its final command. I would use `set -o pipefail` and explicitly handle important command failures.

**17. How would you secure a deployment script?**

I would validate inputs, avoid hardcoded credentials, minimize privileges, quote variables, protect files, avoid unsafe evaluation, and use a secure secret-management mechanism.

**18. Would you use Bash for every automation task?**

No. Bash is excellent for Linux command orchestration and relatively small automation tasks. For complex APIs, extensive data processing, and larger automation systems, Python or specialized DevOps tools may be more suitable.

**19. How would you integrate shell scripting into CI/CD?**

I would use scripts for repeatable tasks such as validation, artifact handling, deployment steps, and health checks. I would run syntax checks and ShellCheck during CI, enforce exit statuses, and keep secrets in the CI/CD secret store.

**20. What makes a shell script production-ready?**

Reliability, security, input validation, error handling, logging, observability, testing, idempotency where appropriate, controlled permissions, and clear recovery behavior.

---

# 26. Hands-On Practice

## Cron

- [ ] Schedule a script to run every minute.
- [ ] Schedule a script to run every hour.
- [ ] Schedule a backup at 2 AM.
- [ ] List your Cron jobs.
- [ ] Inspect Cron logs.
- [ ] Troubleshoot a script that fails under Cron.

## Logging

- [ ] Create a logging function.
- [ ] Add timestamps.
- [ ] Implement INFO, WARN, and ERROR levels.
- [ ] Redirect output to log files.
- [ ] Configure a basic logrotate rule.

## Signals and Cleanup

- [ ] Catch Ctrl + C using `trap`.
- [ ] Create and clean up a temporary directory.
- [ ] Practise `SIGTERM`.
- [ ] Understand why `SIGKILL` cannot be caught.
- [ ] Use `wait` with a background process.

## Reliability and Security

- [ ] Use `flock` to prevent duplicate execution.
- [ ] Validate command-line arguments.
- [ ] Avoid hardcoded credentials.
- [ ] Use safe temporary files.
- [ ] Run ShellCheck.
- [ ] Test error conditions.

## Projects

- [ ] Complete the automated backup system.
- [ ] Complete the disk monitoring script.
- [ ] Complete the application deployment script.
- [ ] Schedule at least one script using Cron.
- [ ] Test failure and recovery scenarios.

---

# 27. Linux Shell Scripting – Final Completion Checklist

## Part 1 – Fundamentals

- [ ] Shell and Bash fundamentals
- [ ] Shebang
- [ ] Script creation and execution
- [ ] Comments
- [ ] Variables
- [ ] Environment variables
- [ ] User input
- [ ] `echo` and `printf`
- [ ] Command-line arguments
- [ ] Special variables
- [ ] Exit codes

## Part 2 – Programming and Error Handling

- [ ] Conditions
- [ ] Comparison operators
- [ ] File test operators
- [ ] Logical operators
- [ ] `case`
- [ ] `for`
- [ ] `while`
- [ ] `until`
- [ ] `break` and `continue`
- [ ] Functions
- [ ] Function arguments and return statuses
- [ ] Arrays
- [ ] String manipulation
- [ ] File operations
- [ ] Error handling
- [ ] Debugging

## Part 3 – Production Automation

- [ ] Cron jobs
- [ ] Crontab management
- [ ] Cron troubleshooting
- [ ] Logging
- [ ] Log levels
- [ ] Log rotation
- [ ] Linux signals
- [ ] `trap`
- [ ] Temporary files
- [ ] Background processes
- [ ] `wait`
- [ ] `flock`
- [ ] Script security
- [ ] ShellCheck
- [ ] Production script structure
- [ ] Backup automation
- [ ] Disk monitoring
- [ ] Deployment automation

---

# 🏆 Final Takeaway – Linux Shell Scripting

You have now covered the complete Shell Scripting learning material across three parts.

You should be able to write scripts that:

- Automate repetitive Linux administration tasks.
- Process multiple servers and files.
- Make decisions and handle errors.
- Schedule recurring operations.
- Create and manage backups.
- Monitor disk usage and services.
- Record logs and troubleshoot failures.
- Protect against duplicate execution.
- Support deployment and CI/CD workflows.

**Remember:** Knowing Bash syntax is only the first step. A strong DevOps engineer must understand how scripts behave when commands fail, servers become unavailable, permissions change, and production environments behave unexpectedly.

The goal is not simply to write a script that works. The goal is to write automation that is safe, reliable, observable, and maintainable.
