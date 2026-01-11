-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Author: Michal Soltysik

Cybersecurity Analyst & Consultant | Digital Forensics Examiner | SOC Analyst | Blue Team Specialist

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

This repository contains a Windows endpoint inspection suite designed to operate across endpoints running Windows operating systems.  
The suite focuses on live endpoint visibility, persistence discovery, and rapid triage by combining local artifact collection with optional reputation enrichment.

<br>

Modules included:

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

Each module runs as a dedicated tab inside a single unified application:

Active Endpoint Hash & IP Inspector - 5-Module Suite

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

General purpose:

The suite is designed to support:

- Endpoint baseline creation
- Threat hunting and anomaly detection
- Malware analysis and post-execution inspection
- Blue Team operations in SOC labs and controlled environments
- Rapid comparison of endpoint state before and after suspicious activity

The tools are intentionally read-focused and do not perform remediation, deletion, or system modification beyond optional API-based reputation lookups.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

API usage notice:

Some modules optionally integrate with external reputation services:

- VirusTotal (file hash reputation)
- AbuseIPDB (public IP address reputation)

API keys are optional and are only stored as environment variables for the current execution session.

If API keys are not provided:
- All local collection, hashing, and enumeration features remain fully functional.
- Reputation columns will display clear error or unavailable states.

Users must comply with the respective Terms of Service and licensing conditions of VirusTotal and AbuseIPDB.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Module summary:

-----------------------------------------
Active SHA-256 Hash Checker
-----------------------------------------

Enumerates currently running processes and:

- Extracts executable paths
- Computes SHA-256 hashes of active binaries
- Optionally queries VirusTotal for file reputation
- Appends only new processes between scans to support baseline comparison

-----------------------------------------
Active Public IP Checker
-----------------------------------------

Enumerates active TCP connections and:

- Filters to public remote IP addresses only
- Correlates connections with owning processes (PID, name, path)
- Optionally queries AbuseIPDB for IP reputation
- Supports detection of newly established outbound connections

-----------------------------------------
Service Hash Checker
-----------------------------------------

Enumerates Windows services and:

- Extracts service executables and command lines
- Computes SHA-256 hashes of service binaries
- Identifies startup type and service state
- Optionally enriches hashes with VirusTotal reputation
- Supports detection of newly added or modified services

-----------------------------------------
Scheduled Task Hash Checker
-----------------------------------------

Enumerates Windows Scheduled Tasks and:

- Expands tasks into individual actions
- Extracts executables and command lines per action
- Collects trigger information (boot, logon, time-based, etc.)
- Computes SHA-256 hashes of task binaries
- Optionally queries VirusTotal
- Designed for scheduled-task persistence analysis

-----------------------------------------
Autostart Hash Checker
-----------------------------------------

Aggregates common Windows autostart persistence locations, including:

- Services (automatic and delayed start)
- Registry Run and RunOnce keys (HKLM and HKCU)
- Startup folders (user and machine)
- Scheduled tasks with startup or logon triggers
- WMI event subscription persistence

For each entry, the tool:
- Resolves the executed binary
- Computes a SHA-256 hash
- Optionally enriches the result with VirusTotal reputation

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Operational model:

All modules follow a consistent workflow:

- Start New Scan builds or extends the current baseline
- Cancel pauses long-running scans safely
- Continue Scanning resumes from the exact previous position
- Skip mode switches scanning to new artifacts only
- Save Results exports findings to CSV for reporting or further analysis

No destructive actions are performed.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Intended use:

This repository is intended for:

- SOC laboratories
- Blue Team operations
- DFIR investigations
- Malware research
- Detection engineering
- Controlled and authorized test environments

Use in production environments is possible for inspection and visibility purposes, but all execution must comply with organizational security policies and applicable laws.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


say which version and I will refine it precisely.
