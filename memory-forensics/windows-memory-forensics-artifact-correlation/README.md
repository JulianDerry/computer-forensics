# Windows Memory Forensics & Artifact Correlation

**Volatility 3 • KAPE • Eric Zimmerman Tools**

> A controlled Windows 11 memory-forensics investigation demonstrating process analysis, document discovery, command-execution reconstruction, browser-memory examination, deleted-file remnants, and cross-artifact correlation.

---

## Case Information

| Field | Value |
|---|---|
| **Case ID** | `MEM-2026-001` |
| **Investigator** | Julian Derry |
| **Platform** | Windows 11 (Build 26100) |
| **Acquisition** | Belkasoft RAM Capture |
| **Primary Analysis** | Volatility 3 |
| **Corroboration** | KAPE / PSReadLine / Prefetch |

## Scenario

I conducted this investigation on my own Windows 11 workstation to demonstrate how volatile memory can be used to reconstruct recent user activity. I created a controlled environment in which I opened documents, executed commands, browsed the web, manipulated files, and deliberately deleted evidence before acquiring a physical memory image.

The objective was to determine what activity could be established from RAM alone, what required corroboration from other forensic artifacts, and where the evidence reached its limitations.

## Investigation Objectives

| Objective | Forensic Question |
|---|---|
| **System Identification** | What Windows version and build was running? |
| **Process Analysis** | What processes were active during acquisition? |
| **User Activity** | Which applications were being used? |
| **Command Execution** | Can PowerShell or CMD use be established, and what was executed? |
| **Network Activity** | What network connections existed in memory? |
| **Browser Activity** | Can browser-related artifacts be recovered from RAM? |
| **Deleted Evidence** | Can remnants of deleted files be identified? |
| **Timeline Reconstruction** | What happened immediately before memory acquisition? |

---

## Controlled Test Environment

### Phase A — Normal User Activity

Before capturing memory, I created a controlled workload to establish known ground truth.

Applications opened:

- Google Chrome
- Microsoft Edge
- Microsoft Word
- Notepad
- File Explorer
- PowerShell
- Command Prompt

Test files created on the Desktop:

- `Project_Notes.docx`
- `Confidential_Report.pdf`
- `passwords.txt`
- `investigation.txt`

> **Figure 1 — Controlled Windows 11 Environment**  
> _Insert screenshot showing the desktop with the created test files._

### Phase B — Controlled Forensic Activity

I deliberately generated activity that I intended to investigate later from memory.

1. Opened `Confidential_Report.pdf`
2. Opened `Project_Notes.docx`
3. Searched for **Music** and **Meek Mill** in File Explorer
4. Executed commands in Command Prompt
5. Executed PowerShell commands
6. Copied a PNG file
7. Deleted `passwords.txt - Copy`
8. Browsed multiple websites using Firefox
9. Opened and closed several applications
10. Left FTK Imager, Magnet RAM Capture, and Claude AI running prior to acquisition

This controlled sequence allowed me to compare known user activity against artifacts recovered from RAM.

---

## Memory Acquisition

I acquired physical memory using **Belkasoft RAM Capture** while the operating system was still running.

> **Figure 2 — Belkasoft RAM Capture**  
> _Insert screenshot of the completed memory acquisition._

### Evidence Integrity

Immediately after acquisition, I calculated the SHA-256 hash of the memory image to preserve evidence integrity.

```powershell
Get-FileHash "C:\Users\CyberSamurai\Desktop\BlkasoftRamCapturer\x64\WIN_RAM.mem"
```

The hash was calculated after memory acquisition and serves only to verify the integrity of the captured evidence. It is not evidence of pre-acquisition user activity.

> **Figure 3 — SHA-256 Hash Verification**  
> _Insert `Get-FileHash` output._

---

## System Identification

I began analysis using Volatility 3 to identify the operating system and kernel information.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.info
```

### Findings

| Artifact | Result |
|---|---|
| **Operating System** | Windows 11 |
| **Build** | 26100 |
| **Architecture** | 64-bit AMD64 |
| **Kernel Base** | `0xf80184c00000` |
| **DTB** | `0x1ae000` |
| **Symbols** | Successfully resolved |

The memory image was successfully identified as a Windows 11 Build 26100 system with correctly resolved symbols, providing a reliable foundation for subsequent analysis.

> **Figure 4 — Volatility `windows.info` Output**  
> _Insert `windows.info` screenshot._

---

## Process Analysis

### Active Processes — `pslist`

I enumerated running processes using Volatility's `windows.pslist` plugin.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.pslist
```

The output confirmed the presence of several applications that I intentionally left open before acquisition.

> **Figure 5 — Active Process List (`pslist`)**  
> _Insert `windows.pslist` screenshot._

### Memory Scan for Process Structures — `psscan`

