# Module 6 - Command Line

# Windows Command Line


## Overview: Why the Command Line Matters

The Windows Command Prompt (`cmd.exe`) is the default command-line interpreter that allows you to interact with your system efficiently. While graphical interfaces are intuitive, the command line provides speed, automation capabilities, and remote management functionality. It requires fewer system resources than GUI applications, making it practical for older systems and cloud environments. Understanding the command line is essential for system administration, troubleshooting, and penetration testing.

## Core Concepts

**Windows Path**: Commands can only execute if they're located in directories listed in your system PATH. Use the `set` command to view your current PATH environment variables, which show where Windows searches for executables.

**Help System**: Most commands support the `/?` option to display help information. Use this whenever you need to understand a command's syntax and options. The `cls` command clears the terminal screen.

## Basic System Information

**System Information Discovery**

The `ver` command displays your Windows version. For deeper system insights, use `systeminfo`, which lists OS information, hardware specifications, processor count, and memory details. These commands are essential for reconnaissance during security assessments.

**Output Management**

For long command outputs that exceed your terminal window, pipe the result through `more` (e.g., `driverquery | more`). This displays output page-by-page—press Spacebar to advance and Ctrl+C to exit. Alternative commands not covered in depth here include `chkdsk` (check disk health), `driverquery` (list device drivers), and `sfc /scannow` (scan and repair system files).

## Basic System Information (Detailed)

**Checking Environment**

Before running commands, verify what's available in your PATH using `set`. The output shows environment variables like `LOGNAME`, `NUMBER_OF_PROCESSORS`, and `OS`.

**Version Information**

`ver` returns the Windows version number. Understanding your OS version is critical for identifying available features and vulnerabilities.

**Comprehensive System Details**

`systeminfo` provides extensive information including hostname, OS type, build number, manufacturer, configuration type, and processor details. This command is invaluable during system audits and penetration testing.

**Usage Tip**: Combine these commands with output redirection to create system reports for documentation.

## Network Troubleshooting

**Network Configuration Assessment**

Use `ipconfig` to display IP address, subnet mask, and default gateway. For comprehensive network information including DNS servers and DHCP configuration, run `ipconfig /all`. This shows whether DHCP is enabled, lease dates, and IPv6 addresses.

**Connectivity Testing**

The `ping` command sends ICMP packets to verify connectivity to a target. Syntax: `ping target_name`. Success indicates the target is reachable and responding. The output shows reply times (TTL - Time To Live) and packet statistics. This is your first tool for diagnosing network issues.

**Route Tracing**

`tracert target_name` traces the network path to reach a destination, showing each router hop along the way. If routers don't respond, you'll see asterisks (\*) and "Request timed out" messages. This helps identify where network problems occur—if an early hop fails, the issue is local; if a later hop fails, it's at the destination.

**DNS Lookup**

`nslookup domain` resolves domain names to IP addresses. You can specify a custom name server with `nslookup domain 1.1.1.1`. This reveals which DNS server provided the answer and helps diagnose DNS configuration issues.

**Network Statistics**

`netstat` displays active network connections and listening ports. Key options:

- `-a`: Show all connections and listening ports
- `-b`: Display the program associated with each connection
- `-o`: Show the process ID (PID) for each connection
- `-n`: Use numerical format for addresses and ports

Combine these: `netstat -abon` reveals listening services and which processes own them. For example, `sshd.exe` listening on port 22 indicates SSH is active. This is crucial for identifying open services during security assessments.

## File and Disk Management

**Directory Navigation and Listing**

`cd` changes directories. `cd ..` goes up one level, `cd target_directory` enters a specific folder. `dir` lists contents of the current directory with timestamps and file sizes. Options include:

- `/a`: Show hidden and system files
- `/s`: Recursively list subdirectories

`tree` visually displays the directory structure as a hierarchy. Use `tree` to understand folder organization at a glance.

**Directory Operations**

`mkdir directory_name` creates new directories. `rmdir directory_name` removes empty directories. These are straightforward file system operations.

