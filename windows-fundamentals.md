# Module 5 - Windows Fundamentals

# Windows Fundamentals 1

## 
Overview

Windows is the dominant operating system for both home and corporate networks since 1985, making it a prime target for attackers and malware writers. This module covers the Windows desktop environment, file systems, user management, security controls, and system monitoring tools essential for both defensive and offensive security roles.

## Windows Editions and Versions

Modern Windows comes in multiple flavors suited to different needs. Windows 11 (current version) exists in Home and Pro variants, with Pro offering features like BitLocker encryption that Home lacks. Historically, Vista and Windows 8 were short-lived failures, while Windows 7 had long support. Windows Server 2019/2025 serves enterprise environments.

**Key fact:** The VM in this module runs Windows Server 2019 Standard. Understanding version differences helps identify system capabilities during reconnaissance—Pro/Server editions have more advanced security and administration features.

## The File System: NTFS Architecture

The **New Technology File System (NTFS)** is the modern Windows file system, replacing older FAT16/FAT32 and HPFS systems. You still see FAT on USB devices and SD cards, but modern Windows installations use NTFS exclusively.


NTFS is a **journaling file system**, meaning it maintains a log file that allows automatic repair of files and folders if the system crashes unexpectedly. This is a major advantage over FAT. NTFS addresses previous limitations by supporting files larger than 4GB, folder/file compression, specific permission controls, and Encryption File System (EFS).


**NTFS Permissions and Access Control:**
Each file and folder can have specific permissions controlling who can access them. The six permission types are:

- Full Control: Complete access and modification rights
- Modify: Change content but not ownership
- Read & Execute: View and run files
- List Folder Contents: View folder items
- Read: View files only
- Write: Create new files or modify existing ones

To view permissions, right-click the file/folder, select Properties, click the Security tab, and check the Group or User Names list to see assigned permissions.


**Alternate Data Streams (ADS):** NTFS files can contain multiple data streams beyond the standard $DATA stream. Windows Explorer doesn't display ADS, but PowerShell or third-party tools can reveal them. Malware writers abuse ADS to hide malicious code or data, making it a critical forensic artifact. However, ADS also marks files downloaded from the internet, tracking their origin.


**Penetration testing note:** When analyzing Windows systems, check for hidden ADS—they're a common malware persistence mechanism and often overlooked by basic file inspection.

## The Windows Desktop (GUI)

The Windows Desktop is the graphical user interface users see after login. It includes the taskbar, start menu, and various UI elements. The login screen requires valid credentials (username and password) before access is granted, whether to a local machine or domain-joined system in Active Directory.

Understanding GUI components helps during phishing and social engineering attacks, as attackers often spoof these familiar interfaces to capture credentials.


### Introduction to Windows

Windows is a complex product with many system files, utilities, settings, and features. This module provides foundational knowledge to navigate the OS, understand its structure, and interact with its administration tools more comfortably.


### Windows\\System32 Folder

The `C:\Windows` directory (location can vary via environment variables) is where the Windows operating system resides. Within it lies the crucial System32 folder containing system libraries and tools. System environment variables like `%windir%` point to this location, storing information about OS paths, processor count, and temporary folder locations.

Many critical system executables, DLLs, and configuration files live here, making it a high-value target for attackers. During reconnaissance, examining System32 reveals installed tools, drivers, and system capabilities.

## User Accounts, Profiles, and Permissions

Windows supports two types of local user accounts:

**Administrator:** Can make system changes—add/delete users, modify groups, adjust system settings. This account type poses security risks because running daily tasks with administrative privileges makes malware infections more devastating.

**Standard User:** Can only modify their own files and folders, cannot perform system-level changes or install programs. This is the safer account type for everyday work.

On Windows systems, you can discover user accounts through Start Menu → Other Users → System Settings, or via command line tools. Most home users run as administrators, but this violates the principle of least privilege.

## User Account Control (UAC)

