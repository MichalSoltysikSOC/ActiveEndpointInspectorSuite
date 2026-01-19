Tool name: ActivePublicIPChecker.exe

Version: 1.0
SHA256 checksum: F13CCD52C8FA3A359442C0518B39AFC49377CB65028DC0BDE006DE31549A41A2
File Size: 46,5 KB
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

Purpose: Active Public IP Checker is a Windows GUI tool designed for endpoint network triage. It enumerates active TCP connections, filters out non-public remote endpoints, correlates each connection with the owning process, and optionally enriches public IP addresses with reputation data from AbuseIPDB to support baseline creation and follow-up threat hunting.
License: Free for personal and commercial use.

Important notice - AbuseIPDB API usage:

(1) This tool optionally integrates with the AbuseIPDB API to enrich public IP addresses with reputation and geolocation context.
(2) On first launch, the application displays a mandatory AbuseIPDB usage notice describing API plan limits, rate constraints, and acceptable use conditions.
(3) Users must explicitly accept this notice to proceed. Declining acceptance prevents the application from running.
(4) By accepting the notice, users confirm that they understand applicable API limits, licensing restrictions, and accept full responsibility for how the integrated service is used.
(5) IP reputation lookups are optional. When no API key is provided, the tool remains fully functional for connection and process enumeration, and only the final reputation-related columns remain unpopulated.

Note: Users are responsible for ensuring compliance with the current AbuseIPDB Terms of Service. API limits and licensing conditions may change over time.

What the application does:

(1) Enumerates all active TCP connections directly from Windows using iphlpapi.dll (GetExtendedTcpTable), for both IPv4 and IPv6.
(2) Filters connections to retain only those where the remote endpoint is a public IP address, excluding:

- RFC1918 private ranges.
- Loopback and link-local addresses.
- Documentation and test ranges.
- Multicast, reserved, and non-routable address blocks.

(3) For each remaining connection, collects:

- Public IP address (remote endpoint).
- Source port (local port).
- Destination port (remote port).
- Protocol (TCPv4 or TCPv6).
- Connection state (for example: ESTABLISHED, SYN_SENT, TIME_WAIT).
- PID, process name (normalized with .exe suffix), and process path.

(4) Optionally queries AbuseIPDB for each unique public IP address and displays:

- IP geolocation information (country name and code, when available).
- AbuseIPDB reputation summary, including:

- This IP was reported X times. Confidence of Abuse is Y%.
- This IP was not found in our database.
- AbuseIPDB error: 401 Unauthorized (missing/invalid API key).
- Rate limit hit (HTTP 429) - try again later.
- AbuseIPDB error: <HTTP status> <error message> for other unexpected API responses.
- Network/TLS error: <error message> for connectivity or TLS-related failures where no HTTP status was returned.

User interface and displayed data:

The application presents a live-updating table with the following columns:

- Public IP Address
- IP Location
- Source Port
- Destination Port
- Protocol
- Connection State
- PID
- Process Name
- Process Path
- AbuseIPDB Reputation Summary

Operational workflow and controls:

(1) Start New Scan:

- Enumerates current active TCP connections and filters to public remote IPs.
- Appends only newly observed connection rows to the existing table without clearing prior results.
- Builds a work queue of unique public IPs requiring AbuseIPDB enrichment.
- Performs reputation lookups with controlled pacing.

(2) Cancel:

- Immediately pauses the scan by cancelling active work using a CancellationToken.
- Preserves all collected rows and completed reputation results.
- Enables Continue and Skip options if pending work remains.

(3) Continue Scanning:

- Resumes AbuseIPDB lookups from the exact point where the scan was paused.
- Continues processing the IP queue by index without restarting completed lookups.

(4) Skip Active IP Checks:

- Stops pending AbuseIPDB lookups and switches the module into new-connections-only mode.
- Takes a snapshot of connection identities currently present in the table.
- On the next Start New Scan, appends and evaluates only newly observed active connections.

(5) Save The Results:

- Exports the current table to a CSV file on demand.
- Uses a timestamped filename and defaults to the Desktop.

AbuseIPDB integration details:

(1) Displays a mandatory AbuseIPDB API usage notice on startup.
(2) Prompts for an AbuseIPDB API key, stored only in memory for the current session.
(3) If no API key is provided or the prompt is skipped, the application continues collecting connection and process metadata without performing reputation lookups.
(4) Uses the AbuseIPDB check endpoint with maxAgeInDays set to 365 and verbose output enabled.
(5) Lookups are rate-paced at approximately one request per 1.1 seconds to respect API limits.
(6) Displays an estimated remaining scan time while processing the reputation queue.
(7) Caches AbuseIPDB responses per IP address in memory to avoid repeated queries during the same session.

Typical use case:

(1) Establish a baseline of public IP addresses the endpoint is actively communicating with and the processes responsible for those connections.
(2) Execute or observe suspicious activity on the system.
(3) Run Start New Scan again to quickly identify newly established outbound connections to public IPs.
(4) Use AbuseIPDB reputation context to prioritize investigation of unexpected or potentially malicious network activity.