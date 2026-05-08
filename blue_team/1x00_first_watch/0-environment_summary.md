# Structured Environment Summary

## 1. Organization Overview

MedDefense Health Systems is a regional healthcare organization operating three sites: MedDefense Central Hospital, Westside Clinic and Corporate HQ. The organization employs approximately 2,000 people across clinical, administrative and IT functions.

MedDefense Central Hospital is the primary clinical facility. It is a 350-bed acute care hospital located downtown, with approximately 1,400 clinical and support staff. It hosts critical departments including Emergency, Surgery, Cardiology, Radiology, Oncology, Pediatrics, Maternity, Pharmacy, Laboratory and Administration. The building has six floors and a basement level that includes mechanical and server room space.

Westside Clinic is a suburban outpatient facility located approximately 12 minutes from Central. It provides primary care, diagnostic imaging, blood work, minor procedures and physical therapy. It has approximately 180 staff members. Westside shares some IT services with Central but also has a local server closet for basic local services.

Corporate HQ is located in Greenfield Business Park, approximately 15 minutes from Central. It hosts administrative departments including Finance, HR, Legal, Marketing, Executive Leadership and IT. It has approximately 220 staff members. The IT department is located at HQ and includes 12 staff members.

The security governance structure is immature. The CISO position is vacant. James Chen, Deputy CISO, acts as the security lead in practice and reports directly to the CEO operationally. The Security Analyst role reports to James Chen. Sarah Park, IT Director, manages IT operations and the IT staff. James Chen has authority over security policy but no direct authority over IT operations, while Sarah Park controls IT execution. This creates a governance gap between security decision-making and operational implementation.

Relevant reporting structure:

- CEO: Dr. Patricia Morales
- Acting security lead: James Chen, Deputy CISO
- Security Analyst: current role, replacing Marcus Webb
- IT Director: Sarah Park
- IT staff: 3 system administrators, 2 network technicians, 1 database administrator, 2 helpdesk analysts, 2 desktop support technicians and 1 vacant intern position
- Other executive stakeholders: CFO Robert Kim, COO Angela Torres and General Counsel David Park

## 2. IT Infrastructure Identified

### MedDefense Central Hospital Systems

| Name / Type | Function | Location | Technical Details |
| --- | --- | --- | --- |
| `ehr-srv-01` | EHR application server | Central Hospital | Ubuntu 20.04 LTS |
| `ehr-db-01` | EHR database server | Central Hospital | Ubuntu 20.04 LTS, PostgreSQL |
| `pacs-srv-01` | PACS imaging server | Central Hospital | Windows Server 2016 |
| `billing-srv-01` | Billing and claims processing | Central Hospital | Ubuntu 18.04 LTS |
| `ad-dc-01` | Primary domain controller | Central Hospital | Windows Server 2019 |
| `ad-dc-02` | Secondary domain controller | Central Hospital | Windows Server 2019 |
| `file-srv-01` | Department file shares | Central Hospital | Windows Server 2016 |
| `print-srv-01` | Print server | Central Hospital | Windows Server 2012 R2, marked unverified |
| `backup-srv-01` | Backup server | Central Hospital | Ubuntu 22.04 LTS, Veeam agent |
| `web-srv-01` | Public website and patient portal | Central Hospital DMZ | Ubuntu 20.04 LTS |
| Local NAS | Backup storage target | Central Hospital server room | Mentioned in Marcus's notes and diagram; located in the same server room, rack and network as backup server |

### Westside Clinic Systems

| Name / Type | Function | Location | Technical Details |
| --- | --- | --- | --- |
| `ws-srv-01` | Local file server and scheduling | Westside Clinic | Windows Server 2016 |
| Possible additional server | Unknown | Westside server closet | Mentioned by Mike Torres but never confirmed by Marcus |
| Local server closet | Local IT equipment area | Westside Clinic | Physical security appears weak; closet reportedly does not lock |

### Corporate HQ Systems

Corporate HQ has no documented on-premise servers. HQ staff use cloud services and connect to Central infrastructure through a site-to-site VPN. The building network and internet service are managed by Greenfield Building Management, and MedDefense has its own VLAN.

### Network Infrastructure

| Site | Component | Function | Technical Details |
| --- | --- | --- | --- |
| Central | Fortinet FortiGate 100F | Perimeter firewall and VPN termination | Fortinet support contract active |
| Central | Cisco core switch | Core switching | Model unknown |
| Central | Cisco access switches | Floor-level access switching | 2 access switches per floor |
| Central | Ubiquiti UniFi APs | Wireless access | 12 access points |
| Central | Guest WiFi | Visitor wireless network | Separate SSID exists, but isolation is not verified |
| Westside | Unmanaged switch | Local switching | Brand unknown |
| Westside | Netgear Nighthawk consumer router | ISP connectivity and site-to-site VPN | Marcus states there is no dedicated firewall |
| HQ | Building-managed network | HQ internet and network connectivity | MedDefense has its own VLAN |
| HQ | Site-to-site VPN | Connectivity to Central | ACLs have not been audited |
| Central-Westside | IPSec VPN | Inter-site connectivity | Runs through the Westside consumer router |
| Central-HQ | Site-to-site VPN | Inter-site connectivity | Runs through building-managed network |