User Account Control is a security feature introduced in Windows Vista and continued in all subsequent versions. UAC limits daily administrative tasks by default, even for administrator accounts. When an administrator logs in, the session doesn't run with elevated privileges—operations requiring higher-level privileges trigger a consent prompt.

**How UAC works:** When you attempt an action needing elevated permissions, UAC prompts you to confirm. If you approve, the operation proceeds with elevated privileges. This prevents malware from silently escalating without user awareness.

**Important caveat:** UAC doesn't apply to the built-in local administrator account. This account always runs with full privileges, making it dangerous for daily use.

**Red team perspective:** UAC bypasses are a common post-exploitation technique. If a user grants permission through a UAC prompt (perhaps via social engineering), the attacker gains elevated context. UAC alone isn't a strong security boundary for skilled adversaries.

## Settings and the Control Panel

Windows provides two primary locations for system configuration: **Settings** and **Control Panel**.

The **Control Panel** was the traditional interface for decades, used to add printers, uninstall programs, adjust hardware settings, and modify system configurations. It's older and less modern in appearance.

**Settings** was introduced in Windows 8 for touch-screen devices and is now the primary configuration location in Windows 10 and 11. It's becoming the preferred interface, though both still exist and offer overlapping functionality. Settings is more modern and user-friendly.

Both interfaces allow system changes but access different underlying configuration stores and tools. Understanding both helps during system administration and reconnaissance.

## Task Manager

Task Manager is the system utility providing visibility into running applications and processes. It shows CPU and RAM utilization metrics, falling under the Performance category. Access it by right-clicking the taskbar.

By default, Task Manager opens in **Simple View**, showing limited information—just basic running apps. Click "More details" to switch to the detailed view, which reveals processes, performance metrics, startup programs, services, and more detailed resource usage.

Task Manager is invaluable for troubleshooting performance issues, identifying suspicious processes, and understanding system behavior. For attackers, monitoring running processes helps identify security tools and understand the target environment.

**Quick access:** Right-click taskbar and select Task Manager.

## Red Team and Pentesting Relevance

**Windows as a target:** Understanding Windows internals helps identify attack surfaces. NTFS permissions can be misconfigured, allowing privilege escalation. UAC bypasses, credential harvesting from the system, and malware execution in the context of elevated processes are common attack chains.

**Key exploitation vectors:**

- Extracting credentials via registry or memory (Windows stores creds differently than other systems)
- Abusing UAC consent prompts or bypasses
- Leveraging NTFS alternate data streams to hide payloads
- Examining running processes in Task Manager to identify security software
- Exploiting weak file/folder permissions to achieve privilege escalation
- Abusing the built-in administrator account if enabled

**Defensive implications:** Regular permission audits, restricting local administrator access, monitoring ADS and file modifications, and understanding UAC limitations help defenders harden Windows systems against these tactics.

## Quick Facts to Remember

- NTFS is journaling; FAT is not—this allows crash recovery
- Permissions: Full Control, Modify, Read & Execute, List Folder Contents, Read, Write
- UAC doesn't apply to the built-in administrator—avoid using it
- Task Manager = process monitoring and performance insight
- System32 contains critical OS files and executables
- ADS can hide data but also marks downloaded files with zone identifiers
- Two user types: Administrator (system-wide changes) and Standard User (limited)
- Settings is newer; Control Panel is legacy but both exist
- Windows is heavily targeted—understand its architecture to exploit or defend it



***

# Windows Fundamentals 2

## Overview: Everything Starts with MSConfig

The critical entry point for accessing all advanced Windows system tools and utilities covered in this module is **MSConfig** (System Configuration utility). MSConfig is the central hub where you can access, configure, and launch virtually every advanced Windows management tool. Everything you'll encounter in this course—from managing system startup to troubleshooting—flows through msconfig. Make msconfig your first stop for system administration and troubleshooting.

## System Configuration (MSConfig): The Master Control Panel

**What it is:** System Configuration (MSConfig) is an advanced utility designed for troubleshooting startup issues and is launched by searching "msconfig" in Windows.

