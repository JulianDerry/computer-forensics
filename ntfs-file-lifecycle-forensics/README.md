# Forensic Lifecycle of a File Created Using CLI
> **Stage 2**

---

## Collaboration Note

This report is part of a collaborative digital forensics series between **Julian Derry** and **Maxwell B. Antwi (TraceHunter)** documenting the forensic lifecycle of a file.

The objective is to demonstrate how the same outcome, file creation, can originate from two different user workflows and how each leaves a distinct evidential trail.

- **Maxwell B. Antwi:** GUI-based file creation and graphical user interaction artifacts.
- **Julian Derry:** CLI-based file creation and corroboration using PowerShell, `ConsoleHost_history.txt`, `$MFT`, `$UsnJrnl`, and `$LogFile`.

Together, the series shows how GUI and CLI workflows converge on the same underlying NTFS evidence while producing different contextual artifacts.

### Stage 2 Scope

This report documents the **CLI workflow**.

The file was created using PowerShell without opening the file or using File Explorer. The resulting evidence was then acquired, parsed, hashed, and correlated across multiple forensic artifacts.

---

## Table of Contents

1. [Collaboration Note](#collaboration-note)
2. [Case Information](#case-information)
3. [Step 1: Objective](#step-1-objective)
   - [Investigation Question](#investigation-question)
4. [Step 2: Build the Lab](#step-2-build-the-lab)
   - [Lab Environment](#lab-environment)
   - [File Creation Command](#file-creation-command)
   - [Creation Details](#creation-details)
5. [Step 3: Acquire Evidence](#step-3-acquire-evidence)
   - [File System Artifacts](#file-system-artifacts)
   - [Registry Artifacts](#registry-artifacts)
   - [Event Logs](#event-logs)
   - [PowerShell History](#powershell-history)
6. [Step 4: Parse the Artifacts](#step-4-parse-the-artifacts)
   - [EZParser Workflow](#ezparser-workflow)
7. [Step 4.1: Evidence Integrity](#step-41-evidence-integrity)
   - [Hash Verification](#hash-verification)
8. [Step 5: Correlate the Timeline](#step-5-correlate-the-timeline)
9. [Step 6: Artifact Findings](#step-6-artifact-findings)
   - [Parsed `$MFT`](#parsed-mft)
   - [Parsed `$UsnJrnl`](#parsed-usnjrnl)
   - [Parsed `$LogFile`](#parsed-logfile)
   - [`ConsoleHost_history.txt`](#consolehost_historytxt)
10. [Step 7: Event Viewer Analysis](#step-7-event-viewer-analysis)
11. [Conclusion](#conclusion)

---

## Case Information

| Property | Value |
|---|---|
| **Case Reference No.** | `DFIR-2026-FFLC` |
| **Investigator** | Julian Derry |
| **Date of Analysis** | September 16, 2026 |
| **Prepared For** | Public |
| **Platform** | Windows |
| **Operating System** | Windows 11 |
| **Tools** | gKAPE, Eric Zimmerman Tools |

---

# Step 1: Objective

## Investigation Question

**Can I prove that a file was created from the Command Line Interface (CLI), and distinguish that workflow from GUI-based file creation?**

This report documents the **CLI path** of a file's forensic lifecycle as part of a collaborative series.

The GUI workflow is documented separately. This investigation focuses exclusively on evidence generated through PowerShell and the resulting NTFS activity.

### Evidence Model

The investigation focuses on two categories of evidence:

1. **Contextual evidence**
   - `ConsoleHost_history.txt`
   - PowerShell activity

2. **File-system evidence**
   - `$MFT`
   - `$UsnJrnl`
   - `$LogFile`

The objective is to determine whether these independent sources can be correlated to the same file creation event.

---

# Step 2: Build the Lab

A Windows 11 virtual machine was used for this experiment.

A folder structure was created using PowerShell only, without interacting with File Explorer.

This ensured the resulting artifacts represented a CLI workflow rather than a graphical file creation workflow.

## Lab Environment

| Property | Value |
|---|---|
| **Platform** | Windows 11 Virtual Machine |
| **User Account** | `CyberSamurai` |
| **Workflow** | Command Line Interface |
| **Shell** | PowerShell |
| **File System** | NTFS |
| **File Name** | `client_report.txt` |
| **Creation Method** | PowerShell CLI |

---

## File Creation Command

The file was created using the following PowerShell command:

```powershell
echo Confidential report > client_report.txt
```
<img width="1919" height="1079" alt="Screenshot 2026-09-16 211030" src="https://github.com/user-attachments/assets/5218539e-c08b-48be-9ec7-3437ec4b3cff" />


## Step 3. Acquire Evidence

Evidence was acquired using **gKAPE**.

Rather than collecting individual artifacts manually, the **KapeTriage** target was selected to preserve the core Windows forensic artifacts required for timeline reconstruction.

### File System Artifacts

The following NTFS artifacts were collected:

- `$MFT`
- `$LogFile`
- `$UsnJrnl`

These artifacts establish the physical creation of the file within the NTFS file system.

### Registry Artifacts

KapeTriage also collects the needed files such as UserAssit, RecentDocs and ShellBags. Little or no activity if the workflow stayed in CLI. That absence becomes meaningful.

### Event Logs

The following logs were collected:

- `Security.evtx`
- `System.evtx`
- `Microsoft-Windows-PowerShell/Operational.evtx`

For redundancy, the **EventLogs** target was acquired separately in addition to KapeTriage.

### PowerShell History

The **ConsoleHost_history.txt** artifact was collected to preserve the commands executed during the PowerShell session.

<img width="1919" height="1079" alt="Screenshot 2026-09-16 211723" src="https://github.com/user-attachments/assets/6e7ce0e9-e7e2-4692-8eff-aafb13efea73" />

<img width="1919" height="1079" alt="Screenshot 2026-09-16 211750" src="https://github.com/user-attachments/assets/3798c5e5-a9cd-46e5-8469-84f78948afcf" />


Instead of collecting the system files individually, I selected KapeTriage, which collects all the system files. This is to establish physical creation.

---

## Step 4. Parse the Artifacts

All collected targets were parsed using Eric Zimmerman’s EZParser modules.

The parsed output produced structured CSV files for timeline correlation across NTFS, Registry, and Event Log artifacts.

<img width="1919" height="1079" alt="Screenshot 2026-09-16 213557" src="https://github.com/user-attachments/assets/8aa17052-a153-483a-8247-f7acad8ecc1e" />

<img width="1919" height="1079" alt="Screenshot 2026-09-16 212832" src="https://github.com/user-attachments/assets/4ae8283d-40d5-40d7-bd60-2e61eb2431f4" />

<img width="1919" height="1079" alt="Screenshot 2026-09-16 213056" src="https://github.com/user-attachments/assets/b2e4d36c-9e43-4064-9c6f-7762f0734136" />

<img width="1919" height="1079" alt="Screenshot 2026-09-16 213105" src="https://github.com/user-attachments/assets/0598c1bd-7a6f-46a1-8794-1b735a0db7c6" />



### Step 4.1 Evidence Integrity

Before analysis, hash values were calculated for the parsed output folders to preserve evidential integrity throughout the examination.

<img width="1919" height="1079" alt="Screenshot 2026-09-16 212948" src="https://github.com/user-attachments/assets/55297da4-d7d2-42d6-903e-62e2c2571da0" />


---

## Step 5. Correlate the Timeline

Independent artifacts were correlated to determine whether they described the same creation event.

| Time | Artifact | Interpretation |
|---|---|---|
| 9:10:19 PM 9/16/2026 | `$MFT` | File record allocated |
| 9:10:19 PM 9/16/2026 | USN Journal | NTFS records file creation |
| 9:10:19 PM 9/16/2026 | `$LogFile` | Creation transaction committed |
| 9:10:19 PM 9/16/2026 | `ConsoleHost_history.txt` | User issued creation command |

The convergence of these independent artifacts corroborates a single file creation event originating from the CLI workflow.

---

## Step 6. Artifact Findings

### 6.1 Parsed `$MFT`

The Master File Table confirms the allocation of `client_report.txt` and records its creation timestamps.

<img width="1917" height="501" alt="Screenshot 2026-09-16 215122" src="https://github.com/user-attachments/assets/5ab0b663-80bb-43d6-a0bf-422ecc2fc13b" />


### 6.2 Parsed `$UsnJrnl`

The USN Journal records the file creation event, providing transactional evidence that the file was created on disk.

<img width="1919" height="1079" alt="Screenshot 2026-09-16 220908" src="https://github.com/user-attachments/assets/e8fcad54-35c5-444c-a357-407e0bfc5005" />


### 6.3 Parsed `$LogFile`

`$LogFile` captures the NTFS transaction associated with the creation of the file, corroborating the journal and MFT records.

<img width="1919" height="1079" alt="Screenshot 2026-09-16 221923" src="https://github.com/user-attachments/assets/89a9e0a3-ed5a-40bc-acfb-b4b6cfb8c984" />


### 6.4 ConsoleHost History

`ConsoleHost_history.txt` contains the exact PowerShell command used to create the file.

This artifact does not prove the file exists on its own; it corroborates that the command responsible for creating the file was executed.

<img width="1914" height="1045" alt="Screenshot 2026-09-16 223008" src="https://github.com/user-attachments/assets/b8c2cf2d-051b-4f99-b708-58927561edd1" />


---

## Step 7. Event Viewer Analysis

PowerShell Operational logs were examined during the investigation.

No event corresponding directly to the file creation timestamp was present. The available PowerShell events instead recorded activity related to the subsequent hash calculation performed during evidence processing, approximately nine minutes after the file was created.

This absence does not weaken the conclusion, as the creation event is independently corroborated by ConsoleHost history and multiple NTFS artifacts.

<img width="1919" height="1079" alt="Screenshot 2026-09-16 230439" src="https://github.com/user-attachments/assets/57e9e65d-5ada-4310-a2d7-82caf7aa327e" />


---

## Conclusion

This investigation demonstrates that a file created entirely through the **Command Line Interface** leaves a distinct and corroborating chain of forensic evidence.

Rather than relying on a single artifact, the examination correlates **ConsoleHost_history.txt**, **$MFT**, **$UsnJrnl**, and **$LogFile** to reconstruct the birth of `client_report.txt`. Together, these artifacts independently describe the same creation event and provide a defensible forensic narrative of CLI-based file creation.

As part of the broader forensic lifecycle project, this report documents the **CLI workflow**, while the corresponding GUI investigation examines the same stage through Explorer-based user interaction. Both workflows ultimately converge on the same physical NTFS evidence, demonstrating how different user actions produce corroborating forensic artifacts.