The Central network is documented as `10.10.0.0/16`. Marcus notes that Central appears to have a flat network with servers, workstations, medical devices and other systems on the same broadcast domain. The draft network diagram also states that no VLANs are configured.

### Endpoint Categories

| Endpoint Category | Location | Approximate Count | Technical Details |
| --- | --- | --- | --- |
| Windows 10 workstations | Central Hospital | Approximately 320 | Count based on an AD report from eight months ago |
| Thin clients | Central clinical areas | Approximately 60 | Used in clinical areas |
| Windows 10 workstations | Westside Clinic | Approximately 45 | Count based on outdated AD data |
| Windows 10/11 workstations | Corporate HQ | Approximately 120 | Used by administrative staff |
| Laptops | Corporate HQ / remote-capable users | Approximately 30 | Remote-capable |
| iPads | Physicians | Approximately 25 | Used by physicians for rounds; management status unclear |

Endpoint protection is provided by Sophos according to the service contracts, but Marcus notes that he did not verify whether it is current on all machines.

### Medical and IoT Devices

| Device / System | Function | Location | Technical Details |
| --- | --- | --- | --- |
| Philips IntelliVue patient monitors | Connected patient monitoring | Central Hospital | Approximately 80 units |
| BD Alaris infusion pumps | Network-connected infusion dosage updates | Central Hospital | Approximately 120 units |
| Siemens MAGNETOM MRI scanner | MRI imaging | Radiology, Central Hospital | Marcus notes that it runs Windows XP and is critical |
| GE Revolution CT scanner | CT imaging | Central Hospital | Operating system unknown |
| Nurse call system | Clinical communication / nurse call workflow | Central Hospital | IP-based and integrated with phone system |
| HID Global badge/access system | Physical access control | Organization-wide / selected doors | Connected to Active Directory for some doors |

### Cloud, Security and Third-Party Services

| Vendor / Service | Function | Notes |
| --- | --- | --- |
| Microsoft O365 E3 | Organization-wide cloud productivity services | Main known cloud service |
| Sophos Endpoint Protection | Endpoint security | Current deployment status across all endpoints is unknown |
| Veeam | Backup software | Used with `backup-srv-01` and local NAS |
| Fortinet Support | FortiGate support | Supports Central perimeter firewall |
| Ubiquiti UniFi Controller | Wireless management | Used for Central UniFi APs |
| Greenfield Building Management | HQ network and internet | Included in lease |
| ClearView Security | Guard service at Central | One guard at main entrance, Monday-Friday 7AM-7PM; no weekend or night coverage |
| MedTech Solutions | EHR maintenance | Includes software updates, not hardware; SLA is 4 hours for critical issues and 24 hours for standard issues |

## 3. Data and Services

### Data Types Handled

MedDefense handles multiple categories of sensitive and operational data:

- Patient health records through the EHR platform.
- EHR database records stored in PostgreSQL on `ehr-db-01`.
- Medical imaging data through PACS, MRI, CT, X-ray and ultrasound services.
- Billing and claims processing data through `billing-srv-01`.
- Departmental file share data through `file-srv-01` and `ws-srv-01`.
- Authentication and identity data through Active Directory domain controllers.
- Administrative, legal, HR, finance and executive data through Corporate HQ systems and O365.
- Patient-facing web and portal data through `web-srv-01`.
- Clinical operational data from patient monitors, infusion pumps and the nurse call system.
- Physical access control data through the HID badge/access system.
- Backup data stored through Veeam and the local NAS.

### Critical IT-Dependent Services

| Service | Supporting Infrastructure | Primary Users | Notes |
| --- | --- | --- | --- |
| Electronic Health Records | `ehr-srv-01`, `ehr-db-01` | Clinical staff, physicians, nurses, administrative users | Core clinical service |
| Medical imaging / PACS | `pacs-srv-01`, MRI, CT, X-ray, ultrasound systems | Radiology, clinical departments | Critical for diagnosis and treatment |
| Billing and claims processing | `billing-srv-01` | Finance, billing staff, administrative users | Previously affected by ransomware according to Marcus's notes |
| Authentication and directory services | `ad-dc-01`, `ad-dc-02` | Organization-wide users and systems | Supports identity and access management |
| Department file shares | `file-srv-01`, `ws-srv-01` | Clinical and administrative departments | Stores departmental working files |
| Printing | `print-srv-01` | Organization-wide users | Server is unverified and runs an unsupported OS |
| Backup and recovery | `backup-srv-01`, local NAS, Veeam | IT operations | Backups run nightly to local NAS |
| Public website and patient portal | `web-srv-01` | Patients, external users, internal administrators | Located in DMZ according to the draft diagram |
| Westside scheduling | `ws-srv-01` | Westside Clinic staff | Local scheduling function |
| O365 services | Microsoft O365 E3 | Organization-wide staff | Main documented cloud service |
| Inter-site connectivity | FortiGate, VPNs, Netgear router, HQ building network | Central, Westside and HQ users | Required for shared services and remote site access |
| Wireless access | UniFi APs and unknown Westside WiFi | Staff, possibly guests | Guest WiFi isolation is not verified |
| Nurse call system | IP-based nurse call infrastructure | Clinical staff and patients | Integrated with phone system |
| Badge/access control | HID Global system and AD integration | Staff, facilities, security | Supports physical access control for some doors |
| Endpoint protection | Sophos | Workstations, laptops, possibly servers | Coverage and currency are not fully verified |