**File Viewing and Copying**

`type filename` displays file contents. For large files, use `more` to page through content (press Spacebar to continue, Ctrl+C to exit). The `copy source destination` command duplicates files. Example: `copy test.txt test2.txt` creates a copy in the same directory.

**File Movement**

`move filename destination` moves or renames files. Moving to a parent directory uses `..`: `move test2.txt ..` moves the file up one level.

**File Deletion**

`del` or `erase` removes files. Be cautious—deletion is permanent. `del filename` removes a specific file.

**Wildcard Patterns**

Use `*` to match multiple files. For example, `copy *.md C:\Markdown` copies all files with the `.md` extension to the Markdown directory. This is powerful for batch operations.

## Task and Process Management

**Listing Running Processes**

`tasklist` displays all running processes with their Process IDs (PIDs). Output shows image name, PID, session type (Services/Console), and session number. This is your inventory of what's executing on the system.

**Filtering Processes**

`tasklist /FI "imagename eq sshd.exe"` filters results to show only specific processes. The `/FI` flag uses the syntax "imagename eq process\_name". This is efficient for finding specific services or applications.

**Terminating Processes**

`taskkill /PID target_pid` terminates a process by its PID. Example: `taskkill /PID 4567` kills the process with ID 4567. This is equivalent to ending a task in Task Manager and essential for stopping unresponsive processes.

**Red Team Application**: During penetration testing, understanding running processes helps identify security software, administrative tools, and potential privilege escalation targets. Killing defender or logging processes can be part of post-exploitation activities.

## Additional Commands & Conclusion

**System Control**

`shutdown /s` shuts down the system. `shutdown /a` aborts a scheduled shutdown. These commands provide remote system control capabilities.

**Key Takeaways**

- All covered commands support `/?` for help pages
- The `more` command paginates long output for readability
- Command chaining with pipes (`|`) allows sophisticated workflows
- Understanding process management is critical for system administration and security testing

**Practical Lab Focus**

In labs, you'll use these commands to:

1. Gather system information (hostname, OS version, network config)
2. Troubleshoot connectivity issues (ping, tracert, ipconfig)
3. Manage files and directories (copy, move, delete with patterns)
4. Monitor and control running processes (tasklist, taskkill)
5. Document system state for compliance or security audits

These commands form the foundation of Windows system access and manipulation, making them essential for both legitimate administration and security research.

## **Quick Reference for Exams/Labs:**

- `systeminfo` → comprehensive system overview
- `ipconfig /all` → network configuration
- `ping target` → verify connectivity
- `tracert target` → identify routing problems
- `netstat -abon` → detect active services and processes
- `tasklist /FI "imagename eq process"` → find specific processes
- `taskkill /PID pid` → terminate processes
- `dir /s /a` → full directory listing with hidden files
- `copy *.ext destination` → batch file operations

***

# Windows PowerShell


## Overview: The Power in PowerShell

PowerShell is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework built on the .NET framework. Unlike traditional command-line tools that process and output data as plain text, PowerShell is object-oriented—it passes objects with properties and methods between commands rather than just text. This fundamental difference makes it dramatically more powerful for data manipulation and system interaction. Initially exclusive to Windows, PowerShell Core (2016) expanded support to macOS and Linux, making it a versatile tool across modern IT environments.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

The key innovation was recognizing that Windows and Unix handle system operations differently: Windows uses structured data and APIs, while Unix treats everything as text files. PowerShell bridges this gap with an object-oriented approach, combining scripting simplicity with the power of the .NET framework.

## What Is PowerShell: Core Concepts

PowerShell was developed in 2006 by Jeffrey Snover at Microsoft to overcome limitations of cmd.exe and batch files in managing complex enterprise environments. As IT infrastructure evolved beyond single operating systems, the need for a versatile automation tool grew, resulting in PowerShell Core—an open-source, cross-platform version available on Windows, macOS, and Linux.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