**Important:** You need local administrator rights to open this utility.


### The Five Core Tabs of MSConfig


**General Tab**
Controls what devices and services Windows loads on startup. Three startup modes are available: Normal (loads all device drivers and services), Diagnostic (basic devices only), and Selective (choose what loads). This tab is your first line of defense when troubleshooting startup problems.

**Boot Tab**
Defines various boot options for the operating system. This includes boot timeout settings, safe boot options, GUI boot preferences, and boot information configuration. Allows you to boot into Safe Mode or other diagnostic modes when normal startup fails.

**Services Tab**
Lists all services configured for the system regardless of their running state. A service is a special background application that runs without user interaction. You can enable or disable services here, and Microsoft warns that some secure services cannot be disabled. This is where you identify problematic background processes.

**Startup Tab**
In Windows 10/11 client systems, the Startup tab shows programs and services configured to run automatically at login. However, on the attached Windows Server machine, the startup management is handled differently—use Task Manager's Startup section instead. Windows advises using Task Manager (taskmgr) for startup item management rather than relying on MSConfig.

**Tools Tab**
Contains a list of system utilities you can launch directly from MSConfig with descriptions of each tool's purpose. Selected commands display the command line needed to launch that tool. This tab provides quick access to essential Windows utilities without searching the system. Tools include About Windows, Change UAC Settings, Windows Troubleshooting, Computer Management, System Properties, Event Viewer, Services, Task Scheduler, Device Manager, Disk Management, System Information, Registry Editor, and others.

## Advanced System Settings: Performance and Recovery

Access through Settings → System → About → Advanced system settings or search "View advanced system settings."

**Performance Settings** (Advanced tab → Settings button)
Controls how Windows allocates processor resources and manages visual effects. The Advanced tab within Performance Options shows page file configuration—Windows uses this as virtual memory when physical RAM becomes full, preventing crashes. Default is 1048 MB but can be adjusted. Includes settings for visual effects, processor scheduling, and memory usage optimization.

**Virtual Memory (Page File)**
Windows creates a page file as extra virtual memory space on the hard disk. This file appears as an area on the hard disk that Windows uses as if it were RAM. You can view or modify the page file by going to Advanced → Settings under Performance, then clicking the Advanced tab and changing the page file size.

**Startup and Recovery**
Accessible through Advanced → Settings under the Startup and Recovery section. Windows can create a crash dump file when it encounters a critical error (Blue Screen of Death). This crash dump helps administrators understand what went wrong. The "Write debugging information" dropdown shows the type of crash dump: Automatic memory dump (system manages size), Kernel memory dump (smaller), Small memory dump (256 KB), Complete memory dump (all memory), or None.

## User Account Control (UAC): Four Security Levels

UAC prevents potentially harmful programs from making unauthorized changes. Access via searching "Change UAC Settings" in Windows.

**Always Notify** (Highest Security)
Windows notifies you whenever any app or you try to make system changes. Desktop dims (Secure Desktop activated). Most restrictive but safest.

**Notify for Apps Only** (Default)
Windows only notifies when apps attempt changes, not when you manually change Windows settings. Screen doesn't dim. Balances security with usability.

**Notify Without Dimming**
Same as above but the screen doesn't dim, making it less obvious a security prompt appeared. Lower visibility but same protection level.

**Never Notify** (No Protection)
UAC is disabled. No warnings about any changes. Most dangerous and not recommended. You can change this slider to adjust your security level.

**Command to access:** UserAccountControlSettings.exe (the .exe filename, not the full path)

## Computer Management (compgmt.msc): Three Main Divisions

Access from MSConfig Tools tab or search "Computer Management." This comprehensive utility has three primary sections:

### System Tools

Contains Task Scheduler, Event Viewer, Shared Folders, Local Users and Groups, Performance Monitor, and Device Manager.

