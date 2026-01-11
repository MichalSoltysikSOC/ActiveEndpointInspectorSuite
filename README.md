-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Author: Michal Soltysik

Cybersecurity Analyst & Consultant | Forensics Examiner | SOC Trainer | Cyber Warfare Organizer

Official website: https://michalsoltysik.com/

LinkedIn: https://www.linkedin.com/in/michal-soltysik-ssh-soc/

Cybersecurity content: https://www.youtube.com/playlist?list=PL0RdRWQWldOAAKBqOVEutxKMP-a6CNoLY

Accredible: https://www.credential.net/profile/michalsoltysik/wallet

Credly: https://www.credly.com/users/michal-soltysik

Email: me@michalsoltysik.com

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Written in C# and compiled against the .NET Framework 4.x.

All tools are compiled into standalone .exe executable files with a standard MZ file header and run natively on Windows systems.

License: Free for personal and commercial use.

<br>

Repository overview:

This repository contains a Windows endpoint inspection suite designed for endpoints running Windows operating systems.  
The suite combines live endpoint visibility, persistence discovery, and network context with optional reputation enrichment.

<br>

Tools included:

(0) Active Endpoint Hash & IP Inspector - 5-Module Suite
<br>
(1) Active SHA-256 Hash Checker  
<br>
(2) Active Public IP Checker  
<br>
(3) Service Hash Checker  
<br>
(4) Scheduled Task Hash Checker  
<br>
(5) Autostart Hash Checker  

<br>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Summary:

-----------------------------------------
Active Endpoint Hash & IP Inspector
-----------------------------------------

This is the main application and unified entry point for the entire toolkit.

It provides a single tabbed interface that hosts all five inspection modules, allowing analysts to switch between endpoint perspectives without launching multiple executables.

Core characteristics:

- Unified Windows GUI hosting all modules as tabs
- Centralized startup notices and API usage disclosures
- Optional session-only API key handling for:
  - VirusTotal (file hash reputation)
  - AbuseIPDB (public IP reputation)
- Consistent scan controls and workflow across all modules
- Designed for baseline creation followed by delta-based threat hunting

The application itself does not perform scanning logic; it orchestrates and embeds the individual modules described below.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Module summary:

-----------------------------------------
Active SHA-256 Hash Checker
-----------------------------------------

Enumerates currently running processes and:

- Extracts executable paths of active processes
- Computes SHA-256 hashes of in-memory binaries
- Optionally queries VirusTotal for file reputation
- Appends only newly observed processes between scans to support baseline comparison

-----------------------------------------
Active Public IP Checker
-----------------------------------------

Enumerates active TCP connections and:

- Filters connections to public remote IP addresses only
- Correlates each connection with the owning process
- Optionally queries AbuseIPDB for IP reputation
- Supports detection of newly established outbound connections

-----------------------------------------
Service Hash Checker
-----------------------------------------

Enumerates Windows services and:

- Extracts service executables and command lines
- Computes SHA-256 hashes of service binaries
- Identifies service startup type and current state
- Optionally enriches hashes with VirusTotal reputation

-----------------------------------------
Scheduled Task Hash Checker
-----------------------------------------

Enumerates Windows Scheduled Tasks and:

- Expands tasks into individual executable actions
- Collects trigger information (boot, logon, scheduled, event-based)
- Computes SHA-256 hashes of task action binaries
- Optionally queries VirusTotal for reputation data

-----------------------------------------
Autostart Hash Checker
-----------------------------------------

Aggregates common Windows persistence mechanisms, including:

- Services (Automatic and Delayed Start)
- Registry Run and RunOnce keys (HKLM and HKCU)
- Startup folders (User and Machine)
- Scheduled tasks with startup or logon triggers
- WMI event subscription persistence

For each entry, the module:

- Resolves the executed binary or script
- Computes a SHA-256 hash where applicable
- Optionally enriches results with VirusTotal reputation

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Operational model:

All modules follow a consistent workflow:

- Start New Scan builds or extends the current baseline
- Cancel safely pauses long-running scans
- Continue Scanning resumes from the exact previous position
- Skip mode switches scanning to newly introduced artifacts only
- Save Results exports findings to CSV for reporting and analysis

No destructive actions are performed by any module.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Intended use:

This repository is intended for:

- SOC laboratories
- Blue Team operations
- Threat hunting and detection engineering
- Malware analysis and post-execution inspection
- DFIR investigations
- Controlled and authorized environments

Execution must always comply with organizational security policies and applicable laws.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
* add a **Quick Start** section
* or prepare a **professional disclaimer block** aligned with SOC and DFIR standards
