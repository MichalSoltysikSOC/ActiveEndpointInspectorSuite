Tool name: AutostartHashChecker.exe

Version: 1.0
SHA256 checksum: 091FDF8E18FFD52AE815DA1265DC8571CE3D0701E79EDBA525AEC4794830DBEB
File Size: 57,0 KB
Written in C#, targeting the .NET Framework.
Compiled into a Windows GUI .exe executable file with an MZ file header.

Author: Michał Sołtysik
Cybersecurity Analyst & Consultant | Forensics Examiner | SOC Trainer | Cyber Warfare Organizer
Official website: https://michalsoltysik.com/
LinkedIn: https://www.linkedin.com/in/michal-soltysik-ssh-soc/
Cybersecurity content: https://www.youtube.com/playlist?list=PL0RdRWQWldOAAKBqOVEutxKMP-a6CNoLY
Accredible: https://www.credential.net/profile/michalsoltysik/wallet
Credly: https://www.credly.com/users/michal-soltysik
Email: me@michalsoltysik.com

Purpose: Autostart Hash Checker is a Windows GUI tool designed for persistence triage and threat hunting. It aggregates common Windows autostart mechanisms, resolves the executables behind each entry, computes SHA-256 hashes of those files, and optionally enriches results with VirusTotal detection summaries to support baseline creation and detection of newly introduced persistence.
License: Free for personal and commercial use.

Important notice - VirusTotal API usage:

(1) This tool optionally integrates with the VirusTotal API to enrich autostart-related binaries with reputation information.
(2) On first launch, the application displays a mandatory VirusTotal usage notice describing public vs premium API constraints and acceptable use conditions.
(3) Users must explicitly accept this notice to proceed. Declining acceptance prevents the application from running.
(4) By accepting the notice, users confirm that they understand applicable API limits, licensing restrictions, and accept full responsibility for how the integrated service is used.
(5) Reputation lookups are optional. When no API key is provided, persistence enumeration and hashing remain fully functional and the final reputation column remains unpopulated or displays an authorization error.

Note: Users are responsible for ensuring compliance with the current VirusTotal Terms of Service. API limits and licensing conditions may change over time.

What the application collects:

The tool aggregates multiple persistence sources into a single unified table.

(1) Windows services (auto-start):

- Enumerates services using WMI (Win32_Service).
- Filters to services configured with StartMode set to Automatic.
- Inspects DelayedAutoStart registry values to distinguish:

  * Automatic.
  * Automatic (Delayed Start).

- Extracts executables from service PathName values, supporting quoted and unquoted paths with spaces.
- Records the persistence source location under: HKLM\SYSTEM\CurrentControlSet\Services<ServiceName>

(2) Scheduled tasks with startup or logon triggers:

- Uses PowerShell Get-ScheduledTask to identify tasks triggered at system startup or user logon.
- Extracts the first executable action (Execute plus Arguments).
- Normalizes triggers into:

  * At startup.
  * At log on.
  * At startup / At log on.

- Records task source paths under: C:\Windows\System32\Tasks<TaskPath><TaskName>
- Marks scope as Machine or User, depending on task context.

(3) Registry Run and RunOnce keys:

- Enumerates classic autorun keys:

  * HKLM\Software\Microsoft\Windows\CurrentVersion\Run
  * HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
  * HKCU\Software\Microsoft\Windows\CurrentVersion\Run
  * HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

- Extracts executables from command lines while preserving original entry details.

(4) Startup folders:

- Enumerates:

  * Per-user Startup folder.
  * Common Startup folder (all users).

- Resolves .lnk shortcuts using WScript.Shell to capture target path and arguments.
- Treats non-.lnk files as directly executed targets.

(5) WMI event subscription persistence:

- Queries the root\subscription namespace for:

  * __EventFilter (query definitions and namespaces).
  * CommandLineEventConsumer (executables, command lines, working directories).
  * ActiveScriptEventConsumer (script engine and embedded script text).

- Surfaces fileless or event-driven persistence that does not appear in traditional autorun locations.

Executable resolution and hashing:

(1) Normalizes executable paths:

- Expands environment variables such as %windir% and %SystemRoot%.
- Attempts resolution of non-rooted paths using System32, Windows directories, and PATH search.

(2) Computes SHA-256 hashes for resolved executables:

- Uses FileShare.ReadWrite to hash binaries that may be in use.
- Produces explicit status strings when hashing is not possible, including:

  * ERROR_FILE_NOT_FOUND (WIN32: 2)
  * PATH_NOT_FOUND (WIN32: 3)
  * ACCESS_DENIED (WIN32: 5)
  * ERROR: <ExceptionType>

(3) VirusTotal enrichment (optional):

- Queries VirusTotal v3 by SHA-256 when a VT_API_KEY is provided.
- Displays compact detection summaries such as:

  * No security vendors flagged this file as malicious.
  * X/Y security vendors flagged this file as malicious.
  * VirusTotal error: 401 Unauthorized (missing/invalid API key).
  * Rate limit hit (HTTP 429) - try again later.
  * We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
  * VirusTotal error: <HTTP status> <reason> for other unexpected responses.

- Handles common API outcomes including 401 Unauthorized, 429 rate limit exceeded, and not found.

User interface and displayed data:

The application presents a live-updating table with the following columns:

- Persistence Method
- Source Location
- Startup Trigger
- Scope
- Target Path
- Entry or File Name
- Details (for example: command line)
- Target SHA-256 Hash
- VirusTotal Detection Summary

Operational workflow and controls:

(1) Start New Scan:

- Collects current persistence entries and appends only newly detected rows.
- Does not remove or overwrite previously collected results.

(2) Cancel:

- Immediately pauses the scan while preserving all collected rows and completed results.

(3) Continue Scanning:

- Resumes hashing or VirusTotal lookups from the exact point where the scan was paused.

(4) Skip Current Items:

- Switches the module into new-items-only mode.
- Subsequent Start New Scan operations process only newly introduced persistence entries.

(5) Save The Results:

- Exports the full table to a CSV file on demand.
- Uses a timestamped filename and defaults to the Desktop.

Typical use case:

(1) Build a baseline of persistence mechanisms present on a Windows endpoint.
(2) Re-scan after running a suspicious installer or sample to identify newly introduced autostart entries.
(3) Map persistence artifacts to real on-disk binaries and validate them using SHA-256 hashes.
(4) Surface stealthy persistence techniques such as WMI event subscriptions alongside traditional autoruns.