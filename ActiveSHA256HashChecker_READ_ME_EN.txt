Tool name: ActiveSHA256HashChecker.exe

Version: 1.0
SHA256 checksum: 0EE07FBC2FE0E2DD9EB913900148F2980F7855A46FD74693E2FAB91C95FE38F1
File Size: 39,0 KB
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

Purpose: Active SHA-256 Hash Checker is a Windows GUI tool designed for quick endpoint triage of currently running processes. It enumerates live processes, computes SHA-256 hashes of their executable files on disk, and optionally enriches those hashes with VirusTotal reputation data to support baseline creation and follow-up threat hunting.
License: Free for personal and commercial use.

Important notice - VirusTotal API usage:

(1) This tool optionally integrates with the VirusTotal API to enrich file hashes with reputation information.
(2) On first launch, the application displays a mandatory VirusTotal usage notice describing public vs premium API constraints and acceptable use conditions.
(3) Users must explicitly accept this notice to proceed. Declining acceptance prevents the application from running.
(4) By accepting the notice, users confirm that they understand applicable API limits, licensing restrictions, and accept full responsibility for how the integrated service is used.
(5) Reputation lookups are optional. When no API key is provided, the tool remains fully functional for process enumeration and hashing, and only the final VirusTotal reputation column remains unpopulated or displays an authorization error.

Note: Users are responsible for ensuring compliance with the current VirusTotal Terms of Service. API limits and licensing conditions may change over time.

What the application does:

(1) Enumerates all currently running processes, collecting PID, process name, and executable path.
(2) Computes a SHA-256 hash for each unique executable path by reading the file from disk using read sharing, allowing hashing of binaries that are currently in use.
(3) Optionally queries VirusTotal for each unique SHA-256 hash and displays a concise detection summary, including:

- No security vendors flagged this file as malicious.
- X/Y security vendors flagged this file as malicious.
- VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Rate limit hit (HTTP 429) - try again later.
- We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- VirusTotal error: <HTTP status> <reason> for other unexpected responses.

(4) Displays all results in a live-updating table.

User interface and displayed data:

The application presents a table with the following columns:

- PID
- Process Name (normalized to display with .exe suffix)
- Process Path
- Process SHA-256 Hash
- VirusTotal Detection Summary

Operational workflow and controls:

(1) Start New Scan:

- Appends newly detected processes to the existing table without clearing previous rows.
- Rebuilds internal work queues for hashing and optional VirusTotal lookups.
- After Skip Running Processes is used, processes only those processes that appeared after the skip action.

(2) Cancel:

- Immediately pauses the scan by cancelling active work using a CancellationToken.
- Preserves all collected rows and completed hash or reputation results.
- Allows the user to decide whether to continue or skip remaining items.

(3) Continue Scanning:

- Resumes the scan from the exact point where it was paused.
- Primarily intended for continuing the VirusTotal lookup phase after a pause.

(4) Skip Running Processes:

- Stops processing already observed processes and switches the scan into new-items-only mode.
- Takes a snapshot of currently known PIDs and ignores them for subsequent Start New Scan operations.
- Useful when existing VirusTotal lookups are no longer relevant and only newly spawned processes are of interest.

(5) Save The Results:

- Exports the current table to a CSV file on demand.
- Defaults to the Desktop and uses a timestamped filename.

VirusTotal integration details:

(1) Supports both VirusTotal Public API and Premium API keys.
(2) The API key is read from the VT_API_KEY environment variable or can be provided via a prompt dialog.
(3) If no API key is provided or the key dialog is cancelled, hashing continues normally and VirusTotal lookups return an authorization error indicator.
(4) VirusTotal queries are rate-paced at approximately one request every 15 seconds, aligned with the Public API limit of 4 requests per minute.
(5) During the VirusTotal phase, the application displays an estimated remaining scan time.

Efficiency and consistency features:

(1) Deduplication:

- Hashing is performed once per unique executable path and applied to all processes sharing that path.
- VirusTotal is queried once per unique SHA-256 hash and applied to all matching rows.

(2) Caching:

- Executable path to SHA-256 cache prevents repeated hashing of the same file.
- SHA-256 to VirusTotal summary cache prevents repeated reputation queries.

(3) Live status reporting:

- Status lines indicate the current phase (collection, hashing, VirusTotal lookup, or rate-limit waiting).
- Remaining scan time is displayed during reputation enrichment.

Typical use case:

(1) Establish a baseline of running processes and their associated hashes.
(2) Execute or observe unknown or suspicious activity on the endpoint.
(3) Run Start New Scan again to append and evaluate newly appearing processes.
(4) Use VirusTotal summaries to quickly identify unexpected or flagged binaries while retaining the ability to pause, resume, or focus exclusively on newly introduced processes.