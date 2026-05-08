# Incident Classification Using the CIA Triad

## Classification Approach

This document classifies six MedDefense security incidents using the CIA Triad:

- **Confidentiality**: information was accessed by someone who should not have seen it.
- **Integrity**: information or a system was modified without authorization or became unreliable.
- **Availability**: a service, system or data became inaccessible when it was needed.

The classifications below are based only on the confirmed facts provided in the incident log.

## Incident Classification Table

| Incident | Primary CIA Pillar Impacted | One-Sentence Justification | Secondary CIA Pillar Impacted | Secondary Explanation |
| --- | --- | --- | --- | --- |
| Incident A | Availability | The ransomware encrypted `billing-srv-01`, preventing the finance team from processing insurance claims for 4 days. | Integrity | The ransomware altered server data by encrypting it without authorization, and the usable backup was 3 weeks old. |
| Incident B | Confidentiality | The broken access control allowed authenticated patients to view other patients' lab results by modifying a URL parameter. | None | The log does not state that lab results were modified or that the portal became unavailable. |
| Incident C | Integrity | A faulty database update script overwrote dosage values, causing the pharmacy system to display incorrect medication dosages. | Availability | Accurate dosage information was not reliably available in the system for approximately 6 hours. |
| Incident D | Integrity | The public website homepage was modified without authorization and replaced with a political message. | Availability | The legitimate homepage content was unavailable until the site was restored from backup within 2 hours. |
| Incident E | Availability | The EHR system was unavailable for 9 hours during a planned database migration, forcing physicians to use paper records. | None | The log does not state that patient data was exposed or modified. |
| Incident F | Confidentiality | An unmanaged personal laptop was connected to the internal corporate WiFi and had access to the same network segment as the HR file share. | None | The log does not confirm that HR files were modified or made unavailable. |

## Summary

The incidents demonstrate confirmed impacts across all three CIA pillars:

- **Availability** was the primary impact for Incident A and Incident E.
- **Confidentiality** was the primary impact for Incident B and Incident F.
- **Integrity** was the primary impact for Incident C and Incident D.

Possible additional impacts should be investigated separately, but this classification only assigns CIA pillars based on confirmed evidence from the incident log.
