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

Important Notice - External Reputation APIs:

<br>

This repository optionally integrates with third-party reputation services to enrich collected artifacts with contextual intelligence.

<br>

VirusTotal API:

<br>

(1) Supports both VirusTotal Public API and VirusTotal Premium API.

(2) Public API limitations include:

<br>

4 requests per minute.

500 requests per day.

Personal, non-commercial use only.

<br>

(3) Public API keys must not be used in commercial products or business workflows.

(4) Abuse of rate limits, account sharing, or automated bulk lookups may violate VirusTotal Terms of Service.

(5) Reputation lookups are optional; the tool remains fully functional without an API key.

<br>

AbuseIPDB API:

<br>

(1) Used to retrieve reputation information for public IP addresses observed in active outbound connections.

(2) API limits depend on the selected plan (Free, Basic, or Premium).

(3) Free and lower-tier plans enforce daily request caps and reduced feature sets.

(4) Excessive querying may result in rate limiting or temporary API blocking.

(5) IP reputation lookups are optional; network inspection remains available without an API key.

<br>

Usage Responsibility:

<br>

(1) Before using the application, users must explicitly acknowledge and accept responsibility via mandatory notice dialogs displayed on first launch.

(2) By accepting these notices, users confirm that they understand applicable API limits and licensing terms, are authorized to use the provided API keys, and accept full responsibility for how the tool and integrated services are used.

(3) Users are responsible for ensuring their API key usage complies with the current Terms of Service of VirusTotal and AbuseIPDB.

(4) API terms, limits, and licensing conditions may change over time.

(5) Users are strongly advised to review official documentation before use:

https://docs.virustotal.com/reference/public-vs-premium-api

https://docs.abuseipdb.com/#api-daily-rate-limits

<br>

-----------------------------------------
ActiveEndpointHash&IPInspector.exe
-----------------------------------------

This is the main application and unified entry point for the entire toolkit.

<br>

It provides a single tabbed interface that hosts all five inspection modules, allowing analysts to switch between endpoint perspectives without launching multiple executables.

<br>

Core characteristics:

<br>

(1) Unified Windows GUI hosting all modules as tabs.

(2) Centralized startup notices and API usage disclosures.

(3) Optional session-only API key handling for:

<br>

VirusTotal (file hash reputation).

AbuseIPDB (public IP reputation).

<br>

(4) Consistent scan controls and workflow across all modules.

(5) Designed for baseline creation followed by delta-based threat hunting.

<br>

The application itself does not perform scanning logic; it orchestrates and embeds the individual modules described below.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Module summary:

-----------------------------------------
ActiveSHA256HashChecker.exe
-----------------------------------------

Enumerates currently running processes and:

<br>

(1) Extracts executable paths of active processes.

(2) Computes SHA-256 hashes of in-memory binaries.

(3) Optionally queries VirusTotal for file reputation.

(4) Appends only newly observed processes between scans to support baseline comparison.

-----------------------------------------
ActivePublicIPChecker.exe
-----------------------------------------

Enumerates active TCP connections and:

<br>

(1) Filters connections to public remote IP addresses only.

(2) Correlates each connection with the owning process.

(3) Optionally queries AbuseIPDB for IP reputation.

(4) Supports detection of newly established outbound connections.

-----------------------------------------
ServiceHashChecker.exe
-----------------------------------------

Enumerates Windows services and:

<br>

(1) Extracts service executables and command lines.

(2) Computes SHA-256 hashes of service binaries.

(3) Identifies service startup type and current state.

(4) Optionally enriches hashes with VirusTotal reputation.

(5) Appends only newly created services to support baseline comparison and persistence detection.

-----------------------------------------
ScheduledTaskHashChecker.exe
-----------------------------------------

Enumerates Windows Scheduled Tasks and:

<br>

(1) Expands tasks into individual executable actions.

(2) Collects trigger information (boot, logon, scheduled, event-based).

(3) Computes SHA-256 hashes of task action binaries.

(4) Optionally queries VirusTotal for reputation data.

(5) Appends only newly detected scheduled tasks to enable reliable baseline comparison and detection of newly introduced persistence mechanisms.

-----------------------------------------
AutostartHashChecker.exe
-----------------------------------------

Aggregates common Windows persistence mechanisms, including:

<br>
<br>

(1) Windows Services:

<br>

Services configured with Automatic and Automatic (Delayed Start) startup types.

Service ImagePath and associated command-line parameters.

<br>

(2) Registry-based autostart entries (Run and RunOnce keys under):

<br>

HKLM\Software\Microsoft\Windows\CurrentVersion\Run

HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKCU\Software\Microsoft\Windows\CurrentVersion\Run

HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

<br>

(3) Startup folders:

<br>

Per-user Startup folder (shell:startup).

System-wide Startup folder (shell:common startup).

<br>

(4) Scheduled Tasks:

<br>

Tasks triggered at system startup.

Tasks triggered at user logon.

Tasks configured for persistence through time-based or event-based triggers.

<br>

(5) WMI event subscription persistence:

<br>

Permanent event subscriptions (Event Filters, Consumers, and Bindings).

Execution of commands or scripts in response to system or user events.

<br>
<br>

For each entry, the module:

<br>

(1) Resolves the executed binary or script.

(2) Computes a SHA-256 hash where applicable.

(3) Optionally enriches results with VirusTotal reputation.

(4) Appends and evaluates only newly introduced autostart entries to enable accurate baseline comparison and detection of persistence added after initial execution.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Operational model:

<br>

All modules follow a consistent workflow:

<br>

(1) Start New Scan:

<br>

Performs initial enumeration and establishes a baseline on first execution.

On subsequent executions, extends the existing baseline by appending only newly observed artifacts.

<br>

(2) Cancel:

<br>

Safely pauses long-running enumeration, hashing, or reputation lookups.

Preserves all collected data and scan progress without data loss.

<br>

(3) Continue Scanning:

<br>

Resumes the scan from the exact point where it was paused.

Continues hashing and reputation enrichment without restarting completed steps.

<br>

(4) Skip Mode:

<br>

Stops processing already observed artifacts.

Switches scanning logic to newly introduced artifacts only.

Ensures reliable baseline comparison and clean detection of changes between executions.

<br>

(5) Save Results:

<br>

Exports the current view to a CSV file.

Preserves all collected metadata, hashes, and reputation results for reporting and analysis.

<br>

Note: No destructive actions are performed by any module.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Intended use:

<br>

This repository is intended for:

<br>

(1) SOC laboratories and training environments.

(2) Blue Team operations in both test and production environments.

(3) Threat hunting and detection engineering activities.

(4) Malware analysis and post-execution inspection in controlled scenarios.

(5) DFIR investigations and incident response triage (post-compromise endpoint inspection).

(6) Production endpoints for baseline creation and continuous inspection, provided no malicious code is intentionally executed.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
