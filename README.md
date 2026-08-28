# Computer Forensics

Windows and general computer forensics covering disk artifacts, registry and file system analysis, data recovery, password and hash recovery, and steganographic examination.

Part of the **JulianDerry DFIR Portfolio**.

---

## Overview

Most incident response cases begin and end on a Windows endpoint, and evidence often survives in places a user assumes are gone: formatted drives, timestomped files, password-protected documents, and images with data hidden within their pixel channels.

This repository documents disk, USB, browser, and file recovery investigations, built to the same standard applied across the rest of the portfolio: **what the artifact shows, and what it can be shown to prove.**

---

## Case Index

| Investigation | What it covers | Status | Repository |
|--------------|----------------|--------|------------|
| **NTFS Host-Based USB Forensics** | USB device activity reconstruction from an E01 image using KAPE, Registry Explorer, and Eric Zimmerman tools | ✅ Complete |[NTFS Host-Based USB Forensics](/usb-forensics/nfts-host-based-usb-forensics)|
| **USB Data Exfiltration Investigation** | FAT32 E01 image examined with Autopsy and Sleuth Kit to trace USB-based data exfiltration | ✅ Complete | `usb-forensics/usb-data-exfiltration-investigation` |
| **Browser Forensics Examination** | Chrome browser history examined with Browser History Examiner and cross-checked against Autopsy | ✅ Complete | `browser-forensics-investigation/browser-history-examiner` |
| **NTFS Timestomping Detection** | Timestomping simulation and detection using KAPE and MFTECmd with MFT timeline analysis | ✅ Complete | `ntfs-timestomping-detection-kape` |
| **Formatted USB Data Recovery** | Recovery of deleted data from a formatted USB drive using Magnet AXIOM Process and Examine | ✅ Complete | `data-recovery` |
| **PNG Image Steganography Investigation** | Recovery of concealed data hidden within a PNG image's pixel channels | ✅ Complete | [Steganography Forensics](steganography/png-steganography-forensics) |
| **Metadata and Steghide Flag Recovery** | PicoCTF challenge involving metadata analysis, Base64 decoding, and Steghide extraction | ✅ Complete | [Metadata and Steghide Flag Recovery](steganography/metadata-steghide-flag-recovery) |
| **MD5 Hash Recovery** | Identification and cracking of an unknown MD5 hash using Hashcat | ✅ Complete | `hash-cracking/md5-hash-recovery` |
| **PDF Password Hash Recovery** | Extraction and cracking of a password hash from a protected PDF | ✅ Complete | `hash-cracking/pdf-password-hash-recovery` |
| **Microsoft Office 2013 Password Recovery** | Offline password cracking of a protected Office document using Office2John and Hashcat | ✅ Complete | `office-2013-password-recovery-forensics` |

---

## Planned Coverage

The following areas are planned but do not yet have dedicated case folders:

- Windows Disk Forensics
- Windows Registry and User Activity
- Windows Event Log Investigation
- Memory Forensics Exercises

---

## Methodology

Every investigation follows a consistent forensic workflow:

1. Identification
2. Preservation
3. Acquisition
4. Hash Verification
5. Examination
6. Analysis
7. Correlation
8. Timeline Reconstruction
9. Reporting

Automated parsing and password-cracking tools are used where appropriate, but all significant findings are manually verified against the underlying artifacts before being reported.

---

## Toolkit

- FTK Imager
- Autopsy
- Sleuth Kit
- Magnet AXIOM
- KAPE
- MFTECmd
- Registry Explorer
- Eric Zimmerman's tools
- USB Detective
- Timeline Explorer
- Browser History Examiner
- Hashcat
- John the Ripper
- Steghide
- Volatility 3
- Python
- PowerShell

---

## Evidence & Reporting Standard

Each investigation includes:

- Acquisition method and chain of custody
- Hash verification
- Artifact examination and analysis
- Timeline reconstruction (where applicable)
- What the evidence established
- What the evidence did **not** establish
- Limitations and forensic considerations

All investigations are conducted using training datasets, laboratory environments, or authorized evidence sources for educational and professional portfolio purposes. No confidential client evidence is included.

---

## Contact

- **LinkedIn**
- **X (Twitter)**
- **Email**
