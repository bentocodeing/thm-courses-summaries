# Module 4 - Linux fundamentals

# Linux Fundamentals Part 1

## 
Overview

Linux is a free, open-source operating system widely used in servers, embedded systems, smart cars, and Android devices. Unlike Windows which runs on proprietary code, Linux variants (called distributions) can run on anything from a desktop to a supercomputer with just 512MB of RAM. This course covers the essential commands needed to interact with a Linux machine through the terminal.


## What Linux Powers

Linux isn't just a desktop OS—it's everywhere. Websites you visit daily run on Linux servers. Point-of-sale systems in shops, car entertainment panels, traffic light controllers, and critical infrastructure all rely on Linux. The efficiency of the operating system makes it the most popular choice for servers and embedded devices worldwide.


## Linux Distributions and Flavors

"Linux" is an umbrella term for multiple operating systems based on the Linux kernel (unlike Windows which is monolithic). Different distributions exist for different purposes. Ubuntu and Debian are common because they're extensible—you can run Ubuntu as a lightweight server or as a full desktop. The course uses Ubuntu as the standard distribution. Important note: Ubuntu Server can run on systems with only 512MB of RAM, making it incredibly efficient.


## Essential Terminal Commands

The terminal is text-based and initially intimidating, but commands become muscle memory quickly. You need to master these core functions: navigate files, output their contents, create files, search efficiently, and understand file permissions.


## Basic Commands

**echo** outputs any text you provide. It requires no special syntax for single words (`echo Hello`) but strings with spaces need double quotes (`echo "Hello Friend"`). This command is fundamental for creating files and testing.

**whoami** tells you the current logged-in username. Use this to verify your identity on the system.

**ls** (listing) shows directory contents. You can list any directory without navigating into it using `ls Pictures` (shows contents without changing your current location).

**cd** (change directory) moves you between folders. When you change directories, you lose your previous location unless you know its path.

**pwd** (print working directory) reveals your exact location on the filesystem. This is critical when navigating deeply nested directories—you can lose track of where you are.

**cat** (concatenate) outputs file contents. It works with any file type, not just text. Pro tip: You can cat a file in a different directory using `cat /home/ubuntu/Documents/todo.txt` without navigating to it first. This is useful when files contain sensitive data like passwords, flags, or configuration settings.

## 
Filesystem Navigation

Before doing anything, you must know what files exist. The **ls** command lists directories and files. Once you know what exists, use **cd** to enter folders. For example: `cd Pictures` to enter, then `ls` to see contents.

When navigating deep into directories, use **pwd** to print the full path so you don't lose orientation. For instance, if you're in \~/Documents but don't remember the full path, `pwd` returns `/home/ubuntu/Documents`, allowing you to reference it later.

## 
Search and File Discovery

Efficiency with Linux comes from automating searches rather than manually checking every folder. Two powerful commands make this possible.

### The find Command

**find** searches through directories recursively and is incredibly flexible. In its simplest form, `find -name passwords.txt` searches your current directory for that filename. The command walks through every folder and returns the full path (e.g., `./folder1/passwords.txt`).

For extension-based searches, use wildcards: `find -name *.txt` finds all text files. This automatically searches subdirectories, which would be tedious to do manually.

The syntax is simple: `find -name [filename]`. The power lies in finding files you don't know the exact location of, or discovering all files matching a pattern.


### The grep Command

**grep** searches *within* files for specific content. If you have a 244-line access log but only want entries from a specific IP address, `grep "81.143.211.90" access.log` returns only matching lines. The output shows the matching line with the IP visible.


For recursive searching across multiple files and subdirectories, use `grep -R "PRETTY_NAME" /etc/` to search all files in the `/etc/` directory tree. Example output:

```javascript
text/etc/sudoers: Permission denied
/etc/os-release:PRETTY_NAME="Ubuntu"

```


This shows file paths before matches, making it easy to locate where values appear. This is critical for security work—quickly finding configurations across your system without manual inspection.

## 
Shell Operators (Powerful Tools)

Operators allow you to chain commands and automate workflows, which is where Linux becomes genuinely powerful for efficiency.