**Objects vs Text:** In traditional shells, commands return text that you must parse. In PowerShell, commands return objects—containers of information with specific properties (characteristics) and methods (actions). For example, a car object might have properties like Color and Model, with methods like Drive() and HonkHorn(). This object-oriented design allows far more sophisticated data manipulation without string parsing.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

**Cmdlets:** PowerShell operations center on cmdlets (pronounced "command-let"), which return objects retaining their properties and methods. This allows seamless chaining of commands for powerful workflows.

## PowerShell Basics: Getting Started

To begin working with PowerShell, start your virtual machine and connect via SSH using Remmina. Navigate to Applications > Internet > Remmina to establish your connection to the lab environment. PowerShell is Windows-native but also available on modern Linux and macOS systems.

The basic workflow involves launching PowerShell and interacting with it through a command-line interface. Before proceeding, ensure you've completed the "Windows Command Line" room to understand foundational command-line concepts. This room covers the unique object-oriented aspects that distinguish PowerShell from traditional shells.

## Navigating the File System and Working with Files

PowerShell provides cmdlets for file system navigation comparable to traditional CLI commands but with enhanced capabilities. The `Get-ChildItem` cmdlet (alias: `dir`) lists files and directories in a specified location using the `-Path` parameter. Without a Path parameter, it displays the current working directory's contents in a table showing Mode, LastWriteTime, Length, and Name columns.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

Key file system cmdlets include directory listing and navigation tools. Like Unix's `ls`, `Get-ChildItem` explores directories and views their contents, supporting filtering and recursive operations. The object-oriented nature means results are actual file/directory objects you can further manipulate, not just text to be parsed.

Common patterns: `Get-ChildItem -Path C:\Users\captain` returns all items in that directory with full object properties available for further processing.

## Piping, Filtering, and Sorting Data

Piping (represented by `|`) is fundamental in both Unix shells and PowerShell, allowing output from one command to become input for another, creating data processing pipelines. PowerShell's piping is uniquely powerful because it passes entire objects rather than just text.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

For example: `Get-ChildItem | Sort-Object -Property Length` retrieves directory contents and sorts them by file size while preserving all object properties and methods. This means every item in the pipeline retains its full data structure for manipulation.

Filtering and sorting operations work on the actual data objects, not string representations, enabling sophisticated queries. You can chain multiple cmdlets to create complex workflows that would require significant text parsing in traditional shells. The object-oriented pipeline means you're always working with rich data structures, not flattened text.

## System and Network Information Gathering

PowerShell provides comprehensive system interrogation capabilities essential for administrators and security professionals. The `Get-ComputerInfo` cmdlet retrieves detailed system information including operating system version, hardware specifications, BIOS details, and more—providing a complete snapshot of system configuration in a single command. Its traditional counterpart `systeminfo` returns only basic details.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

The `Get-LocalUser` cmdlet lists all local user accounts on a system with their properties. Understanding user accounts is critical for security configuration and access management.

These information-gathering cmdlets form the foundation for system administration, configuration auditing, and security assessments. They're essential for understanding target systems in penetration testing scenarios and for defensive security monitoring.

## Real-Time System Analysis: Dynamic Monitoring

For advanced system information beyond static configuration, PowerShell provides real-time monitoring capabilities. The `Get-Process` cmdlet delivers a detailed view of all running processes including CPU usage, memory consumption, and process IDs—making it invaluable for performance troubleshooting and security investigations.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

The `Get-Service` cmdlet retrieves service status information, revealing which services are running, stopped, or paused. In forensics and offensive operations, detecting suspicious or anomalous services installed on systems is critical for understanding system compromise or identifying persistence mechanisms.

These real-time analysis tools are extensively used by incident responders, malware analysts, and penetration testers to understand system state, detect anomalies, and investigate compromised systems.

## Scripting: Automation and Offensive Applications

Scripting is writing and executing command sequences in a text file (script) to automate manual tasks. It's fundamentally a to-do list for computers—each line represents an automatic action, saving time, reducing errors, and enabling complex operations impossible to perform manually.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

