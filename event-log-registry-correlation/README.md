# Correlating Event Logs with Registry Hives Using GKAPE and Eric Zimmerman Tools

Digital forensic examination demonstrating recovery of data from a formatted USB flash drive using Magnet AXIOM Process and AXIOM Examine.

## Case Information

| Field | Value |
|---|---|
| **Case Reference** | DFIR-2026-RMF-011 |
| **Evidence Source** | Live acquisition, local system drive (C:\) |
| **Investigator** | Julian Derry |
| **Date of Analysis** | February 09, 2026 |
| **Time of Analysis** | 4:45 PM GMT |
| **Platform** | Windows |
| **Tools** | GKAPE, EvtxECmd, RECmd |

## Table of Contents

1. [Objectives](#1-objectives)
2. [Phase 1: Collect Evidence with GKAPE](#2-phase-1-collect-evidence-with-gkape)
3. [Phase 2: Verify Evidence Collection](#3-phase-2-verify-evidence-collection)
4. [Phase 3: Parse the Event Logs and Registry Hives](#4-phase-3-parse-the-event-logs-and-registry-hives)
5. [Normal Startup vs Unexpected Shutdown](#5-normal-startup-vs-unexpected-shutdown)
6. [Correlating Event Viewer with Parsed Results](#6-correlating-event-viewer-with-parsed-results)
7. [Findings](#7-findings)
8. [Conclusion](#8-conclusion)

## 1. Objectives

The purpose of this exercise is for me to correlate Windows Event Logs with Registry Hive artifacts to reconstruct system activity. My objectives are to:

- Establish timelines of system startup, shutdown, and restart events.
- Correlate user activity with recorded system events.
- Identify abnormal or unexpected shutdowns.
- Verify whether system reboots were user initiated.

## 2. Phase 1: Collect Evidence with GKAPE

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

## 8. Conclusion

Using GKAPE, EvtxECmd, and RECmd, I successfully collected and parsed the Windows Event Logs and Registry Hives. By correlating the original Event Viewer records with the EvtxECmd output, I verified the consistency of the evidence and reconstructed the system timeline. My analysis identified both normal reboot activity and an unexpected shutdown, providing a reliable forensic timeline for system activity.
