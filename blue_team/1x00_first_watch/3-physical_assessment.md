# Physical Security Assessment — MedDefense Central Walk-Through

> **Assessor context:** Structured risk decomposition using the Vulnerability / Threat / Impact (CIA) framework, derived from direct facility observation alongside James Chen (Director of Physical Security).

---

## Observation 1: Server Room Access

**Vulnerability:** The server room is located on a high-traffic ground floor accessible from a public-facing cafeteria corridor. Physical entry relies solely on a generic, universally-issued badge with no role-based access restriction, no CCTV coverage of the entry point, and no visitor or access log — making unauthorized entry undetectable and unattributable.

**Threat:** A malicious insider (custodial or administrative staff with no legitimate IT need) or a social-engineering attacker who tailgates an employee uses their standard badge to enter the server room after hours. Once inside, they connect a rogue device (e.g., a USB drop implant or a network tap) to a server, or physically remove a storage drive containing patient records.

**Impact:**
- **Confidentiality** — Patient health records and organizational data stored on-premises may be exfiltrated.
- **Integrity** — Hardware-level tampering (e.g., firmware implants, modified configurations) could corrupt data or establish persistent backdoors without triggering software-layer detection.
- **Availability** — Physical destruction or removal of servers would cause immediate system outage, disrupting clinical operations.

**Severity:** 🔴 **Critical** — Unrestricted physical access to core infrastructure, with zero detection or logging capability, represents a complete failure of the physical security perimeter; a single bad actor could compromise the entire environment without leaving any forensic trail.

---

## Observation 2: Network Closet

**Vulnerability:** A second-floor network closet housing switches and patch panels — core network infrastructure — has no physical lock and sits with its door ajar, allowing unrestricted access to anyone in the corridor. A laminated credential sheet with plaintext switch management username and password is affixed in plain sight inside the closet.

**Threat:** An opportunistic attacker (visitor, contractor, or disgruntled employee) notices the open door, photographs or memorizes the credentials, and later authenticates remotely to the switch management interface. They alter VLAN segmentation, mirror traffic to an attacker-controlled port (SPAN attack), or disable switch ports serving critical systems. Alternatively, they directly plug a laptop into a patch panel port and gain authenticated network access from inside the trusted perimeter.

**Impact:**
- **Confidentiality** — Network traffic mirroring enables passive interception of unencrypted data in transit, including EHR queries and authentication exchanges.
- **Integrity** — VLAN manipulation allows the attacker to bridge network segments that should be isolated (e.g., medical IoT and administrative workstations), enabling lateral movement and man-in-the-middle attacks.
- **Availability** — Disabling switch ports or reconfiguring trunks could sever connectivity for clinical devices, causing patient monitoring systems to go offline.

**Severity:** 🔴 **Critical** — The combination of uncontrolled physical access and posted plaintext credentials provides a direct, low-skill path to full network control; this is a textbook multi-vector attack opportunity requiring zero technical sophistication to exploit.

---

## Observation 3: Nurse Station — Unattended EHR Session

**Vulnerability:** A workstation at the third-floor nurse station is logged into the Electronic Health Record (EHR) system with an active patient record displayed, left unattended for at least 15 minutes with no automatic screen lock or session timeout. Institutional policy, as posted ("please do not log out between shifts"), actively discourages the primary compensating control — user logout — normalizing this exposure.

