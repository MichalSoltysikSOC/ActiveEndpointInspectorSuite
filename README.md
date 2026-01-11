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

(1) Unified Windows GUI hosting all modules as tabs
(2) Centralized startup notices and API usage disclosures
(3) Optional session-only API key handling for:
\- VirusTotal (file hash reputation)
\- AbuseIPDB (public IP reputation)
(4) Consistent scan controls and workflow across all modules
(5) Designed for baseline creation followed by delta-based threat hunting

The application itself does not perform scanning logic; it orchestrates and embeds the individual modules described below.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Module summary:

-----------------------------------------
Active SHA-256 Hash Checker
-----------------------------------------

Enumerates currently running processes and:

(1) Extracts executable paths of active processes
(2) Computes SHA-256 hashes of in-memory binaries
(3) Optionally queries VirusTotal for file reputation
(4) Appends only newly observed processes between scans to support baseline comparison

-----------------------------------------
Active Public IP Checker
-----------------------------------------

Enumerates active TCP connections and:

(1) Filters connections to public remote IP addresses only
(2) Correlates each connection with the owning process
(3) Optionally queries AbuseIPDB for IP reputation
(4) Supports detection of newly established outbound connections

-----------------------------------------
Service Hash Checker
-----------------------------------------

Enumerates Windows services and:

(1) Extracts service executables and command lines
(2) Computes SHA-256 hashes of service binaries
(3) Identifies service startup type and current state
(4) Optionally enriches hashes with VirusTotal reputation

-----------------------------------------
Scheduled Task Hash Checker
-----------------------------------------

Enumerates Windows Scheduled Tasks and:

(1) Expands tasks into individual executable actions
(2) Collects trigger information (boot, logon, scheduled, event-based)
(3) Computes SHA-256 hashes of task action binaries
(4) Optionally queries VirusTotal for reputation data

-----------------------------------------
Autostart Hash Checker
-----------------------------------------

Aggregates common Windows persistence mechanisms, including:

(1) Services (Automatic and Delayed Start)
(2) Registry Run and RunOnce keys (HKLM and HKCU)
(3) Startup folders (User and Machine)
(4) Scheduled tasks with startup or logon triggers
(5) WMI event subscription persistence

For each entry, the module:

(1) Resolves the executed binary or script
(2) Computes a SHA-256 hash where applicable
(3) Optionally enriches results with VirusTotal reputation

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Operational model:

All modules follow a consistent workflow:

(1) Start New Scan builds or extends the current baseline
(2) Cancel safely pauses long-running scans
(3) Continue Scanning resumes from the exact previous position
(4) Skip mode switches scanning to newly introduced artifacts only
(5) Save Results exports findings to CSV for reporting and analysis

No destructive actions are performed by any module.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Intended use:

This repository is intended for:

(1) SOC laboratories
(2) Blue Team operations
(3) Threat hunting and detection engineering
(4) Malware analysis and post-execution inspection
(5) DFIR investigations
(6) Controlled and authorized environments

Execution must always comply with organizational security policies and applicable laws.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
