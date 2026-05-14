# Root Cause Analysis — billing-srv-01

## 1. What is `kworker` actually doing?

The process at PID 8834 is **not** a legitimate kernel worker thread. Several indicators expose it immediately.

**The masquerade:** Real kernel worker threads appear as `[kworker/0:1]` — enclosed in brackets, owned by `root`, and live in kernel space with no filesystem path. This one:

- Runs as `www-data` (the Apache web server user)
- Has a real binary on disk at `/var/www/html/.cache/kworker`
- Is invoked with `./kworker` (a relative path, a classic evasion technique)

**The `stratum+tcp://` connection is the smoking gun.** The Stratum protocol is the standard communication protocol used by cryptocurrency mining pools. The process is connecting to `pool.monero.org` and two backup pool IPs, which `config.json` confirms:

```json
"pools": [
    {"url": "stratum+tcp://pool.monero.org:4443", "user": "48Bv3...Kj2"},
    {"url": "stratum+tcp://91.121.87.10:8080",    "user": "48Bv3...Kj2"},
    {"url": "stratum+tcp://104.238.140.32:3333",  "user": "48Bv3...Kj2"}
]
```

The wallet address (`48Bv3...Kj2`) is a Monero (XMR) address. The attacker deployed **XMRig** (or a similar Monero cryptominer), disguised it as a kernel process, and configured it to mine cryptocurrency using MedDefense's hardware — sending the proceeds to their own wallet.

The CPU saturation (94.2%) is a direct consequence: mining is computationally intensive by design.

---

## 2. Classifying the Real Compromise — The Two CIA Pillars

The sysadmin framed this as an **Availability** problem ("CPU saturation → billing app is slow"). That is the *symptom*, not the violation. Two more fundamental CIA pillars were compromised first.

### ⚠️ Confidentiality

An attacker who can drop and execute a binary on a server has, by definition, achieved **code execution** on that system. They have at minimum the same access as `www-data`, which means:

- **Apache's document root and configuration files** are readable
- **The billing application's source code** is accessible
- **Database credentials** stored in the web application's config (e.g., `.env` or equivalent) are exposed — and MySQL is running on the same host (`mysqld`, port 3306 open)
- Any **patient billing data, PHI (Protected Health Information), or financial records** accessible to the application are potentially exfiltrated

MedDefense operates in healthcare. A Confidentiality breach of billing data is a **HIPAA reportable event**. The team is focused on slow performance while patient data may already be compromised.

### ⚠️ Integrity

The attacker modified the filesystem — they wrote a malicious binary and a configuration file to `/var/www/html/.cache/`. This means:

- **The server cannot be trusted.** Any file on this system may have been altered
- The attacker achieved **persistence**: the miner returns after restarts (observed behaviour — the problem recurs every ~2 hours after a service restart, because only Apache/MySQL were restarted, not the miner process)
- If the attacker has write access, they may have installed **backdoors, rootkits, or web shells** that survive the current investigation
- The integrity of the billing application itself is **unverified** — the attacker had write access to the web root

**Availability is the last domino to fall**, and it is the only one that triggered an alert. Confidentiality and Integrity were violated silently.

---

## 3. Why the Hardware Upgrade Solves Nothing

The sysadmin's recommendation:

> *"Recommend migration to a new VM with 16GB RAM and 8 vCPUs."*

This is a textbook example of **treating symptoms while ignoring the disease.**

| What the upgrade addresses | What it does NOT address |
|---|---|
| Slow billing app performance | The malicious `kworker` binary |
| RAM constraints | The three outbound connections to mining pools |
| Old hardware lifecycle | The unpatched Apache RCE vulnerability |
| Ubuntu 18.04 EOL (partially) | The attacker's persistence mechanism |

If MedDefense migrates without first remediating the vulnerability, one of two outcomes occurs:

1. **The data is migrated with the malware** — the miner comes with it, and performance degrades on the new VM within days
2. **The server is rebuilt clean, but the same vulnerability exists** — the attacker's tooling re-exploits the unpatched Apache version and reinfects the new server

This is not hypothetical — **it already happened.** The server was rebuilt after January's ransomware incident and was reinfected. Marcus identified this explicitly in his notes.

More critically: a hardware migration does not trigger a forensic investigation, does not notify stakeholders of a potential HIPAA breach, and does not remove the threat actor's access. **The attacker continues operating.**

---

## 4. Connecting to the January Ransomware Incident

The same server. Two separate malware infections. Different payloads (ransomware → cryptominer). This pattern points to one conclusion:

> **The initial attack vector was never identified and patched during the January rebuild.**

Marcus's notes name the likely candidate: **Apache 2.4.29**, which has documented Remote Code Execution (RCE) vulnerabilities. If the rebuild restored the same Apache version without patching it, the server re-exposed the same open door.

The questions the team should be asking:

- **What CVE enabled the initial January compromise?** Was it Apache (e.g., CVE-2021-41773, CVE-2021-42013)? A vulnerable web application? An exposed service?
- **Was a post-incident vulnerability assessment performed after January?** The fact that the server was rebuilt without patching the entry point suggests the root cause was never formally documented.
- **What did the ransomware actors access before deploying their payload?** If data exfiltration occurred in January, MedDefense may have an unresolved breach notification obligation.
- **Are other servers rebuilt from the same base image equally vulnerable?**

The January incident and this cryptominer are likely the work of **different threat actors who exploited the same unpatched vulnerability** — a common pattern where one actor's initial access is reused or the exploit is weaponised opportunistically. The January actors wanted a ransom. This actor wants passive compute revenue. **The entry point is the same.**

---

## Summary: What Actually Needs to Happen

| Priority | Action |
|---|---|
| 1 | **Isolate the server immediately** — disconnect from network, preserve forensic state |
| 2 | **Do not restart or migrate** — this destroys forensic evidence |
| 3 | **Identify the entry point** — review Apache access logs around the binary creation date (14 days ago per `stat` output) |
| 4 | **Assume data breach** — treat as a HIPAA incident until confirmed otherwise; notify compliance and legal |
| 5 | **Audit all servers** rebuilt from the same January process for the same vulnerability |
| 6 | **Patch the vulnerability, then rebuild** — not rebuild and assume it's fixed |

The sysadmin saw a performance problem. The actual situation is an **active, persistent intrusion on a server containing patient billing data**, with an unpatched vulnerability that has already enabled two separate compromises.