**Task Scheduler**
View and manage scheduled tasks that run automatically. Tasks can execute applications, scripts, or commands at any point. Tasks can run at logon, logoff, or on specific schedules. Click "Task Scheduler Library" to see all scheduled tasks. Each task shows details like status, next run time, and last run time. For example, "SystemInfoDailyLog" runs every day at 10:00 AM. Some tasks run once at a specific time (e.g., "At 2:50 PM on 6/15/2025"). Create basic tasks via "Create Basic Task" under Actions (right pane).

**Event Viewer**
Allows viewing events that occurred on the computer, serving as an audit trail showing system activity. Has three panes: left pane shows hierarchical event log providers, middle pane shows overview and summary of events, right pane shows actions. Windows supports five event types: Error (significant problems like service failures), Warning (not necessarily significant but may indicate future problems), Information (successful operations like driver loading), Success Audit (successful authorized access attempts), and Failure Audit (failed unauthorized access attempts). Standard logs: Application (logged by applications), Security (logon attempts and resource access), System (system component failures), and Custom Log (user-created for specific purposes).

**Shared Folders, Sessions, and Open Files**
Shared Folders shows complete list of shares and folders others can connect to. Default Windows shares include ADMIN$ (Remote Admin), C$ (Default share), IPC$ (Remote IPC). Right-click folders to view permissions. Sessions lists currently connected users (VM shows none in this case). Open Files lists what connected users are accessing.

**Performance Monitor (perfmon)**
Displays real-time performance data for troubleshooting. Useful for both local and remote systems. Shows CPU, memory, disk, and network performance.

**Device Manager**
Allows viewing and configuring hardware. Can disable hardware or update drivers.

### 
Storage

Contains Windows Server Backup and Disk Management.

**Disk Management**
System utility for advanced storage tasks: set up new drives, extend partitions, shrink partitions, assign or change drive letters.

### 
Services and Applications

Contains Services and WMI Control.