**&** (ampersand) runs commands in the background. If you're copying a large file, it normally locks your terminal. Using `command &` lets it run invisibly, freeing you to do other work. This is essential for long-running tasks.

**&&** (double ampersand) chains commands intelligently. `command1 && command2` only runs command2 if command1 succeeds. This prevents cascading failures. For example, if you wanted to create a file and then check it, you'd use `echo hello > file && cat file`, ensuring the cat only runs if the echo succeeded.

**>** (redirection operator) sends output to a file instead of your terminal. `echo hey > welcome` creates a file called "welcome" containing "hey". Running this again overwrites the file. This is how you create files from terminal commands.

**>>** (append operator) adds output to the end of a file instead of replacing it. `echo hello >> welcome` adds "hello" to the bottom of the welcome file. If the file contains "hey", it now contains:

```javascript
texthey
hello

```


These operators are the foundation of Linux automation. Understanding them allows you to build complex workflows from simple commands.

## 
Common Misconceptions to Avoid

- The terminal isn't powerful because it's complicated—it's powerful because simple commands can be chained together invisibly
- You don't need GUI tools to be efficient; the terminal actually makes you faster once muscle memory develops
- File contents discovered with `cat` can reveal sensitive data (passwords, API keys, configurations)—this is a critical red team consideration

## 
Red Team and Security Relevance

Linux fundamentals directly enable penetration testing workflows. Finding files recursively with `find` allows attackers to locate configuration files, credentials, or vulnerable code. The `grep` command searches logs for evidence of intrusion or finds hardcoded secrets in source code. Shell operators enable chaining of reconnaissance commands to automate discovery. For example: `find / -name "*.txt" -type f 2>/dev/null | grep -i password` would locate password-containing files system-wide—a classic reconnaissance technique.

Understanding Linux paths and file structure is essential before advanced attacks like privilege escalation or persistence can succeed.

## 
Key Facts to Remember

- **echo** + **>** operator = file creation (`echo content > filename`)
- **find** searches by filename across directory trees; **grep** searches within file contents
- Commands can be chained with **&&** (conditional) or **&** (background execution)
- **pwd** prevents orientation loss in complex directory structures
- **cat** retrieves file contents anywhere without navigation
- **ls**, **cd**, **pwd** form the navigation trinity—you use them constantly
- Output redirection (**>** replaces, **>>** appends) is fundamental to scripting

***

## LINUX FUNDAMENTALS PART 2

## Overview

Linux Fundamentals Part 2 builds on foundational Linux knowledge, focusing on remote access via SSH, command arguments and flags, filesystem manipulation, file permissions, and critical system directories. This course emphasizes practical skills for controlling remote machines and managing files on a Linux system.

## Introduction

This room builds directly on Part 1. The course covers four main learning objectives: using flags and arguments to extend command behavior, interacting with the filesystem through create/move/delete operations, understanding file permissions and access control, and finally running scripts and executables.

## SSH - Accessing Remote Linux Machines

**What is SSH?**
Secure Shell (SSH) is a protocol for encrypted communication between devices. When you send input in human-readable format, it gets encrypted for network transmission, then decrypted on the remote machine. This ensures your commands and data remain secure while traveling over the network.

The TryHackMe in-browser terminal uses the same SSH protocol that's standard for remote Linux administration. There are two machines deployed in this room: your personal Linux machine and the TryHackMe AttackBox for running commands.

## Introduction to Flags and Switches

**Core Concept**
Commands can accept arguments identified by hyphens followed by keywords. These are called flags or switches and modify how a command behaves.


**Default Behavior vs. Modified Behavior**
By default, the `ls` command lists only visible files in the working directory. Hidden files (those starting with a dot `.`) are excluded. Using the `-a` flag (short for `--all`) extends the behavior to show all files including hidden ones.


**How to Learn Command Options**
Commands that accept arguments provide a `--help` option, which lists possible options with brief descriptions. This generates output similar to the man page. For example, `ls --help` shows all available flags for the ls command.


