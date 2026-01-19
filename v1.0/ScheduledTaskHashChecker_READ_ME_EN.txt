Tool name: ScheduledTaskHashChecker.exe

Version: 1.0
SHA256 checksum: B7E0E117D3B747E74918A369928434E8D83E8F5BB1F090956E065ABCAC3330AB
File Size: 53,5 KB
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

Purpose: Scheduled Task Hash Checker is a Windows GUI tool designed for persistence analysis and threat hunting focused on Windows Scheduled Tasks. It enumerates scheduled tasks, expands them into individual task actions, computes SHA-256 hashes of action executables, and optionally enriches those hashes with VirusTotal detection summaries to support baseline creation and detection of newly introduced persistence.
License: Free for personal and commercial use.

Important notice - VirusTotal API usage:

(1) This tool optionally integrates with the VirusTotal API to enrich scheduled task action binaries with reputation information.
(2) On first launch, the application displays a mandatory VirusTotal usage notice describing public vs premium API constraints and acceptable use conditions.
(3) Users must explicitly accept this notice to proceed. Declining acceptance prevents the application from running.
(4) By accepting the notice, users confirm that they understand applicable API limits, licensing restrictions, and accept full responsibility for how the integrated service is used.
(5) Reputation lookups are optional. When no API key is provided, task enumeration and hashing remain fully functional and the final reputation column remains unpopulated or displays an authorization error.

Note: Users are responsible for ensuring compliance with the current VirusTotal Terms of Service. API limits and licensing conditions may change over time.

What the application does:

(1) Enumerates all Windows Scheduled Tasks using the native Task Scheduler COM interface, including tasks located in nested folders.
(2) Processes tasks at the action level:

- Each executable action is represented as a separate row.
- Tasks with multiple actions are fully expanded.
- Tasks without executable actions are still recorded for visibility.

(3) Collects detailed task context:

- Full scheduled task name, including folder path.
- Trigger types and schedules (for example: Logon, Boot, Time-based, Event-based, Enabled or Disabled).
- Task action type (Exec, ComHandler, SendEmail, ShowMessage, or Unknown).

(4) Normalizes executable paths and command lines:

- Extracts executable paths from task definitions.
- Expands environment variables such as %windir% and %SystemRoot%.
- Resolves relative paths using System32, Windows directories, and PATH lookup.
- Displays command lines without quotes around the executable while preserving arguments.

(5) Computes SHA-256 hashes of resolved task action executables:

- Uses FileShare.ReadWrite to hash binaries that may be in use.
- Produces explicit status strings when hashing is not possible, including:

  * ERROR_FILE_NOT_FOUND (WIN32: 2)
  * PATH_NOT_FOUND (WIN32: 3)
  * ACCESS_DENIED (WIN32: 5)
  * ERROR: <ExceptionType>

(6) Optionally queries VirusTotal for unique SHA-256 hashes and displays concise verdicts:

- No security vendors flagged this file as malicious.
- X/Y security vendors flagged this file as malicious.
- VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Rate limit hit (HTTP 429) - try again later.
- We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- VirusTotal error: <HTTP status> <reason> for other unexpected responses.

User interface and displayed data:

The application presents a live-updating table with the following columns:

- Scheduled Task Name
- Task Triggers
- Task Action Type
- Action File Path
- Action File Name
- Action Command Line
- Action File SHA-256 Hash
- VirusTotal Detection Summary

Operational workflow and controls:

(1) Start New Scan:

- Enumerates all current scheduled tasks and expands them into task-action rows.
- Appends only newly detected task actions to the existing table without removing prior results.

(2) Cancel:

- Immediately pauses the scan by cancelling active work.
- Preserves all collected rows and completed hash or reputation results.

(3) Continue Scanning:

- Resumes hashing or VirusTotal lookups from the exact point where the scan was paused.
- Continues processing using saved queue indices.

(4) Skip Scheduled Tasks:

- Freezes the currently known task set and switches the module into new-tasks-only mode.
- Subsequent Start New Scan operations process only newly created scheduled tasks or actions.

(5) Save The Results:

- Exports the full table to a CSV file on demand.
- Uses a timestamped filename and defaults to the Desktop.

Efficiency and analysis features:

(1) Deduplication:

- Hashing is performed once per unique executable path and applied to all task actions using the same binary.
- VirusTotal lookups are performed once per unique SHA-256 hash.

(2) In-memory caching:

- Executable path -> SHA-256 cache reduces repeated hashing.
- SHA-256 -> VirusTotal summary cache minimizes redundant API requests.

(3) Stable task identity:

- Maintains consistent identification using a unique key derived from task path, task name, and action index.

(4) Live status reporting:

- Displays current scan phase and estimated remaining time during VirusTotal enrichment.

Typical use case:

(1) Establish a baseline of scheduled task-based persistence mechanisms on a Windows endpoint.
(2) Re-run the scan after suspected compromise or malware execution to identify newly added or modified scheduled tasks.
(3) Validate task action binaries using SHA-256 hashes and optionally leverage VirusTotal summaries to prioritize suspicious persistence mechanisms.
(4) Support SOC triage, DFIR activities, and proactive threat hunting focused on scheduled task abuse.