**Services**
View all background services and their configurations. Each service shows display name (what you see), startup type (Automatic, Manual, Disabled), and status (Running, Stopped). Right-click and select Properties to see more details: service name (differs from display name), path to executable, startup type options, and service status controls (Start, Stop, Pause, Resume buttons). Startup type determines when and how the service starts: Automatic (boots with system), Manual (starts when triggered), or Disabled (won't run). You can specify start parameters when starting the service. For example, VMware SVGA Helper Service is set to Automatic and is currently Running.

**WMI Control (Windows Management Instrumentation)**
Configures and controls Windows Management Instrumentation service. WMI allows scripting languages like VBScript or PowerShell to manage Microsoft Windows computers and servers both locally and remotely. Windows provides WMIC (Windows Management Instrumentation Command-line) as a command-line interface to WMI. Note: WMIC is deprecated in Windows 10 version 21H1; PowerShell now supersedes this tool.

## System Information (msinfo32): Three Information Categories

Access via MSConfig Tools or search "System Information." This utility gathers comprehensive information about your computer.


**System Summary**
General technical specifications: processor brand/model, OS details, system name, Windows license owner.

**Hardware Resources**
Shows information about installed hardware devices. Not for average users but useful for advanced troubleshooting. Includes subsections like Conflicts/Sharing, DMA, Forced Hardware, I/O, IRQs, Memory.

**Components**
Lists specific hardware device information installed on the computer. Many subsections show data (like Display and Input), while some may be empty. Includes Multimedia (Audio Codecs, Video Codecs, CD-ROM, Sound Device), Input (Keyboard, Pointing Device), Modem, Network (Adapter, Protocol, WinSock), Ports, Storage (Drives, Disks, SCSI, IDE), Printing, Problem Devices, and USB.

**Software Environment**
Shows information about software built into the OS and installed software. Includes System Drivers, Environment Variables, Print Jobs, Network Connections, Running Tasks, Loaded Modules, Services, Program Groups, Startup Programs, OLE Registration, and Windows Error Reporting.

**Environment Variables**
Store information about the operating system environment. Examples: WINDIR (Windows installation directory path), NUMBER\_OF\_PROCESSORS, OS (Windows\_NT), PATH, PATHEXT, PROCESSOR\_ARCHITECTURE. View them in msinfo32 by selecting Environment Variables, or through Control Panel → System and Security → System → Advanced system settings → Environment Variables, or Settings → System → About → Advanced system settings → Environment Variables. These variables store data used by the OS and other programs.

**Search Feature**
At the bottom of msinfo32 is a search bar. Select Components and search for "IP address" to find network configuration details, for example.

## Resource Monitor (resmon): Real-Time Performance Analysis

Access via MSConfig Tools or type resmon directly. Microsoft describes it as: "Resource Monitor displays per-process and aggregate CPU, memory, disk, and network usage information. Advanced filtering allows users to isolate data related to one or more processes, start, stop, pause, and resume services, close unresponsive applications, and see process analysis to help identify deadlocked processes and file-locking conflicts."

Primarily for advanced troubleshooting on computer systems.

**Overview Tab (Four Main Sections)**
CPU, Disk, Network, and Memory. Each shows running processes and their resource usage.

**Dedicated Tabs**
Same four sections (CPU, Memory, Disk, Network) have corresponding tabs across the top with additional detail for each resource type. Each tab shows process details with specific performance metrics.

**Graphical Real-Time Monitoring**
A pane on the far right displays graphical views in real-time for each section.


**Note:** Resource Monitor data will differ between systems compared to the images shown.

**Command:** resmon.exe (the .exe filename, not the full path)

## Command Prompt (cmd): Essential Windows Terminal

The command prompt allows you to interact directly with Windows through text commands rather than the graphical interface. While the GUI is primary for most tasks, the command prompt remains powerful for troubleshooting and system administration.


### Basic Commands


**hostname**
Outputs the computer name. Example: THM-WINFUN2

**whoami**
Outputs the name of the logged-in user. Example: THM-WINFUN2\\administrator

**ipconfig**
Shows network address settings for the computer. Displays Connection-specific DNS Suffix, Link-local IPv6 Address, IPv4 Address, Subnet Mask, and Default Gateway. Essential for network troubleshooting.

**ipconfig /?** or **ipconfig /all**
Shows full network configuration information with all available options and parameters.

**cls**
Clears the command prompt screen.

**netstat**
Displays protocol statistics and current TCP/IP network connections. Shows Active Connections with Proto, Local Address, Foreign Address, and State. Can be run alone or with parameters like -a (all), -b, -e, -f, -n, -o, -p, -r, -s, -x, -t, or [interval]. The /? help flag doesn't work for netstat; use different syntax.

**Example syntax:** netstat -a (displays all connections and listening ports)

**net**
Manages network resources with multiple sub-commands. Sub-commands include: ACCOUNTS, COMPUTER, CONFIG, CONTINUE, FILE, GROUP, HELPMSG, LOCALGROUP, PAUSE, SESSION, SHARE, START, STATISTICS, STOP, TIME, USE, USER, VIEW. To access help for net command: use "net help" not "/?". Example: "net help user" shows syntax for the USER sub-command.

**net user** Creates and modifies user accounts. When used alone or with certain parameters, lists user accounts and their properties.

**net localgroup, net use, net share, net session**
Other useful net sub-commands for network administration.

**Note:** /? doesn't work for net; use "net help" instead.


### Command Help System

Type /? after most commands to see help manual with expected syntax and parameters. Example: ipconfig /? shows all ipconfig parameters and usage.

## Windows Registry (regedit32.exe): The System Database

**What it is:** The Windows Registry is a central hierarchical database storing all information necessary to configure the system for one or more users, applications, and hardware devices.


**Critical Information Stored:**

- User profiles for each account
- Installed applications and document file types they can create
- Property sheet settings for folders and application icons
- Existing hardware on the system
- Ports currently being used


**Warning:** The registry is for advanced users. Making unauthorized changes affects normal computer operations.


**Access Method:** Registry Editor (regedit). Search "regedit" or launch from MSConfig Tools.


**Structure:** Hierarchical layout with root keys like HKEY\_CLASSES\_ROOT, HKEY\_CURRENT\_USER, HKEY\_LOCAL\_MACHINE visible in the left pane. Each key contains subkeys with Name, Type, and Data columns in the right pane.

## Key Practical Takeaways for Labs and Exams

1. **MSConfig is the Gateway:** MSConfig provides access to nearly all tools discussed. Master navigating its five tabs and Tools section. When asked to find a system utility, think MSConfig first.
2. **Service Management:** Understanding startup types (Automatic, Manual, Disabled) is crucial. Services run in the background and can be managed through Computer Management or MSConfig.
3. **Network Troubleshooting:** ipconfig, netstat, and net commands are your primary CLI tools. Know the difference between ipconfig /all (full details) and basic ipconfig.
4. **System Information:** msinfo32 provides comprehensive system details. Know its three categories and how to search within it.
5. **Resource Monitoring:** resmon shows real-time CPU, disk, memory, and network usage. Perfect for identifying performance bottlenecks and problematic processes.
6. **UAC Security Levels:** Four levels from "Always Notify" (most secure) to "Never Notify" (none). Default is "Notify for apps."
7. **Event Viewer for Auditing:** Five event types (Error, Warning, Information, Success Audit, Failure Audit) create an audit trail. Security log captures logon attempts and resource access.
8. **Registry Caution:** The registry is powerful and dangerous. Changes can break the system. Only advanced users should modify it directly.
9. **Task Scheduler:** Automated task execution at specified times or triggers. Can run applications, scripts, or commands.
10. **Red Team Perspective:** System tools reveal valuable information about system configuration, services, users, and network settings. Lateral movement and persistence often involve manipulating scheduled tasks, services, startup items, and registry entries. Understanding these tools helps identify what an attacker might target.

## Critical Reminder for Exams and Labs

**All core system tools are accessible from MSConfig.** This is not just a convenience—it's the architectural foundation. Your entry point to system administration is MSConfig. Whether you need Computer Management, System Information, Registry Editor, or any other utility, remember that MSConfig serves as your central control panel and the base point for everything.

***

# Active Directory Basics - Revision Notes

## Overview: What is Active Directory?

Active Directory (AD) is the backbone of corporate network management in Windows environments. It's a centralized repository that simplifies management of devices and users within a corporate environment. Instead of manually configuring each computer separately, AD allows administrators to manage hundreds or thousands of computers and users from a single location through Domain Controllers.

The key insight: **A Windows domain is a group of users and computers under the administration of a given business**, with Active Directory (AD) as the central repository and a Domain Controller (DC) as the server running AD services.

## Windows Domains: Scaling Management

**The Problem**: Small networks with 5 computers are manageable. Large networks with 157 computers across 4 offices are not—you can't manually log into each machine or configure individual policies for every user.


**The Solution**: A Windows domain provides centralized administration. The Domain Controller runs Active Directory services and stores all network objects. When a user logs into any machine on the domain, the machine forwards authentication to the Domain Controller, which verifies credentials against the central repository. Users no longer need accounts on each individual machine.


**Key advantages of domains**:

- **Centralized identity management**: All users configured once in AD, accessible everywhere on the network
- **Centralized security policies**: Policies deployed domain-wide to all computers and users simultaneously
- **Simplified support**: Credentials valid across all machines; policies prevent users from accessing restricted areas


Real-world example: In universities, you receive one username/password valid on all campus computers. The university's AD validates your credentials whenever you log in. Likewise, policies from AD prevent you from accessing the control panel on university machines.

## Active Directory Core Components

**Active Directory Domain Service (AD DS)** is the backbone. It acts as a catalogue holding information about all "objects" on the network: users, groups, machines, printers, shares, and more.


## Security Principals and Resources

AD objects fall into two categories:


**Security Principals**: Objects that can be authenticated by the domain and assigned privileges. These include:

- **Users**: Represent entities that can be authenticated (people or services) and assigned privileges over resources (files, printers)
    - *People users*: Employees needing network access
    - *Service users*: Service accounts for IIS, MSSQL, etc.—these have only the privileges needed for their specific service
- **Machines**: Every computer joining the domain gets a machine account (a security principal). Machine accounts are local administrators on their assigned computers and have automatically rotated passwords (120 random characters, rotated regularly)
    - *Machine account naming*: Follow a pattern with a $ suffix (e.g., `DC01$` for a machine named `DC01`)
- **Groups**: Collections of users and/or machines. Groups can be nested (contain other groups)


**Resources**: The things that need access control—files, printers, shared folders, network services.

## Security Groups

Default built-in groups grant specific privileges:

- **Domain Admins**: Administrative privileges over entire domain; can administer all DCs
- **Server Operators**: Can administer Domain Controllers but can't change administrative groups
- **Backup Operators**: Can access any file ignoring permissions; used for backups
- **Account Operators**: Can create or modify other domain accounts
- **Domain Users**: Contains all existing user accounts
- **Domain Computers**: Contains all existing computers
- **Domain Controllers**: Contains all existing DCs


## Organizational Units (OUs)

**OUs are container objects that classify users and machines**. The main purpose is to apply Group Policies to specific user/computer sets. Typical structure: root OU (e.g., THM) with child OUs for departments (IT, Management, Marketing, R&D, Sales).

**Key distinction — OUs vs Security Groups**:

- **OUs are for policies**: Apply configurations to entire departments; can only apply one policy set per OU
- **Security Groups are for permissions**: Grant access to resources; multiple groups can have access to the same resource


## Default Containers

Created automatically by Windows:

- **Builtin**: Default groups available to any Windows host
- **Computers**: Default location for domain-joined machines
- **Domain Controllers**: Contains all DCs
- **Users**: Default users and groups (domain-wide context)
- **Managed Service Accounts**: Accounts used by services in the domain

## 
Management Tool

**Active Directory Users and Computers** is the standard GUI tool (found in start menu). It shows the hierarchy of users, computers, groups, and OUs, allowing creation, deletion, and configuration of objects.

## Managing Users and Computers in AD

### User Management

Administrators configure users centrally and apply policies through OUs. Users are created in appropriate organizational units matching the company structure.

### Computer Management

By default, machines joining a domain are placed in a "Computers" container. However, best practice is to organize them by function (servers separate from workstations, development machines separate from production). Machines should be organized into at least three categories:

- **Servers**: Application, file, and database servers requiring specific policies
- **Workstations**: User machines with different policies than servers
- **Special purpose machines**: Labs, testing environments, etc.

## Group Policy Objects (GPOs)

**Group Policy Objects (GPOs)** are collections of settings that can be applied to OUs, containing policies for specific machines and identities. This is how administrators deploy configurations uniformly across the organization.

**Tool**: Group Policy Management (available from start menu). GPOs can set baselines on specific machines and identities, allowing different departments to have different configurations while maintaining consistency within each department.

**Red Team Relevance**: Misconfigurations in GPOs are common attack vectors. Domain Admins can modify GPOs, making them high-value targets during penetration tests.

## Authentication Methods

When users try to authenticate to services using domain credentials, the DC must verify them. Two protocols are available:

### Kerberos (Modern Standard)

**Default protocol for any recent Windows version.** Kerberos authentication works with tickets—cryptographic proof of previous authentication.


**How Kerberos works**:

1. User sends username and timestamp encrypted with a key derived from their password to the **Key Distribution Center (KDC)**, a service on the Domain Controller
2. KDC verifies credentials and sends back a **Ticket Granting Ticket (TGT)**, allowing the user to request service tickets
3. User presents the TGT to access services, receiving **Service Tickets** for specific resources
4. User presents the Service Ticket to the service to authenticate


**Key concept**: Tickets are proof of prior authentication, reducing the need to send credentials repeatedly.


### NetNLM (Legacy)

Legacy authentication protocol kept for compatibility. Should be considered obsolete in modern networks but is often still enabled.


**Red Team Relevance**: NetNLM is vulnerable to pass-the-hash attacks. Kerberos is more secure when properly configured but can be exploited if misconfigured (e.g., weak passwords, unconstrained delegation).

## Trees, Forests, and Trusts: Enterprise-Scale AD

As companies grow beyond a single domain, AD supports hierarchical structures.

### Trees

**A tree is a group of Windows domains sharing the same namespace.** Example: `thm.local` can have subdomains `uk.thm.local` and `us.thm.local`, each with its own Domain Controller.

**Benefits**:

- Partition network into independently managed units
- Each branch IT team manages only their DC
- Each domain has its own Domain Admins (controlling only that domain)
- Policies configured independently per domain
- A new group **Enterprise Admins** grants administrative privileges over all domains in the tree

**Why this matters**: Allows IT compartmentalization—UK IT manages UK resources without interfering with US resources, reducing blast radius of misconfigurations and limiting privilege escalation.

### 
Forests

**A forest is the union of multiple trees with different namespaces.** When companies merge, they often have different domain trees (e.g., `thm.local` and `mht.local`), which can be joined into a forest.

**Example**: THM Inc. (with `thm.local` tree) merges with MHT Inc. (with `mht.local` tree). The union forms a forest allowing both organizations' resources to coexist and be accessed across company boundaries when needed.

### 
Trust Relationships

**Trust relationships allow users in one domain to access resources in another.** Without trust, domains are isolated.

**One-way trusts**: If Domain A trusts Domain B, users from B can access resources on A (note: trust direction is opposite to access direction).

**Two-way trusts**: Mutual authorization—both domains trust each other. Automatically established when domains join a tree or forest.

**Critical point**: Trust relationships allow *authorization* but don't grant automatic access. After establishing trust, you explicitly configure which users/groups have access to which resources.

**Red Team Relevance**: Trust relationships are pivot points. Compromising a domain in a forest can allow lateral movement to other domains through the trust relationship.

## Key Facts to Remember

- **AD = Catalogue**: Holds all network objects (users, computers, groups, printers, etc.)
- **Domain Controller (DC)**: Server running Active Directory services
- **Security Principal**: Object that can be authenticated and assigned privileges (users, machines, groups)
- **OUs**: For applying policies; one policy set per OU
- **Security Groups**: For granting access to resources; multiple groups can grant access
- **Kerberos**: Default modern authentication protocol; uses tickets
- **NetNLM**: Legacy protocol; vulnerable to pass-the-hash
- **Tree**: Multiple domains in same namespace (e.g., `uk.thm.local` under `thm.local`)
- **Forest**: Multiple trees with different namespaces (e.g., `thm.local` and `mht.local`)
- **Trust Relationship**: Allows cross-domain resource access; doesn't grant automatic permissions
- **Enterprise Admins**: Administrative privileges across all domains in a forest
- **Machine Account**: Named with $ suffix; auto-rotates 120-character password

## Penetration Testing / Red Team Applications

1. **Domain Admins are high-value targets**: They control everything. Compromising a DA account gives full domain control.
2. **Kerberos attacks**: Exploit weak passwords (brute-force), unconstrained delegation, pass-the-ticket
3. **Trust relationships are pivot points**: Compromising one domain domain can lead to access in others
4. **Group Policy**: Misconfigurations (e.g., storing credentials in GPO) are common vulnerabilities
5. **Machine accounts**: Often have predictable passwords; can be leveraged for lateral movement
6. **Service accounts**: May have excessive privileges; compromising them grants those privileges
7. **Organizational structure reconnaissance**: Understanding OU structure helps identify high-value targets
8. **NetNLM fallback**: If Kerberos fails, systems may fall back to NetNLM, which is vulnerable to pass-the-hash
