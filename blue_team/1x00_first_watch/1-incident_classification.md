# Incident Classification Using the CIA Triad

| Incident | Primary CIA Pillar Impacted | One-Sentence Justification | Secondary CIA Pillar Impacted | Secondary Explanation |
| --- | --- | --- | --- | --- |
| Incident A | Availability | The ransomware encrypted the billing server and prevented the finance team from processing insurance claims for 4 days. | Integrity | The ransomware changed the state of the billing data by encrypting it, and the most recent usable backup was 3 weeks old. |
| Incident B | Confidentiality | The broken access control allowed authenticated patients to view other patients' lab results by modifying a URL parameter. | None | No unauthorized modification or service outage is described in the incident log. |
| Incident C | Integrity | A faulty database update script overwrote medication dosage values, causing the pharmacy system to display incorrect dosages. | Availability | Accurate dosage information was not reliably available in the system for approximately 6 hours. |
| Incident D | Integrity | The public website homepage was modified without authorization and replaced with a political message. | Availability | The legitimate homepage content was unavailable until the website was restored from backup within 2 hours. |
| Incident E | Availability | The EHR system was unavailable for 9 hours during a planned database migration, forcing physicians to use paper records. | None | The incident log does not describe unauthorized data disclosure or unauthorized data modification. |
| Incident F | Confidentiality | An unmanaged personal laptop on the internal corporate WiFi had access to the same network segment as the HR file share, exposing sensitive HR resources to an unauthorized device. | None | The log does not confirm that HR data was modified or made unavailable. |
