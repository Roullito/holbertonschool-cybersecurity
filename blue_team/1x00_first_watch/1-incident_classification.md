# Incident Classification Using the CIA Triad

## Classification Approach

This document classifies six security-relevant incidents using the CIA Triad:

- **Confidentiality**: information was accessed by someone who should not have seen it.
- **Integrity**: information or a system was modified without authorization or became unreliable.
- **Availability**: a service, system or data became inaccessible when it was needed.

The classification is based on the facts provided in the incident log. Potential impacts are only mentioned when they are useful for context, but the primary and secondary CIA pillars are assigned based on confirmed evidence.

## Incident Classification Table

| Incident | Primary CIA Pillar Impacted | Primary Justification | Secondary CIA Pillar Impacted | Secondary Justification |
| --- | --- | --- | --- | --- |
| Incident A: Ransomware on `billing-srv-01` | Availability | The billing server was encrypted by ransomware and the finance team could not process insurance claims for 4 days. | Integrity | The ransomware modified server data by encrypting it without authorization, and the available backup was 3 weeks old because of a misconfigured cron job. |
| Incident B: Patient portal broken access control | Confidentiality | Authenticated patients could view other patients' lab results by modifying a URL parameter, which exposed patient information to unauthorized users. | None confirmed | The incident log does not state that lab results were modified or that the patient portal became unavailable. |
| Incident C: Incorrect pharmacy dosage values | Integrity | A buggy database update script overwrote dosage values, causing the pharmacy management system to display incorrect medication dosages across all three sites. | Availability | Correct dosage information was not reliably available in the system for approximately 6 hours, forcing staff to rely on external validation such as printed references. |
| Incident D: Public website defacement | Integrity | The public-facing website homepage was modified without authorization and replaced with a political message. | Availability | The legitimate homepage content was unavailable to users until the site was restored from backup within 2 hours. |
| Incident E: EHR outage during database migration | Availability | The EHR system was unavailable for 9 hours during a planned database migration, forcing physicians to use paper records. | None confirmed | The incident log does not indicate that patient data was exposed or modified; the confirmed impact was loss of access to the EHR service. |
| Incident F: Intern laptop on internal network | Confidentiality | An unmanaged personal laptop was connected to the internal corporate WiFi for 3 weeks and had access to the same network segment as the HR file share. | None confirmed | The incident created a confidentiality exposure, but the log does not confirm that HR files were accessed, modified or made unavailable. |

## Detailed Notes

### Incident A: Ransomware on `billing-srv-01`

The primary impact is **Availability** because the billing service was unavailable for 4 days and the finance team could not process insurance claims.

The secondary impact is **Integrity** because ransomware encrypts data without authorization, making the original data state unreliable or unusable. The 3-week-old backup increases the integrity concern because restored data may not represent the most recent valid business state.

A confidentiality impact is possible in real ransomware cases because attackers may exfiltrate data before encryption, but this incident log does not confirm that data was copied or disclosed. Therefore, confidentiality should not be classified as an impacted pillar based on the available evidence.

### Incident B: Patient Portal Broken Access Control

The primary impact is **Confidentiality** because patients could access other patients' lab results by modifying a URL parameter.

This is a direct unauthorized disclosure of sensitive patient information. There is no confirmed integrity impact because the log does not say that patients could modify lab results. There is also no confirmed availability impact because the portal remained accessible.

### Incident C: Incorrect Pharmacy Dosage Values

The primary impact is **Integrity** because medication dosage data was overwritten by a faulty database update script. The system displayed incorrect values, meaning the data could no longer be trusted.

The secondary impact is **Availability** because accurate dosage information was not reliably available in the pharmacy system for approximately 6 hours. Even though the error was caught by a pharmacist, the potential clinical impact could have been severe if incorrect dosages had been used for patient care.

### Incident D: Public Website Defacement

The primary impact is **Integrity** because the website content was changed without authorization.

The secondary impact is **Availability** because the legitimate homepage content was unavailable until the site was restored from backup. The website did not contain patient data, so there is no confirmed confidentiality impact.

### Incident E: EHR Outage During Database Migration

The primary impact is **Availability** because the EHR system was unavailable for 9 hours.

The use of paper records was a workaround, not proof that the EHR service remained available. The failed rollback also shows a weakness in change management and recovery planning, but there is no confirmed confidentiality or integrity impact in the incident log.

### Incident F: Intern Laptop on Internal Network

The primary impact is **Confidentiality** because an unmanaged personal laptop was connected to the internal corporate WiFi and had network-level access to the same segment as the HR file share.

The log does not confirm that HR files were accessed, modified or deleted. Therefore, integrity and availability are not confirmed impacts. Integrity would become relevant only if unauthorized modification of HR data or network traffic manipulation were observed.

## Summary

The incidents show that MedDefense has experienced confirmed impacts across all three CIA pillars:

- **Availability** was the primary impact in the ransomware incident and the EHR outage.
- **Confidentiality** was the primary impact in the patient portal access control issue and the unmanaged intern laptop incident.
- **Integrity** was the primary impact in the pharmacy dosage error and the public website defacement.

The most operationally disruptive incidents were the ransomware incident on `billing-srv-01` and the EHR outage. The most sensitive confidentiality incident was the patient portal broken access control issue because it exposed patient lab results to unauthorized authenticated users.

A key lesson from these incidents is that CIA classification must be based on confirmed facts. Possible impacts, such as ransomware-related data exfiltration or unauthorized modification from an unmanaged laptop, should be documented as risks or investigative concerns, but not classified as confirmed CIA impacts unless evidence supports them.
