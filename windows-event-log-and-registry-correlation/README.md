<div align="center">

# Correlating Event Logs with Registry Hives Using GKAPE & Eric Zimmerman Tools

**Digital Forensics | Windows DFIR | Event Log Analysis**

![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Windows_11-0078D6?style=for-the-badge)
![Tools](https://img.shields.io/badge/Tools-GKAPE%20%7C%20EvtxECmd%20%7C%20RECmd-black?style=for-the-badge)

</div>

---

---

# Case Information

| Field | Value |
|---|---|
| **Case Reference** | DFIR-2026-ELRC-011 |
| **Investigator** | Julian Derry |
| **Date of Analysis** | 26 August 2026 |
| **Prepared For** | Public Portfolio |
| **Platform** | Windows |
| **Category** | Digital Forensics / DFIR |
| **Tools Used** | GKAPE, EvtxECmd, RECmd |

---

# Table of Contents

- [Objectives](#objectives)
- [Evidence Sources](#evidence-sources)
- [Evidence Collection](#evidence-collection)
- [Evidence Verification](#evidence-verification)
- [Parsing the Evidence](#parsing-the-evidence)
- [Startup vs Unexpected Shutdown](#startup-vs-unexpected-shutdown)
- [Correlation Analysis](#correlation-analysis)
- [Findings](#findings)
- [Integrity Verification](#integrity-verification)
- [Conclusion](#conclusion)

---

# Objectives

The purpose of this investigation was to reconstruct Windows system activity by correlating Event Logs with Registry Hive artifacts.

### Objectives

- Establish startup, shutdown, and reboot timelines.
- Correlate user activity with system events.
- Detect abnormal or unexpected shutdowns.
- Verify whether system reboots were user initiated.

---

# Evidence Sources

| Evidence | Acquisition | Hash Verification |
|---|---|---|
| C: Drive | GKAPE | PowerShell SHA-256 |
| Windows Event Logs | GKAPE | PowerShell SHA-256 |
| Registry Hives | GKAPE | PowerShell SHA-256 |
| NTUSER.DAT | GKAPE | PowerShell SHA-256 |

## Acquisition Configuration

| Targets | Modules |
|---|---|
| KapeTriage | !EZParser |
| Windows Event Logs | RECmd Compatible |

### Screenshot: GKAPE Configuration

<img width="1918" height="1026" alt="evidence01" src="https://github.com/user-attachments/assets/4c95f85c-ad37-487b-95e1-2bde085f2694" />

<img width="1086" height="615" alt="evidence02" src="https://github.com/user-attachments/assets/ba9ce402-e6b9-4e2c-90b2-d5bab2224484" />


### Screenshot: GKAPE Output Folder

<img width="769" height="329" alt="evidence03" src="https://github.com/user-attachments/assets/486b0b6a-1f5e-47c6-b7ca-c1de670b4f66" />
<img width="768" height="456" alt="evidence04" src="https://github.com/user-attachments/assets/a1824e80-d129-4278-9b77-482cc6001039" />



### Screenshot: Hash Verification

<img width="1115" height="283" alt="evidence05" src="https://github.com/user-attachments/assets/53e081dd-c761-4ac7-92ce-6134cfb6fd29" />

---

# Evidence Collection

I executed GKAPE with administrative privileges and collected forensic artifacts from the Windows system.

### Collected Locations

| Artifact | Location |
|---|---|
| Event Logs | `C:\Windows\System32\winevt\Logs` |
| SYSTEM Hive | `C:\Windows\System32\config\SYSTEM` |
| SOFTWARE Hive | `C:\Windows\System32\config\SOFTWARE` |
| SAM Hive | `C:\Windows\System32\config\SAM` |
| SECURITY Hive | `C:\Windows\System32\config\SECURITY` |
| User Hive | `C:\Users\CyberSamurai\NTUSER.DAT` |

---

# Evidence Verification

Each required artifact was verified before analysis.

## Windows Event Logs

<img width="784" height="785" alt="evidence06" src="https://github.com/user-attachments/assets/dd9cd14d-c931-4b97-a89e-95abf25d73d3" />

## Registry Hives

<img width="777" height="788" alt="evidence07" src="https://github.com/user-attachments/assets/f6bd7443-81f3-4075-abc4-a3790b17155c" />

## NTUSER.DAT

<img width="768" height="552" alt="evidence08" src="https://github.com/user-attachments/assets/bbf51b4c-c190-41de-81f9-a68414a5941a" />

---

# Parsing the Evidence

## Step 1. Parse Event Logs

I used **EvtxECmd** to convert the collected `.evtx` files into structured CSV output for timeline analysis.

### Example Command

```powershell
EvtxECmd.exe -d "EventLogs" --csv "Output"
```

### Output Screenshot

<img width="1330" height="1032" alt="evidence09" src="https://github.com/user-attachments/assets/5c7bfa02-dbc1-476b-acda-382d7e211cb4" />

---

## Step 2. Parse Registry Hive

I parsed the user registry hive using **RECmd** to extract user activity artifacts.

### Example Command

```powershell
RECmd.exe -f NTUSER.DAT --csv Output
```
---

# Startup vs Unexpected Shutdown

## Expected Windows Shutdown Sequence

| Event ID | Description |
|---|---|
| **12** | Operating System Started |
| **6005** | Event Log Service Started |
| **13** | Shutdown Initiated |
| **6006** | Event Log Service Stopped |

This represents a **normal, graceful shutdown**.

---

## Observed Timeline

| Event ID | Timestamp | Interpretation |
|---|---|---|
| **12** | 26 Jun 2026 10:49:32 | Operating system started |
| **6005** | 26 Jun 2026 10:49:32 | Event Log service started |
| **41** | 26 Jun 2026 10:49:37 | Unexpected power loss |
| **6008** | 26 Jun 2026 10:49:58 | Unexpected shutdown |

<img width="1402" height="814" alt="evidence10" src="https://github.com/user-attachments/assets/63979de2-eb77-4efd-a6c0-e981db722ee6" />

### Timeline Visualization

```text
10:49:32  Event 12     Windows Started
      │
      ▼
10:49:32  Event 6005   Event Log Service Started
      │
      ▼
10:49:37  Event 41     Kernel-Power (Unexpected)
      │
      ▼
10:49:58  Event 6008   Unexpected Shutdown Recorded
```

### Key Observations

- ❌ Event ID **13** was absent.
- ❌ Event ID **6006** was absent.
- ✅ Events **41** and **6008** indicate an abnormal shutdown sequence.

---

# Correlation Analysis

To validate the parsed evidence, I compared the original Event Viewer records against the CSV output generated by EvtxECmd.

## Event Viewer Filter

<img width="1916" height="1031" alt="evidence11" src="https://github.com/user-attachments/assets/7d186ab2-12f7-4541-a184-1101ad596156" />

## Filtered Results

<img width="1918" height="1026" alt="evidence12" src="https://github.com/user-attachments/assets/ceba6c6a-bd57-4c93-ab85-e01d2265d07f" />

### Correlation Summary

| Source | Result |
|---|---|
| Event Viewer | Event IDs 12, 41, 6005, 6008 |
| EvtxECmd CSV | Identical IDs and timestamps |
| Verification | Match Confirmed |

The timestamps matched exactly, confirming that the parsed CSV accurately represented the original Windows Event Logs.

---

# Findings

## Objective 1. Establish Timelines

**Status:** ✅ Achieved

Using Event IDs **12, 6005, 41, 6008, and 1074**, I reconstructed the sequence of startup, shutdown, and reboot activity.

---

## Objective 2. Correlate User Activity

**Status:** 🟡 Partially Achieved

Event ID **1074** recorded that the Windows account **MX50\\CyberSamurai** initiated a restart on **3 Jun 2026 at 10:51:41 PM**.

This associates a specific user account with the reboot event.

<img width="1402" height="255" alt="evidence13" src="https://github.com/user-attachments/assets/d1895c07-14d5-46da-b763-0de2ae8a4834" />

---

## Objective 3. Detect Abnormal Shutdowns

**Status:** ✅ Achieved

The combination of:

- Event ID **41**
- Event ID **6008**
- Missing Event IDs **13** and **6006**

demonstrates that Windows experienced an **unexpected shutdown** rather than a graceful shutdown.

---

## Objective 4. Verify Reboots

**Status:** ✅ Achieved

Event ID **1074** confirmed a user initiated restart and provided the exact reboot timestamp.

---

# Integrity Verification

All acquired evidence was hashed immediately after collection using PowerShell.

| Item | Verification |
|---|---|
| Event Logs | SHA-256 Verified |
| Registry Hives | SHA-256 Verified |
| NTUSER.DAT | SHA-256 Verified |
| Evidence Set | Integrity Maintained |

### Screenshot Placeholder

<img width="1110" height="454" alt="evidence14" src="https://github.com/user-attachments/assets/47e742fc-2e30-464c-8a05-a8b438cced62" />

---

# Conclusion

This project demonstrates a complete DFIR workflow for correlating Windows Event Logs with Registry Hive artifacts using **GKAPE**, **EvtxECmd**, and **RECmd**.

The investigation established:

- A verified startup timeline
- Evidence of an unexpected shutdown
- A user associated restart event
- Validation of parsed artifacts against the original Event Viewer records

The correlation of Event Logs and Registry evidence provides a stronger forensic reconstruction than relying on either artifact independently. While the identified events accurately describe system behavior, they do **not** independently prove malicious activity or physical user presence without additional supporting evidence.

---

# Repository Structure

```text
DFIR-Windows-EventLog-Registry-Correlation/
│
├── README.md
├── LICENSE
├── images/
│   ├── 01_gkape_output.png
│   ├── 02_gkape_configuration.png
│   ├── 03_gkape_output_folder.png
│   ├── 04_hash_verification.png
│   ├── 05_event_logs_verification.png
│   ├── 06_registry_hives.png
│   ├── 06b_ntuser_confirmation.png
│   ├── 07_event_viewer_filter.png
│   ├── 08_evtxecmd_csv.png
│   ├── 09_recmd_output.png
│   ├── 10_event_viewer_filter.png
│   ├── 11_filtered_results.png
│   └── 12_integrity_verification.png
│
├── evidence/
│   ├── EventLogs/
│   ├── Registry/
│   └── Hashes/
│
├── output/
│   ├── EvtxECmd_Output.csv
│   └── RECmd_Output.csv
│
└── docs/
    └── Case_Report.pdf
```

---

## Author

**Julian Derry**

Digital Forensics • DFIR • Incident Response