## 4. Known Unknowns

### Asset Inventory Gaps

- The ServiceDesk asset list is explicitly marked as partial.
- Some assets were added by different people at different times, and some entries have not been physically verified in more than one year.
- `print-srv-01` is marked unverified.
- There may be an additional server in the Westside server closet, but this has not been confirmed.
- The local NAS used for backups is mentioned, but its model, capacity, configuration, access controls and backup retention details are not documented.
- No complete and current endpoint inventory exists. Current endpoint counts are based on an Active Directory report from eight months ago.
- The management status of approximately 25 physician iPads is unclear.

### Network Documentation Gaps

- The network diagram is a simplified draft and Marcus notes that the real topology is messier.
- The Cisco core switch model at Central is unknown.
- The Westside unmanaged switch brand and configuration are unknown.
- Westside's full network topology is not documented.
- Westside WiFi details are unknown.
- Central guest WiFi exists, but its actual isolation from internal networks has not been verified.
- HQ VPN ACLs have not been audited.
- The HQ network is managed by the building landlord, but the exact boundaries between landlord-managed infrastructure and MedDefense-controlled infrastructure are not fully documented.

### Endpoint and Security Tooling Gaps

- Sophos Endpoint Protection is under contract, but it is unknown whether it is installed, healthy and current on all endpoints.
- No endpoint security evaluation has been completed.
- No formal vulnerability assessment has been completed for all servers.
- SSH password authentication remains enabled on Linux servers, but the exact status of each Linux server is not fully documented. Marcus only confirmed migration work on `ehr-srv-01`.
- MFA is not deployed organization-wide. The only documented MFA use is James Chen's personal account.

### Medical Device and IoT Gaps

- The MRI scanner is documented as critical and running Windows XP, but the referenced separate file is not included in the onboarding packet.
- The CT scanner operating system is unknown.
- The security posture of Philips IntelliVue patient monitors has not been assessed.
- The security posture of BD Alaris infusion pumps has not been assessed.
- Medical devices appear to be reachable on the same Central network as other systems, but there is no completed segmentation or access-control review.
- The nurse call system is IP-based and integrated with the phone system, but its network placement, authentication model and resilience are not documented.

### Cloud and Third-Party Service Gaps

- O365 is the main documented cloud service, but Marcus suspected that individual departments may use additional cloud services.
- No complete cloud service inventory exists.
- The security configuration of O365 is not described in the packet.
- Vendor access arrangements for MedTech Solutions, Greenfield Building Management, ClearView Security or other service providers are not documented.
- The scope of Fortinet, Sophos and Veeam support contracts is listed financially but not mapped to operational responsibilities or security requirements.

### Governance, Compliance and Process Gaps

- The CISO role is vacant.
- James Chen is responsible for security leadership in practice, but he does not have direct authority over IT operations.
- Security policy authority and IT operational control are separated between James Chen and Sarah Park, creating potential execution friction.
- HIPAA Security Rule compliance has never been formally assessed.
- Legal states that the organization is compliant, but no evidence is documented in the packet.
- No formal incident response plan exists.
- No business continuity plan exists.
- No disaster recovery plan exists.
- There is no documented procedure for clinical operations if Central loses power beyond the estimated UPS capacity of approximately 20 minutes.

### Backup and Recovery Gaps

- Backups run nightly to a local NAS, but restore testing is not documented.
- Backup retention periods are not documented.
- Backup access controls are not documented.
- The backup NAS is located in the same server room, rack and network as the systems it protects.
- Offsite or cloud backup was proposed but denied due to budget.
- Recovery priorities for critical services are not documented.

### Incident and System Health Gaps

- `billing-srv-01` has recurring performance issues, but the root cause is unknown.
- Marcus left a note stating that something is wrong with `billing-srv-01`.
- Marcus notes that a ransomware incident affected `billing-srv-01` in January, but no formal incident record, lessons learned report or remediation status is included.
- The current compromise status of `billing-srv-01` is unknown.
- It is unclear whether systems affected by the January ransomware incident were fully rebuilt, cleaned, monitored or only restarted.

### Physical Security Gaps

- Server room badge access reportedly uses the same generic badge everyone receives.
- There are no documented cameras in the server room corridor.
- Central has guard service only at the main entrance, Monday-Friday from 7AM to 7PM.
- There is no documented weekend or night guard coverage at Central.
- There is no guard service documented for Westside or HQ.
- Westside has weak physical security for IT equipment, and the server closet reportedly does not lock.
