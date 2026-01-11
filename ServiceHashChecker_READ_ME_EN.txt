Tool name: ServiceHashChecker.exe

Version: 1.0
SHA256 checksum: 9CECF2C1E691D63FB071C1DF13A5CC467AD34197B18D37B7496FC7F960CF074A
File Size: 44,5 KB
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

Purpose: Service Hash Checker is a Windows GUI tool designed for service-based endpoint triage. It inventories Windows services, extracts executable paths and command lines, computes SHA-256 hashes of service binaries on disk, and optionally enriches those hashes with VirusTotal detection summaries to support baseline creation and persistence-focused threat hunting.
License: Free for personal and commercial use.

Important notice - VirusTotal API usage:

(1) This tool optionally integrates with the VirusTotal API to enrich service binary hashes with reputation information.
(2) On first launch, the application displays a mandatory VirusTotal usage notice describing public vs premium API constraints and acceptable use conditions.
(3) Users must explicitly accept this notice to proceed. Declining acceptance prevents the application from running.
(4) By accepting the notice, users confirm that they understand applicable API limits, licensing restrictions, and accept full responsibility for how the integrated service is used.
(5) Reputation lookups are optional. When no API key is provided, service enumeration and hashing remain fully functional and the final reputation column displays an authorization error or remains unpopulated.

Note: Users are responsible for ensuring compliance with the current VirusTotal Terms of Service. API limits and licensing conditions may change over time.

What the application does:

(1) Enumerates Windows services using multiple data sources:

- WMI (Win32_Service) to retrieve PathName (binary path with arguments), StartMode (startup type), and State.
- ServiceController to cross-check service identity and current status.

(2) Normalizes service metadata for consistent reporting:

- Builds a readable Service Name as "DisplayName (ServiceName)" when available.
- Normalizes startup types (for example: Auto -> Automatic).
- Normalizes service states (for example: StartPending -> Start Pending).

(3) Reliably extracts the executable path from the service command line:

- Supports quoted and unquoted paths, including paths with spaces, by cutting at .exe, .dll, or .sys when required.
- Expands environment variables and resolves relative executables via System32, Windows directories, and PATH lookup.
- Displays command lines with the executable unquoted for clearer presentation while preserving arguments.

(4) Computes a SHA-256 hash for each resolved service executable:

- Reads files using FileShare.ReadWrite to allow hashing of binaries currently in use.
- Produces explicit non-hash status values when hashing is not possible, including:

  * ACCESS_DENIED (WIN32: 5)
  * ERROR_FILE_NOT_FOUND (WIN32: 2)
  * PATH_NOT_FOUND (WIN32: 3)
  * ERROR: <ExceptionName>

(5) Optionally queries VirusTotal for unique SHA-256 hashes and displays concise verdicts:

- No security vendors flagged this file as malicious.
- X/Y security vendors flagged this file as malicious.
- VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Rate limit hit (HTTP 429) - try again later.
- We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- VirusTotal error: <HTTP status> <reason> for other unexpected responses.

User interface and displayed data:

The application presents a live-updating table with the following columns:

- Service Name
- Status
- Startup Type
- Executable Path
- Executable File Name
- Executable Command Line
- Executable File SHA-256 Hash
- VirusTotal Detection Summary

Operational workflow and controls:

(1) Start New Scan:

- Collects all current services and appends only newly detected service entries to the existing table.
- Builds internal work queues for hashing and optional VirusTotal lookups.
- Processes results with live table updates.

(2) Cancel:

- Immediately pauses the scan by cancelling active work using a CancellationToken.
- Preserves all collected rows and completed hash or reputation results.

(3) Continue Scanning:

- Resumes scanning from the exact point where it was paused.
- Continues hashing and VirusTotal lookups using saved queue indices.

(4) Skip Services:

- Stops processing existing services and switches the module into new-services-only mode.
- Takes a snapshot of service identities present at the time of skipping.
- Subsequent Start New Scan operations append and evaluate only newly created services.

(5) Save The Results:

- Exports the current table to a CSV file on demand.
- Uses a timestamped filename and defaults to the Desktop.

VirusTotal integration details:

(1) Supports both VirusTotal Public API and Premium API keys.
(2) The API key is read from the VT_API_KEY environment variable or can be provided via a prompt dialog.
(3) If no API key is provided, hashing continues normally and VirusTotal results display an authorization error indicator.
(4) VirusTotal queries are rate-paced at approximately one request every 15 seconds, aligned with the Public API limit of 4 requests per minute.
(5) Displays an estimated remaining scan time during the VirusTotal lookup phase.

Efficiency features:

(1) Deduplication:

- Hashing is performed once per unique executable path and applied to all services pointing to the same binary.
- VirusTotal is queried once per unique SHA-256 hash and applied to all matching rows.

(2) In-memory caching:

- Executable path -> SHA-256 cache prevents repeated hashing.
- SHA-256 -> VirusTotal summary cache prevents repeated reputation queries.

(3) Live status reporting:

- Status lines indicate the current phase (collection, hashing, VirusTotal lookup, or rate-limit waiting).

Typical use case:

(1) Establish a baseline of Windows services, including startup configuration and binary locations.
(2) Re-run the scan after a suspected compromise to quickly identify newly introduced services.
(3) Verify service binaries using SHA-256 hashes and optionally leverage VirusTotal summaries to prioritize suspicious or unexpected persistence mechanisms.