**The Man (Manual) Page**
Manual pages are the definitive documentation for Linux commands and applications. Access them using `man command_name`. For example, `man ls` opens the manual for the ls command. Navigate using arrow keys, press `h` for help, or `q` to quit. Man pages follow a standard format: NAME, SYNOPSIS (syntax), DESCRIPTION, and OPTIONS sections.


**Key flags for common commands:**

- `-h` or `--help`: Show command help
- `-a` or `--all`: Include hidden files
- `-l`: Long listing format
- `-h`: Human-readable format (for file sizes)
- `-down`: Navigate man pages

## Filesystem Interaction Continued

**Core Commands for File Management**
The filesystem interaction relies on these fundamental commands:

- `ls` and `find`: List and locate files
- `cd`: Navigate directories
- `touch`: Create files
- `mkdir`: Create directories (make directory)
- `cp`: Copy files or folders
- `mv`: Move files or folders (also used for renaming)
- `rm`: Remove files
- `file`: Determine file type


**Complete Command Table**

| Command | Full Name | Purpose |
| --- | --- | --- |
| touch | touch | Create file |
| mkdir | make directory | Create a folder |
| cp | copy | Copy a file or folder |
| mv | move | Move or rename a file or folder |
| rm | remove | Delete a file |
| file | file | Determine the type of a file |
| cd | change directory | Change working directory |
| ls | list | List directory contents |
| find | find | Search for files |


**Practical Workflows**
These commands allow you to create new files and folders, copy or move existing files between locations, delete files you no longer need, and rename files by moving them with the mv command. The combination of these tools enables complete filesystem management.

## Permissions 101

**Permission System Overview**
Not all users can access all files and folders. The permission system determines which actions are allowed and which users or groups have those permissions. Understanding permissions is critical for security and access management.


**Reading Permissions**
When you use `ls -l` (list with long format), you see three columns at the beginning that represent permissions. Each row shows permissions for a single file or folder, revealing file ownership and what actions are permitted.