PowerShell scripting capabilities vary dramatically by user role:

**Blue Team (Defensive):** Security analysts and incident responders use PowerShell scripts for log analysis, anomaly detection, and indicators of compromise (IOCs) extraction. Scripts can reverse-engineer malicious code and automate system scanning for intrusion signs. They're essential for automating defensive responses at scale.

**Red Team (Offensive):** Penetration testers and ethical hackers leverage PowerShell for system enumeration, executing remote commands, and crafting obfuscated scripts to bypass defensive controls. Deep integration with all Windows system types makes it ideal for simulating real-world attacks and testing system resilience.

**System Administrators:** Automation of integrity checks, configuration management, and network security in large-scale environments. Scripts enforce security policies, monitor system health, and respond automatically to security incidents, enhancing overall security posture.

## Remote Command Execution: Invoke-Command

The `Invoke-Command` cmdlet is fundamental for remote system management and a critical tool for both defenders and attackers. It executes commands on remote computers, enabling efficiency and automation across multiple machines.[[tryhackme](https://tryhackme.com/room/windowspowershell)]​

Two primary patterns: First, execute local scripts on remote machines using `-FilePath` and `-ComputerName` parameters. Second, execute arbitrary commands using `-ScriptBlock` without requiring local scripts, allowing ad-hoc command execution across remote systems. Example: `Invoke-Command -ComputerName RoyalFortune -ScriptBlock { Get-Service }` executes Get-Service on the remote machine and returns results locally.

For penetration testers and attackers, this cmdlet enables lateral movement and command execution on target systems. Combined with appropriate credentials and network access, it facilitates sophisticated multi-stage attacks and system compromise.

## Key Concepts to Remember

- **Objects, not text:** PowerShell returns objects with properties and methods, enabling powerful data manipulation without string parsing
- **Piping power:** The pipe operator (`|`) chains commands, passing complete objects rather than text strings
- **Cmdlets are specialized:** Each cmdlet serves specific functions; understanding the available cmdlets for your task is essential
- **Real-time monitoring:** Get-Process and Get-Service reveal dynamic system state for incident response and forensics
- **Remote execution:** Invoke-Command enables efficient management and lateral movement across networked systems
- **Scripting for scale:** Automation through scripting is essential across all cybersecurity roles—defensive, offensive, and administrative
- **Object-oriented architecture:** The fundamental difference from traditional shells enables sophisticated, programmable system interaction

## Red Team / Penetration Testing Applications

PowerShell is a primary tool in offensive security due to its deep Windows integration and post-exploitation capabilities. System enumeration via Get-Process and Get-Service identifies running applications and persistence mechanisms. Remote command execution via Invoke-Command enables lateral movement and multi-stage attacks. Custom scripts bypass defenses and execute payloads while maintaining stealth. Understanding PowerShell deeply is as essential for attackers as it is for defenders—mastery enables effective post-compromise operations and persistence establishment on Windows systems.


***

# Linux Shells

## Overview

A shell is the interface between you and the operating system. Rather than relying on the GUI's point-and-click approach, shells allow you to interact with Linux through the command line interface (CLI). This method is far more efficient and resource-friendly. When you write a shell script, you're essentially combining multiple commands into one file that can be executed repeatedly, automating tasks that would otherwise require manual repetition.

## Shell Basics & Interaction

The shell prompt (typically `user@hostname:~$`) appears when you're ready to enter commands. Understanding how to navigate and interact is fundamental to shell work. Your default working directory is typically your home directory. You can check your current location with the `pwd` command (Print Working Directory). To change directories, use the `cd` (Change Directory) command followed by the path.

For listing directory contents without opening a GUI, use the `ls` command. To view file contents, use `cat` followed by the filename. The `grep` command is a powerful tool for searching specific patterns or keywords within files. For example, `grep THM dictionary.txt` will search for the word "THM" and display the matching line.

## Types of Linux Shells

Multiple shells exist on Linux systems, each with distinct characteristics. Check your current shell with `echo $SHELL`, and view all available shells in `/etc/shells` using `cat /etc/shells`. You can switch between shells by simply typing the shell name (e.g., `zsh`) or permanently change your default shell with `chsh -s /usr/bin/zsh`.

**Bash (Bourne Again Shell)** is the default for most distributions. It combines features from earlier shells and includes widely compatible scripting capabilities. Key features include tab completion for faster command entry, command history (accessible via the `history` command and up/down arrow keys), and comprehensive documentation. Bash is user-friendly and widely used in production environments.

**Fish (Friendly Interactive Shell)** prioritizes user-friendliness over compatibility. It features a simpler syntax that's easier for beginners, auto spell-correction for commands you type, and customizable command prompts with visual themes. However, its scripting capabilities are more limited than Bash.

**Zsh (Z Shell)** is a modern shell combining Bash's traditional capabilities with Fish's user-friendly features. It offers advanced tab completion with suggestions based on your previous commands, extensive customization through the oh-my-zsh framework, and excellent scripting capabilities. The main trade-off is slightly slower performance compared to Bash due to its extensive customization features.

## Shell Scripting Fundamentals

A shell script is simply a text file containing commands. Before creating a script, select a text editor to create a file with the `.sh` extension. Every script must begin with a **shebang** (`#!/bin/bash`), which tells the system to use Bash as the interpreter. This is crucial for proper execution.

## Variables

Variables store values for reuse throughout your script. Instead of hardcoding values, store them in variables and reference them where needed. Use the `echo` command to display text, `read name` to capture user input and store it in a variable called `name`, and reference variables with the `$` prefix (e.g., `echo "$name"`). This makes scripts reusable and cleaner.

## Loops

Loops automate repetitive tasks. A `for` loop iterates through a list of values. The syntax `for i in {1..10}; do` starts a loop where `i` takes values 1 through 10, with `do` marking the start of the loop body and `done` marking the end. Between `do` and `done`, place the commands you want executed in each iteration. This eliminates the need to type similar commands multiple times.

## Conditional Statements

Conditional statements execute code only when specific conditions are met. The `if` statement checks a condition and executes code if it's true. Use `elif` for additional conditions and `else` for the fallback case. The `fi` keyword ends the statement. For example, `if [ "$name" = "Stewart" ]; then` checks if the variable `name` equals "Stewart" and executes the following code if true.

## Comments

Comments explain code and don't affect execution. Start with a `#` sign followed by a space. Comments are essential for complex or unclear code sections, helping you and others understand the script's logic later.

## Practical Script Execution

To execute a script, first grant it executable permissions using `chmod +x script_name.sh`. Then execute it with `./script_name.sh`. The `./` tells the shell to look for the script in the current directory rather than searching PATH environment directories. Without `./&#32;`, the shell would search system paths and fail to find your script.

## Key Concepts to Remember

- **The shell is an interface**: It's your command interpreter and automation tool—more powerful than GUIs for repetitive tasks.
- **Default shell matters**: Bash is the standard, but Fish and Zsh offer modern alternatives with trade-offs.
- **Scripting automates tasks**: Combine commands into scripts to run repeatedly without manual re-entry.
- **Shebang is essential**: `#!/bin/bash` must be the first line of every Bash script.
- **Variables store and reuse values**: Use `$` to reference them.
- **Loops and conditionals control flow**: They enable complex logic and automation.
- **File permissions matter**: Use `chmod +x` to make scripts executable.
- **grep is your search tool**: Use it frequently in security contexts to find specific patterns in logs or data.

## Red Team/Security Relevance

Shell scripts are invaluable in penetration testing and security automation. Attackers use shell scripts to automate reconnaissance, execute payloads, and maintain persistence. During labs, you'll write scripts to search logs for suspicious activity, extract data from files, and automate repetitive security checks. Understanding shells deeply is essential for both defense and offense in cybersecurity.