# Correlating Event Logs with Registry Hives Using GKAPE and Eric Zimmerman Tools

Digital forensic examination demonstrating recovery of data from a formatted USB flash drive using Magnet AXIOM Process and AXIOM Examine.

## Case Information

| Field | Value |
|---|---|
| **Case Reference** | DFIR-2026-ELRC-011 |
| **Evidence Source** | Live acquisition, local system drive (C:\) |
| **Investigator** | Julian Derry |
| **Date of Analysis** | February 09, 2026 |
| **Time of Analysis** | 4:45 PM GMT |
| **Platform** | Windows |
| **Tools** | GKAPE, EvtxECmd, RECmd |

## Table of Contents

1. [Objectives](#1-objectives)
2. [Table of Evidence Sources](#2-table-of-evidence-sources)
3. [Phase 1: Collect Evidence with GKAPE](#3-phase-1-collect-evidence-with-gkape)
4. [Phase 2: Verify Evidence Collection](#4-phase-2-verify-evidence-collection)
5. [Evidence Integrity Verification](#5-evidence-integrity-verification)
6. [Phase 3: Parse Event Logs and Registry Hives](#6-phase-3-parse-event-logs-and-registry-hives)
7. [Normal Startup vs Unexpected Shutdown](#7-normal-startup-vs-unexpected-shutdown)
8. [Correlating Event Viewer with Parsed Results](#8-correlating-event-viewer-with-parsed-results)
9. [Correlation of Event Logs and Registry Evidence](#9-correlation-of-event-logs-and-registry-evidence)
10. [Findings](#10-findings)
11. [Post-Examination Integrity Verification](#11-post-examination-integrity-verification)
12. [Conclusion](#12-conclusion)

## 1. Objectives

The purpose of this exercise is for me to correlate Windows Event Logs with Registry Hive artifacts to reconstruct system activity. My objectives are to:

- Establish timelines of system startup, shutdown, and restart events.
- Correlate user activity with recorded system events.
- Identify abnormal or unexpected shutdowns.
- Verify whether system reboots were user initiated.

## 2. Table of Evidence Sources

| Evidence Source | Acquisition Tool | Hashing Tool |
|---|---|---|
| **C:** | GKAPE | PowerShell |

I ran GKAPE as Administrator and selected my source to be `C:\`.

Next, I selected my destination folders for both Targets and Modules.

| Targets | Modules |
|---|---|
| KapeTriage | !EZParser |
| Windows Event Logs | |

<img width="1918" height="1026" alt="Screenshot 2026-08-26 112234" src="https://github.com/user-attachments/assets/b0b662f8-dd62-409a-9fb2-de1678b4e294" />

<img width="1086" height="615" alt="Screenshot 2026-08-26 112450" src="https://github.com/user-attachments/assets/2cecf911-7e4a-4914-b79f-529b70892f40" />

**GKAPE Output Folder**

<img width="769" height="329" alt="Screenshot 2026-08-26 114236" src="https://github.com/user-attachments/assets/f41368c9-3cae-461f-b25f-571f27a0b9cf" />

<img width="768" height="456" alt="Screenshot 2026-08-26 114244" src="https://github.com/user-attachments/assets/e0fbc869-2908-4dc6-a414-b19528bce273" />


**GKAPE Output Folder Hashed using PowerShell**

<img width="1115" height="283" alt="Screenshot 2026-08-27 105516" src="https://github.com/user-attachments/assets/4a7a43b9-3df0-4aa2-a771-7430ab07edf2" />


## 3. Phase 2: Verify Evidence Collection

I confirmed that the required evidence was successfully acquired in the following directories:

1. `C:\Windows\System32\winevt\Logs` to verify Event Logs.
2. `C:\Windows\System32\config` to verify Registry Hives.
3. `C:\Users\CyberSamurai\NTUSER.DAT` to verify the user registry hive.

**Evidence Verification**

Event Logs folder

<img width="784" height="785" alt="Screenshot 2026-08-26 114807" src="https://github.com/user-attachments/assets/aedf79de-8d24-416d-a2d3-e85beb0ea525" />


Registry Hives (SYSTEM, SOFTWARE, SAM, SECURITY)

<img width="777" height="788" alt="Screenshot 2026-08-26 114819" src="https://github.com/user-attachments/assets/35eff785-d9bd-4e84-bcdd-150a6a182a8b" />

NTUSER.DAT confirmation

<img width="768" height="552" alt="Screenshot 2026-08-26 114844" src="https://github.com/user-attachments/assets/71006f59-9764-4f61-b9a7-d86d0d7b5d3a" />

## 4. Phase 3: Parse the Event Logs and Registry Hives

**Step 1: Parse Event Logs**

I ran EvtxECmd as Administrator from Command Prompt to parse the collected `.evtx` files into CSV format.

<img width="1330" height="1032" alt="Screenshot 2026-08-26 122046" src="https://github.com/user-attachments/assets/f64231d5-f28f-4486-9647-4934bd2e5e74" />

**Step 2: Parse the Registry Hive**

I ran RECmd against the collected NTUSER.DAT hive to extract user activity artifacts.

## 5. Normal Startup vs Unexpected Shutdown

**Expected Normal Sequence**

I identified the normal boot-and-shutdown timeline as: **12 → 6005 → Normal Operation → 1074/13 → 6006**.

| Event ID | Description |
|---|---|
| 12 | Operating System Started |
| 6005 | Event Log Service Started |
| 13 | Shutdown Initiated |
| 6006 | Event Log Service Stopped |

**What I Observed**

| Event ID | Date & Time | Interpretation |
|---|---|---|
| 12 | 6/26/2026 10:49:32 AM | Operating system started |
| 6005 | 6/26/2026 10:49:32 AM | Event Log service started |
| 41 | 6/26/2026 10:49:37 AM | Unexpected power loss |
| 6008 | 6/26/2026 10:49:58 AM | Unexpected shutdown |

<img width="1402" height="814" alt="Screenshot 2026-08-26 160602 - Copy" src="https://github.com/user-attachments/assets/07ce9443-50dd-4791-bb02-f86c00023f3f" />

From this, I noted that:

- Event ID 13 is absent.
- Event ID 6006 is absent.
- Event IDs 41 and 6008 indicate an abnormal shutdown rather than a clean shutdown sequence.

## 6. Correlating Event Viewer with Parsed Results

I filtered Event Viewer using the Date, Time, and Event ID fields, then compared the filtered results against the parsed output generated by EvtxECmd to verify that both sources reported identical events and timestamps.

**Figure 7. Event Viewer Filter Configuration**

Filter configured by Date, Time and Event ID

<img width="1916" height="1031" alt="Screenshot 2026-08-26 161701" src="https://github.com/user-attachments/assets/ed3a9418-2833-4d54-837a-1b186e3d4bd8" />

**Figure 8. Event Viewer Filtered Results**

Filtered Event Viewer results showing Event IDs 12, 41, 6005 and 6008

<img width="1918" height="1026" alt="Screenshot 2026-08-26 161848" src="https://github.com/user-attachments/assets/922d927a-95c2-42b0-bc45-1298fdc5c3f3" />

I compared the timestamps in Event Viewer against the EvtxECmd CSV output. The Event ID, date, and time matched exactly, confirming the integrity of the parsed evidence.

## 7. Findings

**Objective 1: Establish Timelines**
Status: Achieved
I used the combination of Event IDs 12, 6005, 41, 6008, and 1074 to establish the sequence of startup, shutdown, and reboot activity.

**Objective 2: Prove User Activity**
Status: Partially Achieved
I found that Event ID 1074 records that the Windows account `MX50\CyberSamurai` initiated a restart on 3 Jun 2026 at 10:51:41 PM. This associates a user account with the reboot event.

**Objective 3: Detect Abnormal Shutdowns**
Status: Achieved
I determined that Event ID 41 followed by 6008, together with the absence of 13 and 6006, demonstrates that Windows experienced an unexpected shutdown.

**Objective 4: Verify Reboots**
Status: Achieved
I confirmed that Event ID 1074 shows a graceful, user-initiated restart and provides the exact timestamp of the reboot.

<img width="1402" height="255" alt="Screenshot 2026-08-26 160602" src="https://github.com/user-attachments/assets/3b514dd8-82d7-4eae-9d5f-d2549e2a8142" />

## 8. Post-Examination Integrity Verification

<img width="1110" height="454" alt="Screenshot 2026-08-27 105611" src="https://github.com/user-attachments/assets/14d0efe2-7072-407d-912a-0f935d68adc3" />


## 8. Conclusion

This examination demonstrated the collection, verification, parsing, and correlation of Windows Event Logs and Registry Hives using GKAPE, EvtxECmd, and RECmd.

GKAPE was used to acquire the relevant Windows forensic artifacts. EvtxECmd was used to parse the Windows Event Logs, while RECmd was used to examine the Registry Hives.

The Event Log analysis established evidence of system startup, unexpected shutdown activity, and a user-associated restart. Event IDs 41 and 6008 indicated an unexpected shutdown, while Event ID 1074 identified MX50\CyberSamurai as the account associated with a restart on 3 Jun 2026 at 10:51:41 PM.

The original Event Viewer records were also compared with the parsed EvtxECmd output to validate the identified Event IDs and timestamps.

The correlation of Event Logs and Registry evidence provides a stronger forensic reconstruction than relying on either evidence source independently. However, conclusions regarding malicious tampering or physical user presence require additional supporting evidence and should not be inferred from the Event IDs alone.