I then used `windows.psscan`.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.psscan
```

Unlike `pslist`, `psscan` searches memory for process structures and may identify processes that are no longer present in the active process list. Finding a process in `psscan` does **not** automatically mean it was hidden or malicious.

This distinction is important because several applications appeared in both plugins, demonstrating consistency rather than concealment.

> **Figure 6 — Process Scan (`psscan`)**  
> _Insert `windows.psscan` screenshot._

---

## Cross-Artifact Corroboration

To strengthen the investigation, I compared Volatility results with Windows Prefetch artifacts collected using KAPE.

Prefetch demonstrated that many of the same applications executed within the relevant timeframe. I intended to further corroborate this with Amcache and its transaction logs; however, the transaction logs were not available, so the newest Amcache entries could not be verified.

> **Figure 7 — Prefetch Corroboration**  
> _Insert Prefetch screenshot._

---

## Identifying User Activity

To isolate applications involved in the controlled experiment, I filtered the process list using `grep`.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.pslist.PsList | \
grep -Ei "PID|explorer\.exe|msedge\.exe|chrome\.exe|winword\.exe|powershell\.exe|cmd\.exe|notepad\.exe"
```

I later narrowed the results to processes associated with `2026-09-03` using `psscan`.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.psscan.PsScan | \
grep "2026-09-03" | \
grep -Ei "chrome|msedge|firefox|explorer|winword|notepad|powershell|pwsh|cmd|Acrobat|claude|ftkimager|magnet"
```

This significantly reduced noise and made timeline reconstruction easier. Not every returned process was manually launched by me; some were background services or child processes spawned by applications.

> **Figure 8 — Filtered User Processes**  
> _Insert filtered `psscan` results._

---

## Recovering Opened Documents

### `Confidential_Report.pdf`

I searched memory for references to the PDF.

```bash
strings -a -t x "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" | grep -i "Confidential_Report"
```

**Findings:**

- Full Desktop path recovered
- Multiple references found in memory
- Strings associated with Chrome and Word
- Recent-view references observed

The recovered strings indicate that Windows and user applications had the file path loaded into memory, supporting the conclusion that the document had recently been accessed. They do **not** independently establish the exact time the document was opened.

> **Figure 9 — Memory Strings for `Confidential_Report.pdf`**  
> _Insert `strings` output._

### `Project_Notes.docx`

I repeated the same procedure for the Word document.

```bash
strings -a -t x "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" | grep -i "Project_Notes.docx"
```

**Findings:**

- Desktop path recovered
- Multiple memory references
- Explorer and Chrome references present
- Recent document strings identified

These findings support the assessment that the document had been accessed during my controlled activity, although RAM alone does not establish the precise access timestamp.

> **Figure 10 — Memory Strings for `Project_Notes.docx`**  
> _Insert recovered strings screenshot._

---

## Command Execution Analysis

### Objective

> Can I establish that a command shell was used, and what was executed?

Volatility successfully identified the PowerShell process, but it did not recover the individual commands executed during the session.

The executed commands were instead recovered from the **PSReadLine** artifact collected with KAPE.

| Evidence Source | Established |
|---|---|
| **Volatility RAM** | PowerShell process existed |
| **KAPE PSReadLine** | Commands executed |
| **Combined Interpretation** | PowerShell session and command history were corroborated |

Artifact:

`ConsoleHost_history.txt`

Location:

```text
C:\Users\CyberSamurai\AppData\Roaming\Microsoft\
Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

Recovered commands included:

```text
tree
ipconfig
ipconfig /all
netstat
```

I therefore concluded that command execution was established through **cross-artifact correlation, not from RAM alone**.

> **Figure 11 — PSReadLine Command History**  
> _Insert `ConsoleHost_history.txt` screenshot._

---

## Network Activity

