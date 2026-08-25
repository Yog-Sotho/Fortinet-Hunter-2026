# Fortinet Hunter 2026 — Operator Guide

**Version:** v2026.08 · **Plugins:** 97 · **CVEs:** 58 · **Suite:** 809 passed + 32 subtests
**Author:** YogSotho - BrokenSec

> FortiOS attack-surface mapper, exploit engine, exfiltration pipeline, C2,
> ML scoring, and CI/CD worm detection — one framework.

---

## 1. Installation

### 1.1 Requirements

| Component | Min | Notes |
|---|---|---|
| Python | 3.10+ | 3.11+ recommended; developed on 3.14 |
| pip | any | bootstrapped automatically by the downloader |
| OS | Linux / macOS / Windows | Linux recommended (hashcat, sniftran) |

### 1.2 Step-by-step

```bash
# 1. Unpack the distribution
unzip Fortinet-Hunter-framework-slim.zip -d fortinet-hunter
cd fortinet-hunter

# 2. Bulletproof dependency downloader (pure stdlib — always runs)
python3 tools/fetch_components.py
#    installs aiohttp, cryptography, paramiko, PyYAML (auto-bootstraps pip)

# 3. Optional components (pick what you need)
python3 tools/fetch_components.py --optional hashcat geoip2 impacket shodan openai
python3 tools/fetch_components.py --bins sniftran          # capture → PCAPng

# 4. Verify
python3 -m fortinet_hunter --help
python3 -m fortinet_hunter list | head -20
```

### 1.3 Quick smoke test

```bash
# Dry-run scan (no network)
python3 -m fortinet_hunter scan 127.0.0.1:8443 --dry-run 2>/dev/null | tail -5

# Full offline test suite (~13 min)
python3 -m pytest tests/ -p no:warnings --no-header 2>&1 | tail -2
#   → 809 passed, 32 subtests passed
```

---

## 2. Usage

### 2.1 Command surface

```
scan        fingerprint + detect + fuzz + post           exploit     run a weaponized exploit
chain       multi-stage attack chains                    agent       agentic orchestrator loop
path        attack-path graph planner                    list        enumerate plugins + CVEs
score       rank CVEs (EPSS/KEV/ML/success)              report      convert runs to HTML/JSON
dashboard   web dashboard                                beacon      C2 beacon (Tor/DoH)
doh-server  RFC 8484 DoH listener                        evade       evasion primitives
darkweb     dark-web credential monitoring               sniff       passive auth sniffer
crack       SH2/AK1 hash cracking                        brute       .txt brute-force (Belsen-ready)
megalodon   CI/CD worm IOC scan                          fuzz        SSL-VPN/WS fuzzer
persist     persistence mechanisms                       fgfm        FGFM probe
osint       Shodan/Censys/FOFA discovery                 extract     firmware/hardware extraction
```

### 2.2 Core workflow

```bash
# 1. Scan a target
python3 -m fortinet_hunter scan 203.0.113.7:443 --check-cve -o results/

# 2. Exploit (dry-run first — always safe, no network)
python3 -m fortinet_hunter exploit cve_2025_32756 203.0.113.7:443 --dry-run

# 3. Chain + exfil
python3 -m fortinet_hunter chain sslvpn_config_exfil 203.0.113.7:443 --dry-run

# 4. Score the estate
python3 -m fortinet_hunter score --ml --success

# 5. Report
python3 -m fortinet_hunter report results/run.json --enhanced
```

### 2.3 NEW: dynamic exploit ingestion (`--expl`)

Integrate a user-provided exploit **without touching framework code**:

```bash
# Audit + dry-run test + register (in-memory)
python3 -m fortinet_hunter exploit --expl /path/to/my_exploit.py

# Audit + test only (no registration)
python3 -m fortinet_hunter exploit --expl my_exploit.py --no-register

# Persist permanently (copies into the framework + canonical list)
python3 -m fortinet_hunter exploit --expl my_exploit.py --persist

# Run the ingested exploit against a target
python3 -m fortinet_hunter exploit <cve_id_from_output> 203.0.113.7:443 --dry-run
```

**Exploit file contract** (must be a `.py` file):

