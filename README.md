# AI_Security_Scanning
scripts to scan my network for malicious AI

A comprehensive security scanner for detecting malicious plugins and extensions installed by AI coding assistants (GitHub Copilot, Cursor, Claude, ChatGPT, etc.).

**The Problem**
AI coding assistants are increasingly popular, but they also introduce new attack vectors:
* Supply chain attacks: Malicious extensions masquerading as legitimate AI tools
* Credential theft: SSH keys, API tokens, browser cookies, wallet files
* Source code exfiltration: Stealing proprietary code via Discord webhooks, HTTP requests
* Remote Access Trojans (RATs): ScreenConnect, AnyDesk, TeamViewer delivery
* Cryptominers: CoinIMP miners running in background
* Self-propagation: Extensions that modify other installed extensions


**Recent Attacks**

* MaliciousCorgi (Jan 2026): 1.5M+ installs of AI assistants exfiltrating code to Chinese servers
* Fake ClawdBot (Jan 2026): Trojanized AI assistant delivering ScreenConnect RAT
* GlassWorm: Self-propagating worm modifying installed VS Code extensions
* Evelyn Stealer: Credential theft through VS Code extensions

**Detection Capabilities**

This scanner implements multi-layered detection across 6 modules:

1. Package Analysis
Examines extension manifests for suspicious configurations:

|Check|What It Detects|Severity|
|---|---|---|
|Blocklist|Extension ID matches known malicious extensions|Critical|
|Wildcard activation	activation|Events: ["*"] - runs on every action|High|
|Startup activation|onStartupFinished - runs at VS Code launch|Medium|
|Theme with code|Theme extension that has a main entry point|High|
|Malicious npm packages|Dependencies matching known malware packages|Critical|
|Typosquatting|Dependencies within edit distance 1-2 of popular packages|High|
|Lifecycle scripts|preinstall/postinstall scripts with suspicious patterns|Critical/Medium|

3. Indicators of Compromise (IOCs)
Matches against curated threat intelligence:
* Malware hashes: SHA256 hash matching known samples
* C2 domains: Domain extraction matched against blocklist
* C2 IPs: IPv4 extraction matched against blocklist
* GitHub C2: GitHub API/raw URLs referencing known malicious accounts
* Crypto wallets: BTC, ETH, Monero, Solana address patterns

3. AST Analysis
Abstract Syntax Tree parsing to detect structural patterns:
* eval(variable) - Dynamic code execution
* new Function(string) - Runtime code generation
* require(variable) - Computed module loads
* import(variable) - Computed dynamic imports
* process.binding() - Node.js internals access
* globalThis.eval - Indirect eval access

4. Obfuscation Detection
Identifies techniques used to hide malicious intent:
* High entropy strings (>5.5 bits/char)
* Zero-width characters (U+200B-200D)
* Variation selectors (U+FE00-FE0F) - GlassWorm technique
* Bidirectional text overrides (U+202A-202E) - Trojan Source attacks
* Unicode ASCII escapes (\u00XX)
* Cyrillic homoglyphs (а/a, е/e, с/c)

5. YARA Rules
Complex pattern matching for:
* Blockchain-based C2 (Solana RPC, Ethereum smart contracts)
* Code execution patterns (eval, Function constructor, child_process)
* Credential harvesting (NPM/GitHub/SSH, .npmrc access)
* Crypto wallet targeting (MetaMask, Phantom, Exodus)
* Data exfiltration (Discord webhooks, SSH key theft)
* Google Calendar API abuse
* Multi-stage attacks (droppers, reverse shells, keyloggers)
* Native addon loading (.node files)
* Obfuscation patterns (hex variables, fromCharCode)
* macOS persistence (LaunchAgent, Login Items)
* PowerShell attacks (hidden windows, AMSI evasion)
* RAT capabilities (SOCKS proxy, VNC, remote execution)
* RMM tool delivery (ScreenConnect, AnyDesk, TeamViewer)
* Self-propagation (extension modification)

6. Telemetry Detection
Identifies unwanted data collection:
* SDK imports (Sentry, Mixpanel, PostHog, AppInsights)
* Endpoint URLs matching known telemetry services
* API paths (/collect, /track, /ingest, /metrics)
* Data collection patterns (machine_id, user_id, file_paths)