I attempted to identify active network connections using Volatility's `windows.netscan` plugin.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.netscan
```

### Result

No usable network connection artifacts were recovered from the captured memory image using either Volatility 3 in Kali Linux or Volatility Workbench in Windows.

The correct forensic conclusion is **not** that no network connections existed. The evidence only supports that network connections could not be established from the available memory evidence.

> **Figure 12 — `windows.netscan` Output**  
> _Insert `netscan` screenshot, including no results if applicable._

---

## Browser Activity

To investigate Firefox activity, I dumped the browser process memory.

```bash
python3 vol.py -f "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" windows.memmap --pid 43804 --dump
```

I then extracted URL strings:

```bash
strings -a pid.43804.dmp | grep -Eio 'https?://[^\"]+'
```

### Findings

- Firefox process successfully identified
- Browser memory successfully dumped
- URL strings recovered from process memory

However, I could not reconstruct a reliable timestamped browsing history from RAM alone. For that purpose, browser profile artifacts or Browser History Examiner would provide stronger evidence and are addressed in a separate browser-forensics project.

> **Figure 13 — Firefox Process Dump**  
> _Insert `windows.memmap` screenshot._

> **Figure 14 — Recovered URL Strings**  
> _Insert extracted URL results._

---

## Deleted File Remnants

As part of the controlled experiment, I copied `passwords.txt` and deleted the copy shortly afterward.

I searched memory for references to the deleted filename.

```bash
strings -a -t x "/mnt/hgfs/SHARED FOLDER/WIN_RAM.mem" | \
grep -i "passwords.txt - Copy"
```

### Findings

Memory contained identifiable remnants associated with `passwords.txt - Copy`.

Although I knew from my controlled activity that the file had been deleted, the recovered memory strings did **not** independently establish the exact deletion timestamp. In a real investigation, additional artifacts such as the MFT, USN Journal, or Recycle Bin metadata would be required to prove when deletion occurred.

> **Figure 15 — Deleted File Memory Remnants**  
> _Insert `passwords.txt - Copy` strings output._

---

## Correlated Timeline

| Activity | Evidence Source | Confidence |
|---|---|---|
| Applications launched | `pslist` / `psscan` | **High** |
| PDF referenced in memory | Volatility + `strings` | **High** |
| DOCX referenced in memory | Volatility + `strings` | **High** |
| PowerShell process identified | Volatility | **High** |
| PowerShell commands recovered | PSReadLine | **High** |
| Firefox URLs recovered | Process memory | **Medium** |
| Deleted filename recovered | Memory strings | **Medium** |
| Memory acquisition completed | Belkasoft | **High** |
| SHA-256 integrity verified | PowerShell | **High** |

> **Note:** This timeline separates observed forensic evidence from my known controlled activity. Events are included only where supporting artifacts exist.

> **Figure 16 — Final Investigation Timeline**  
> _Insert timeline graphic or annotated screenshot._

---

## Evidence Matrix

| Finding | Artifact | Tool | Status |
|---|---|---|---|
| Windows 11 Build 26100 identified | Kernel structures | Volatility 3 | **Confirmed** |
| Active processes recovered | EPROCESS structures | Volatility 3 | **Confirmed** |
| User applications identified | Process list | Volatility 3 | **Confirmed** |
| PowerShell execution established | Process + PSReadLine | Volatility + KAPE | **Confirmed** |
| PDF and DOCX references recovered | Memory strings | Volatility | **Confirmed** |
| Firefox URLs recovered | Process dump | Volatility | **Partial** |
| Deleted filename recovered | Memory strings | Volatility | **Partial** |
| Network connections established | Socket structures | Volatility | **Not established** |

---

## Objective Assessment

| Objective | Status |
|---|---|
| System Identification | ✅ **Achieved** |
| Process Analysis | ✅ **Achieved** |
| User Activity | ✅ **Achieved** |
| Command Execution | ✅ **Achieved — Cross-artifact correlation** |
| Network Activity | ⚠️ **Not Established** |
| Browser Activity | ◐ **Partially Achieved** |
| Deleted Evidence | ◐ **Partially Achieved** |
| Timeline Reconstruction | ◐ **Partially Achieved** |

---

## Conclusion

In this investigation, I successfully reconstructed significant portions of recent user activity from a controlled Windows 11 memory acquisition. Volatility 3 established the operating system, recovered active processes, identified recently accessed documents, and preserved remnants of a deliberately deleted filename. KAPE and Eric Zimmerman artifacts complemented the memory analysis by recovering PowerShell command history through PSReadLine, demonstrating the value of cross-artifact correlation in DFIR.

The investigation also highlights the limitations of volatile memory. Although Firefox URL strings were recoverable, RAM alone could not produce a reliable timestamped browsing history. Likewise, no network connections could be established from the captured memory using the tested methods, and the deleted file remnants did not independently prove the deletion time.

Rather than treating the absence of evidence as proof that an event never occurred, I distinguished between confirmed findings, corroborated findings, and evidence that could not be established. This reflects the methodology I would apply in a real digital forensic investigation.

---

## Tools Used

- Volatility 3
- KAPE
- Eric Zimmerman Tools
- Belkasoft RAM Capture
- PowerShell
- Kali Linux / Volatility Workbench
- Browser History Examiner (referenced as a stronger source for browser-profile corroboration)

## Forensic Takeaways

1. **RAM is powerful but volatile.** It can expose processes, paths, URLs, and remnants that may disappear from persistent storage.
2. **Process existence is not proof of maliciousness.** `psscan` results require contextual interpretation.
3. **Cross-artifact correlation strengthens conclusions.** The PowerShell process in RAM and PSReadLine history provided stronger evidence together than either source alone.
4. **Absence of evidence is not evidence of absence.** The failed `netscan` result did not prove that network connections never existed.
5. **Timestamps require appropriate artifacts.** RAM strings alone cannot reliably establish precise document-access or deletion times.
6. **Forensic reporting must state limitations.** Confirmed, partial, and unestablished findings should remain clearly separated.

---

## Portfolio Context

This case is part of my broader **JulianDerry DFIR Portfolio** and focuses specifically on Windows memory forensics and artifact correlation.

[← Back to Computer Forensics](../)

[View the main DFIR portfolio](https://github.com/JulianDerry/JulianDerry)

---

> **Disclaimer:** This investigation was conducted using a controlled laboratory environment for educational and professional portfolio purposes. No confidential client evidence is included.