**Threat:** An unauthorized individual (another patient's family member, a visitor who wanders from an adjacent waiting area, or a malicious insider) approaches the unattended station and accesses, copies, or modifies a patient's health records. In a more targeted scenario, a threat actor uses the live authenticated session to pivot into the EHR backend — viewing records beyond the visible patient, altering medication orders, or extracting bulk data before staff return.

**Impact:**
- **Confidentiality** — Protected Health Information (PHI) is exposed to unauthorized parties, triggering HIPAA breach notification obligations and potential regulatory fines.
- **Integrity** — An attacker with write access to the EHR could alter diagnoses, medication dosages, or allergy records, creating direct patient safety risk and a falsified medical record.
- **Availability** — If the session is used to lock the account or corrupt records, clinical staff lose access to patient data at a critical moment.

**Severity:** 🔴 **Critical** — A live, authenticated EHR session with no timeout and a policy that prevents staff from applying the most obvious remediation creates a direct, repeatable path to PHI exposure and falsification of medical records, with immediate patient safety and regulatory consequences.

---

## Observation 4: Medical IoT — Unpatched Vital Signs Monitor on Flat Network

**Vulnerability:** A connected vital signs monitor openly displays its IP address (10.10.3.47) and firmware version (v2.1.3, last updated 2019). The device shares the same IP subnet as nurse station workstations, indicating that medical IoT devices are not network-segmented from administrative/clinical computing assets. The five-year-old firmware almost certainly contains unpatched known vulnerabilities (CVEs).

**Threat:** An attacker with access to the internal network (achieved via any of the other vectors observed during this walk-through) performs network reconnaissance, identifies the IoT device by IP, queries public CVE databases for vulnerabilities in firmware v2.1.3, and exploits a known remote code execution or authentication bypass flaw. From the compromised monitor, they pivot laterally across the flat subnet to EHR workstations, or manipulate device readings to cause alert fatigue or clinical misjudgment. The publicly visible IP and port label (MED-3F-12) also aid physical mapping for a follow-on physical attack.

**Impact:**
- **Confidentiality** — A compromised device may relay patient biometric data to an external attacker-controlled endpoint.
- **Integrity** — Tampered firmware or spoofed sensor readings could falsify vital sign data displayed to clinicians, directly endangering patient health.
- **Availability** — A denial-of-service attack against the device or its subnet could disable monitoring for an entire ward simultaneously.

**Severity:** 🔴 **Critical** — An internet-of-medical-things device running 5-year-old unpatched firmware on a flat network shared with clinical workstations represents a laterally exploitable beachhead with potential for direct patient harm — the highest consequence tier in a healthcare environment.

---

## Observation 5: Emergency Exit — Perimeter Bypass to Restricted Wing

**Vulnerability:** A fire exit door between the public waiting area and the restricted administrative wing is propped open with a physical wedge, bypassing its access control function entirely. The open door provides a direct, unobstructed view of — and path to — the IT department corridor and the Director's office. The hand-written sign institutionalizes the breach, suggesting it is a persistent, accepted condition rather than a momentary oversight.

**Threat:** An external attacker, delivery person, or unauthorized visitor present in the public waiting area walks through the propped door without challenge, badge scan, or alarm trigger, entering the restricted administrative zone. They proceed to the IT department or James Chen's office to conduct physical reconnaissance, install keyloggers on unattended machines, access filing cabinets with sensitive documentation, or shoulder-surf credentials. Since the door generates no alert, the intrusion is invisible to security operations.

**Impact:**
- **Confidentiality** — Physical access to the IT department and executive offices exposes policy documents, network diagrams, credentials, and strategic information.
- **Integrity** — Hardware implants (keyloggers, rogue USB devices) installed on IT staff workstations could compromise administrative credentials and allow persistent, stealthy tampering.
- **Availability** — Physical disruption of IT infrastructure (e.g., cutting network cables, destroying hardware) would impact facility-wide operations.

**Severity:** 🔴 **Critical** — A permanently propped fire exit between public and restricted zones eliminates the physical perimeter entirely; the institutionalized nature of the bypass (written sign, wooden wedge) indicates systemic process failure and means the exposure is continuous, not accidental.

---

## Summary Risk Matrix

| # | Location | Primary Vulnerability | CIA Pillars Affected | Severity |
|---|---|---|---|---|
| 1 | Server Room | Universal badge access, no camera, no log | C, I, A | 🔴 Critical |
| 2 | Network Closet | No lock, open door, plaintext credentials posted | C, I, A | 🔴 Critical |
| 3 | Nurse Station | Unattended EHR session, no timeout, anti-logout policy | C, I, A | 🔴 Critical |
| 4 | Patient Room / IoT | Unpatched firmware, flat network, exposed device info | C, I, A | 🔴 Critical |
| 5 | Emergency Exit | Propped fire door bypasses restricted zone perimeter | C, I, A | 🔴 Critical |

> **Assessment note:** All five observations rated Critical. The findings are not isolated weaknesses — they form a compounding attack chain. An adversary entering through Observation 5 (propped exit) could exploit Observation 2 (open closet, posted credentials), traverse the flat network to reach Observation 4 (IoT devices), pivot to Observation 3 (live EHR session), and ultimately reach the servers identified in Observation 1. The cumulative risk exposure is greater than the sum of its parts.
