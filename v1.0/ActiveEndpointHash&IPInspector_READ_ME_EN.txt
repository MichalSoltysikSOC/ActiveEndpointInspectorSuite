Tool name: ActiveEndpointHash&IPInspector.exe

Version: 1.0
SHA256 checksum: A43FB41C5311AEF1C5E54962AB1930BE39BC330C03D8DE394832C329A94B3882
File Size: 162 KB
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

Purpose: Active Endpoint Hash & IP Inspector is a Windows GUI suite that bundles five endpoint inspection modules into a single tabbed application. It is designed for rapid baseline creation and follow-up threat hunting by combining local endpoint inspection with optional reputation enrichment using VirusTotal and AbuseIPDB.
License: Free for personal and commercial use.

Important notice - external reputation APIs:

(1) This application optionally integrates with third-party reputation services (VirusTotal and AbuseIPDB) to enrich collected artifacts with contextual intelligence.
(2) On first launch, the application displays mandatory notice dialogs explaining API usage limits, licensing constraints, and acceptable use conditions.
(3) Users must explicitly accept these notices to proceed. Declining acceptance prevents the application from running.
(4) By accepting the notices, users confirm that they understand applicable API limits, are authorized to use the provided API keys, and accept full responsibility for how the tool and integrated services are used.
(5) Reputation lookups are optional. When API keys are not provided, all inspection modules remain fully functional and only the final reputation-related columns remain unpopulated.

Note: Users are responsible for ensuring compliance with the current Terms of Service of VirusTotal and AbuseIPDB. API limits, licensing conditions, and policies may change over time.

Application structure:

ActiveEndpointHashIPInspector.exe is the main application and unified entry point for the entire toolkit.
The application hosts five endpoint inspection modules inside a single TabControl interface. Each module is implemented as a dedicated UserControl and embedded as a tab.
The main application itself does not perform scanning logic. Its responsibilities include:

(1) hosting and orchestrating modules,
(2) presenting a unified user interface,
(3) enforcing startup notices and responsibility acceptance,
(4) handling optional API key input for the current session,
(5) and providing a consistent operational workflow across all modules.

Module hosting and loading:

(1) Each inspection module is implemented as a UserControl and loaded dynamically at runtime using reflection.
(2) The application searches the current assembly for a UserControl whose class name matches the expected module type (for example: ActiveSHA256HashCheckerControl).
(3) If a required module control is missing from the build, the corresponding tab displays a clear fallback message describing:

- the expected class name,
- required visibility (public UserControl),
- and common build issues (incorrect naming or leftover Main() entry points).

(4) Modules may optionally override the visible tab title by exposing a public string property named ModuleTitle.

Startup workflow and API key handling:

On first launch, the application executes the following sequence:

(1) VirusTotal notice dialog:

- Explains Public vs Premium API usage and licensing constraints.
- Declining acceptance closes the application.

(2) AbuseIPDB notice dialog:

- Explains plan limits and acceptable use constraints.
- Declining acceptance closes the application.

(3) Dual API key dialog (optional):

- Prompts for VT_API_KEY and ABUSEIPDB_API_KEY.
- If skipped, the application continues running but clears both environment variables to avoid partial configuration ambiguity.
- If provided, API keys are stored only as process-level environment variables for the current session and are not persisted.

User experience:

(1) All inspection capabilities are presented within a single integrated GUI, allowing analysts to switch between endpoint perspectives without launching multiple executables.
(2) Each hosted module follows a consistent operational model:

- baseline-oriented scanning,
- pause and resume for long-running reputation lookups,
- tab-specific Skip modes for new-artifact-only detection,
- and CSV export functionality implemented within each module.

(3) The application provides a uniform look, feel, and control layout across all modules to reduce analyst cognitive load during investigations.

Intended use:

(1) SOC laboratories and analyst workstations.
(2) Blue Team operations in test and production environments.
(3) Threat hunting and detection engineering activities.
(4) Malware analysis and post-execution inspection in controlled scenarios.
(5) Incident response triage and post-compromise endpoint inspection.
(6) Production endpoints for baseline creation and continuous inspection, provided no malicious code is intentionally executed.

Summary:

ActiveEndpointHashIPInspector.exe is the wrapper and orchestrator for a five-module Windows endpoint inspection toolkit. It provides a single unified UI, enforces responsible API usage, manages optional session-based keys, and embeds individual inspection modules as tabs to support baseline-driven endpoint analysis and threat hunting.