**Permission Columns (reading left to right from&#32;`ls -l`&#32;output)**
The first three columns are:

1. **Owner permissions**: What the file owner can do
2. **Group permissions**: What users in the group can do
3. **Other permissions**: What everyone else can do

Each column contains three characters representing read (r), write (w), and execute (x) permissions.


**Important Files in /etc**
The `ls -l` command combined with viewing the `/etc` directory reveals important system files:

- `sudoers` file: Lists users and groups with permission to run sudo commands (commands as root)
- `sudoers.d` directory: Contains additional sudo configuration files
- `passwd` file: Stores user account information
- `shadow` file: Stores encrypted user passwords using sha512 hashing

The passwd and shadow files are special for Linux because they manage how the system stores and manages passwords for each user in encrypted format.

## Common Directories

**/etc - System Configuration**
The `/etc` directory is one of the most important root directories, containing system files used by the operating system. The name comes from "etcetera" (leftover files). Key files here include configuration for sudo permissions and user password management.


**Important /etc Files:**

- `sudoers`: Defines which users can execute commands as root
- `passwd`: User account information
- `shadow`: Encrypted password hashes (sha512 format)
- `sudoers.d`: Directory containing additional sudo configurations

These files control system security and user access. The passwd and shadow files are critical because they handle password storage and authentication for all user accounts on the system.


**Other Notable Root Directories**
Beyond /etc, Linux systems have many other important root directories storing specific types of data and system components. Each serves a distinct purpose in how the OS operates and stores information. Understanding these directories helps you navigate the filesystem and comprehend where system data is organized.

## **Red Team/Penetration Testing Relevance**

- **SSH**: The primary protocol for remote system access; attackers exploit weak SSH configurations and credentials to gain initial machine access
- **Flags and commands**: Understanding how to use commands efficiently speeds up reconnaissance and exploitation during pentesting
- **File permissions**: Misconfigurations in permissions are common privilege escalation vectors; the sudoers file is a critical target
- **/etc directory**: Contains sensitive configuration files and password hashes; accessing /etc/shadow is a primary goal in privilege escalation
- **File management**: Being able to create, modify, and hide files is essential for maintaining persistence on compromised systems


## **Common Misconceptions to Avoid**

- Flags are not just optional extras; they fundamentally change how commands behave
- Hidden files (those starting with `.`) are truly invisible by default and can be used by attackers to hide malware
- File permissions are absolute; even as a regular user, you can protect your files from other users through proper chmod settings
- The `/etc/shadow` file should never be world-readable; if it is, password cracking becomes trivial

## **Quick Reference**

- SSH syntax: `ssh username@hostname`
- View hidden files: `ls -a`
- View file permissions: `ls -l`
- View man pages: `man command_name`
- File creation: `touch filename`
- Directory creation: `mkdir dirname`
- Copy with permissions: `cp -r source destination`
- Change permissions: `chmod` (covered in Part 1)
- View /etc contents: `ls -l /etc`

***

# Linux Fundamentals Part 3 

## Overview

This module covers practical Linux utilities and system maintenance. You'll work with everyday tools, understand how processes work, and learn to automate and maintain your system effectively. The focus is on practical skills you'll actually use when working with Linux, including package management, logging, and automation.

## Terminal Text Editors

### Introduction to Text Editing

Previously you used basic tools like `echo` and pipe operators to create and manipulate files, but this approach doesn't efficiently handle multi-line files. Terminal text editors provide a proper way to create and edit text files directly in the shell.

### Nano

Nano is a beginner-friendly text editor. To create or edit a file, use the command `nano filename`, replacing "filename" with your target file. When nano launches, you can start typing immediately. Navigation uses arrow keys (up/down to move between lines), and the Enter key creates new lines.

**Key shortcuts in nano:**

- `^G` - Get Help
- `^O` - Write Out (save)
- `^W` - Where Is (search)
- `^K` - Cut Text
- `^X` - Exit
- `^R` - Read File
- `^\&#32;`- Replace
- `^U` - Paste Text
- `^T` - Spell Check

Nano covers the most general text editing needs: searching, cutting, pasting, saving, and exiting. It's ideal for quick edits and is very forgiving for newcomers to the terminal.

### Vim

Vim is a more powerful alternative available on most systems. TryHackMe has a dedicated room for learning Vim in depth. Unlike nano's immediate text entry, Vim operates in modes. You enter insert mode to type text, and use command mode for navigation and operations. This makes it more complex but far more efficient for experienced users.

## General/Useful Utilities

### Downloading Files (Wget)

`wget` is a command-line tool for downloading files from the web via HTTP. It mimics accessing the file in a browser but directly saves it to your machine. The syntax is straightforward: `wget https://example.com/file.txt`. This is essential for downloading programs, scripts, or data files in automated workflows.


### Transferring Files Securely (SCP - SSH)

Secure Copy (SCP) transfers files between computers using SSH protocol, providing both authentication and encryption. Unlike regular `cp`, which works locally, SCP allows secure file transfer between two systems over the network. It works bidirectionally:

- **Pull from remote:** Copy files and directories from a remote system to your local system
- **Push to remote:** Copy files and directories from your local system to a remote system

This is critical for secure data transfer between servers and prevents credentials/sensitive data from being exposed in plain text over the network.


### Python Simple HTTP Server

A quick way to serve files over HTTP is using Python. The command `python3 -m http.server` starts a basic web server in the current directory. This is useful for sharing files quickly in local networks or testing web content without setting up Apache/Nginx. Clients can download files directly from the served directory using a web browser or tools like wget.

## Processes 101

### What Are Processes?

Processes are running programs on your machine managed by the kernel. Each process receives a unique Process ID (PID). PIDs increment sequentially—the 60th process to start gets PID 60, the 61st gets PID 61, and so on. Understanding processes is essential for system monitoring and control.


### Viewing Processes

The `ps` command lists running processes in the current user session with details like:

- PID (process identifier)
- TTY (terminal where running)
- TIME (CPU time used)
- CMD (the actual command/program)

To see processes from other users and system processes, use `ps aux`. The `aux` flags provide additional context for filtering and analyzing all system processes. For example, running `ps aux` twice shows incrementing PIDs, demonstrating how the system tracks each new process instance.

## System Automation with Cron

### Understanding Cron

Users may want to schedule tasks to run automatically after system boot—like running commands, backing up files, or launching applications. The `cron` process, started during boot, is responsible for facilitating and managing cron jobs.


### Managing Cron Jobs

Crontab is the tool for creating and managing cron jobs. Crontab is a process started during boot and is responsible for facilitating and managing cron jobs. Each task is defined by a single line with fields specifying the time parameters:

- Minute (m): 0-59
- Hour (h): 0-23
- Day of Month (dom): 1-31
- Month (mon): 1-12
- Day of Week (dow): 0-6

Use wildcards (`*`) to run at any time in a field. For example, to run a backup every week at 5 AM: `0 5 * * 1 tar -zcf /var/backups/home.tgz /home/`

**Key output:**

- Crontab job output (including errors) goes through email to the user
- Redirected output (unless redirected elsewhere) goes to the crontab file
- Cron uses the system daemon's notion of time and timezones

For detailed syntax and examples, check the manual pages: `man crontab(5)` and `man cron(8)`.

## Package Management

### Software Repositories

When developers submit software to the community, it goes into an "apt" repository. If approved, it's released publicly. Two major features of Linux stand out here: user accessibility and open source merit.

When using `ls` on Ubuntu systems, the `/etc/apt` directory contains the sources list (gateway/registry for software):

- `sources.list` - Primary repository configuration
- `apt.conf.d`, `auth.conf.d`, `preferences.d` - Configuration files
- Additional source repositories can be added (from trusted vendors like Canonical's partner repository, security repositories, etc.)


### Installing and Managing Packages

The `apt` command (Advanced Package Tool) is the package manager for Debian/Ubuntu systems. You can:

- Install packages: `apt install package-name`
- Update package lists: `apt update`
- Upgrade installed packages: `apt upgrade`
- Remove packages: `apt remove package-name`

While OS vendors maintain repositories, you can also add third-party repositories to access additional software. These are added to the sources configuration to extend available packages beyond the official repositories.

## System Logging

### Understanding Logs

Logs are stored in the `/var/log` directory. The OS automatically manages logs through a process called "rotating"—old logs are compressed and archived to prevent disk space issues. Log files contain crucial information about system operations, errors, and security events.

### Important Service Logs

Linux systems typically run multiple services that generate logs:

- **Apache2 (web server):** Logs include access logs, error logs, and status information
- **Fail2ban service:** Monitors attempted brute force attacks; logs suspicious login attempts
- **UFW (Uncomplicated Firewall):** Logs firewall rules, blocked connections, and allowed traffic

These logs are essential for:

- Troubleshooting application issues
- Security monitoring and intrusion detection
- Understanding system behavior and performance
- Compliance and auditing

## Key Facts to Remember

1. **Terminal editors are essential:** Nano is beginner-friendly for quick edits; Vim is powerful for advanced users
2. **File transfer methods differ:** wget for HTTP downloads, SCP for secure remote transfers
3. **Processes are numbered:** Each process gets a unique incrementing PID for identification
4. **Automation saves time:** Cron jobs run scheduled tasks automatically after boot or at specified intervals
5. **Repositories centralize software:** The apt system manages installation from trusted repositories
6. **Logs reveal everything:** Monitor logs in `/var/log` for security, troubleshooting, and system health

## Red Team / Penetration Testing Relevance

**Lateral movement & persistence:** Understanding processes helps identify running services and potential exploitation targets. Use `ps aux` to find privileged processes and services.

**Scheduled task abuse:** Cron jobs can be modified or created by attackers for persistence. Check crontabs for suspicious scheduled tasks: `crontab -l` for user tasks.

**Log tampering:** Attackers try to cover tracks by deleting or modifying logs in `/var/log`. Monitor for missing or suspicious log entries.

**Package supply chain:** Compromised repositories or third-party packages can distribute malware. Only add trusted repositories and verify package integrity.

**File exfiltration:** SCP and wget are used by attackers to download tools and exfiltrate data. Monitor network traffic and process execution for these utilities.

**Service enumeration:** Tools like `ps aux` and package lists (`apt list --installed`) reveal the attack surface. Fewer running services = smaller attack surface.