```python
from fortinet_hunter.exploits.base import BaseExploit, ExploitResult

class MyExploit(BaseExploit):
    cve_id = "CVE-2026-XXXXX"      # required
    description = "what it does"    # required
    cvss = 9.8                      # required
    severity = "CRITICAL"           # required

    async def run(self, state=None) -> ExploitResult:
        st = self._new_state(state)
        if self.dry_run:            # dry-run invariant: zero network
            return ExploitResult(success=True, session=st,
                                 detail="DRY-RUN OK")
        # ... live exploitation here ...
        return ExploitResult(success=True, session=st, detail="OK")
```

**Audit rules** (static AST check): exactly one `BaseExploit` subclass;
required attrs `cve_id/description/cvss/severity`; `run()` method present;
blocked imports rejected (`os`, `subprocess`, `socket`, `ctypes`, `pickle`,
`marshal`, `pty`, `fcntl`, `multiprocessing`, `threading`).

### 2.4 Corpus operations (operator-provided data)

```bash
# Belsen leak: brute a device with its own leaked creds
python3 -m fortinet_hunter brute 187.248.37.194_443 \
    --creds resources/Belsen-leak/MX/187.248.37.194_443/vpn-passwords.txt --dry-run

# Fortibleed 830K corpus (library only)
python3 - <<'EOF'
from fortinet_hunter.recon.fortibleed import FortiBleedCorpus
c = FortiBleedCorpus().load()
print(c.summary())
EOF

# MEGALODON worm detection
python3 -m fortinet_hunter megalodon resources/megaladon.txt
```

---

## 3. Architecture

```
fortinet_hunter/          canonical package (97 plugins)
├── core/                 registry, CVE DB (58), session, target, attack graph
├── detectors/            32 CVE detectors
├── exploits/             19 exploits (+ dynamic ingestion)
├── exfil/                5 exfiltration modules (vault, DNS-TXT, config, ...)
├── lateral/              15 lateral-movement modules
├── recon/                10 recon modules (incl. megalodon, fortibleed)
├── ops/                  evasion + sniffer + crack
├── persist/              3 persistence modules
├── ml/                   4 ML modules (cve_risk, anomaly, payload, success)
├── c2/                   2 C2 channels (gist, ngrok) + Shai kit
├── agent/                agentic orchestrator
└── reporting/            enhanced report generator
fortigate_hunter/         legacy shim (234 tests green)
tools/                    build_zip, gen_inventory, fetch_components
tests/                    809 tests + 32 subtests
```

---

## 4. Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `pyftdi not installed` etc. | optional deps absent | `fetch_components.py --optional <name>` (harmless warnings) |
| `ModuleNotFoundError: aiohttp` | required dep missing | `python3 -m pip install aiohttp` or re-run downloader |
| `Unknown exploit` | exploit not registered | `list` to see names; `--persist` user exploits |
| Scan crash `web_dashboard` | old CLI invocation | use `scan <target> --check-cve` |
| HTTPS cert errors on lab boxes | self-signed certs | framework disables TLS verify in live mode by design |
| `hashcat not found` (crack) | binary missing | `apt install hashcat` or set `--hashcat-bin` |
| Ahmia Tor search returns form | token rotated | the tool auto-extracts + retries; retry once |
| Full suite slow | 809 tests | run targeted: `pytest tests/test_<area>.py -q` |
| `fortibleed` command missing | replaced by generic `brute` | use `brute --creds f --targets f`; corpus = library |
| Corpora missing | slim package excludes data | use the FULL package (contains resources/) |

### 4.1 Environment

| Variable | Purpose |
|---|---|
| `FH_FORTIOS_HOST/PORT/USER/PASSWORD` | live FortiOS CLI session (sniff live) |
| `FH_FORTIOS_PDE_KEY` | per-device ENC key (config decrypt) |
| `FH_TEST_API_KEY` | deterministic dry-run harvest in tests |
| `SHODAN_API_KEY` / `CENSYS_*` | osint sources |

### 4.2 Known limitations

- Exploitation requires **authorized targets only** — the framework is a
  defensive-security audit tool.
- Live exploits need reachable targets; dry-run is always safe.
- ML modules degrade gracefully without optional deps (pure-Python fallback).

---

## 5. License & etiquette

READMEs carry the motto, code carries the work.

Private

**YogSotho - BrokenSec**