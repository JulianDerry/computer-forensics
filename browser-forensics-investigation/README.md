# Browser Forensics Examination Report

**Magnet AXIOM / Browser History Examiner Analysis**

- **Case Reference:** DFIR-2026-BF-001
- **Analyst:** Julian Derry
- **Date of Analysis:** 18 August 2026
- **Evidence Type:** Chrome Browser Profile
- **Browser Profile:** Chrome (Default)

> *This report documents a GUI-based browser forensic examination using Browser History Examiner. The results were also manually examined using Autopsy to provide an independent comparison of the browser-history examination workflow.*

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Examination Objectives](#2-examination-objectives)
3. [Evidence and Examination Scope](#3-evidence-and-examination-scope)
4. [Methodology](#4-methodology)
5. [Browser History Examiner Overview](#5-browser-history-examiner-overview)
6. [Browser Artifact Findings](#6-browser-artifact-findings)
7. [Handgun-Related Browser Activity](#7-handgun-related-browser-activity)
8. [Bookmarks, Downloads, Logins and Extensions](#8-bookmarks-downloads-logins-and-extensions)
9. [Other Relevant Browser Activity](#9-other-relevant-browser-activity)
10. [Timeline and Correlation](#10-timeline-and-correlation)
11. [Comparison with Manual Autopsy Examination](#11-comparison-with-manual-autopsy-examination)
12. [Findings and Interpretation](#12-findings-and-interpretation)
13. [Limitations](#13-limitations)
14. [Conclusion](#14-conclusion)

---

## 1. Executive Summary

The examination reviewed browser artifacts recovered from a Chrome Default profile using Browser History Examiner, a GUI-oriented browser history examination tool. The examination identified a substantial volume of browser activity, including **27,676 website visits**, **10,727 searches**, **136 downloads**, **661 form-history records**, **10 saved login records**, **42 bookmarks**, **9 browser extensions**, **3,562 cookies**, **456 session-tab records**, and **700 site-storage records** as displayed by the examiner.

The recovered activity shows that the user accessed a broad range of websites and services. Of particular forensic interest, the search and browsing records contain multiple **handgun-related queries** and visits to **firearms-related websites**. Recorded searches included queries for handgun types, how to buy a gun, and how to load a double-action gun. The examination also identified visits to **Armscor** and **Mister Guns**, as well as downloaded material associated with a **Beretta 92A1** and a **Ruger P97** manual.

These artifacts support the conclusion that the examined browser profile was used to research handguns and related firearm information. The artifacts do not, by themselves, establish that the user purchased, possessed, owned, or intended to use a firearm.

The same browser evidence was also examined manually using Autopsy. The purpose of the comparison was not to establish which tool is more forensically valid, but to demonstrate the practical difference between manual artifact examination and a purpose-built GUI workflow. Browser History Examiner presented the browser artifacts in categorized, searchable and filterable views, making common examination tasks significantly easier to navigate and review.

---

## 2. Examination Objectives

- Identify browser artifacts present within the examined Chrome profile.
- Review recorded website visits and search activity.
- Identify downloads and their associated URLs and filenames.
- Examine bookmarks, saved logins, form history, cookies, session tabs, and extensions.
- Identify activity relevant to the investigative question, including firearm-related browsing.
- Correlate related browser artifacts by date and time.
- Evaluate the usability of a GUI-based browser history examination workflow.
- Compare the GUI examination process with the manual Autopsy examination performed on the same evidence.

---

## 3. Evidence and Examination Scope

The supplied examination material contains screenshots from Browser History Examiner showing the recovered artifacts from a Chrome Default browser profile. The screenshots identify the following artifact categories and record counts:

| Artifact | Records Displayed |
| :--- | :--- |
| Bookmarks | 42 |
| Browser Settings | 23 |
| Cached Files | 1 |
| Cached Images | 0 |
| Cached Web Pages | 0 |
| Cookies | 3,562 |
| Downloads | 136 |
| Email Addresses | 98 |
| Extensions | 9 |
| Favicons | 24,567 |
| Form History | 661 |
| Logins | 10 |
| Searches | 10,727 |
| Session Tabs | 456 |
| Site Settings | 0 |
| Site Storage | 700 |
| Thumbnails | 1 |
| Website Visits | 27,676 |

> The examiner interface displayed the time zone as UTC and the date format as dd/mm/yyyy in the supplied screenshots.

---

## 4. Methodology

1. Review the browser evidence using Browser History Examiner.
2. Identify the available browser profile and artifact categories.
3. Review high-value artifacts including website visits, searches, downloads, bookmarks, logins, form history, and extensions.
4. Filter and correlate records by keyword and time where relevant.
5. Identify firearm-related activity from the recorded URLs, search terms, and downloaded files.
6. Review the underlying activity manually using Autopsy as a comparison exercise.
7. Distinguish directly observed artifacts from conclusions that require interpretation.
8. Document limitations and avoid inferring intent beyond what the browser artifacts support.

---

## 5. Browser History Examiner Overview

Browser History Examiner provided a single graphical interface for accessing multiple browser artifact categories. The supplied screenshots show dedicated views for website visits, bookmarks, browser settings, cached files, cookies, downloads, email addresses, extensions, favicons, form history, logins, searches, session tabs, site settings, site storage, and thumbnails.

The tool also provided keyword, date, time, and browser filtering. The capture workflow shown in the supplied material supports extraction from a user profile and includes options relating to Chrome, Edge, Firefox, and Internet Explorer/Edge Legacy, as well as history and cache data. This makes the workflow particularly accessible when the immediate investigative objective is browser activity rather than a complete manual reconstruction of the browser databases.

---

## 6. Browser Artifact Findings

### 6.1 Website Visits

The Website Visits view records **27,676 visits** across the examined period. The summary identifies Google, GitHub, LinkedIn, Gmail, X, TryHackMe, YouTube, ChatGPT, Google Drive, and other services among the most frequently visited domains. The presence of a large number of visits demonstrates the usefulness of a summarized domain view when triaging a browser profile before moving to individual records.

![Figure 12: Website-visit summary showing the highest-volume domains in the examined browser profile.](media/image1.png)

*Figure 12. Website-visit summary showing the highest-volume domains in the examined browser profile.*

### 6.2 Search Activity

The Searches artifact contains **10,727 records**. The search view provides the date searched, search terms, search engine, URL, source, and browser profile. This artifact is particularly useful for reconstructing the subjects the user was actively researching.

![Figure 13: Search artifacts showing handgun-related queries and other searches recorded in Chrome.](media/image2.png)

*Figure 13. Search artifacts showing handgun-related queries and other searches recorded in Chrome.*

---

## 7. Handgun-Related Browser Activity

Several artifacts are directly relevant to handgun research. The search records include the following queries:

- `how to load Double-Action gun`
- `hand gun types`
- `how to buy a gun`

The search records show these queries being submitted to Google and YouTube on **18 June 2026**. The examiner also recorded visits associated with **Armscor** on **18 August 2026** and **Mister Guns** during the same examination period. These records are consistent with research into handguns and firearm-related information.

The Downloads artifact provides additional context. The supplied screenshot shows a completed download of a **Beretta 92A1 image** from `everydaymarksman.com` and a PDF retrieved from a Ruger S3 `amazonaws.com` documentation URL identified as **`p97DA0.pdf`**. These downloads are relevant because they provide artifacts beyond search terms alone and show that firearm-related material was retrieved to the local system.

Taken together, the search queries, firearm-related website visits, and firearm-related downloads provide multiple independent browser artifacts supporting the assessment that the user was researching handguns. The available evidence does not establish a purchase, physical possession, ownership, or intent to commit an offence.

![Figure 6: Download artifacts including a Beretta 92A1 image, a Ruger P97 manual, and other downloaded files.](media/image3.png)

*Figure 6. Download artifacts including a Beretta 92A1 image, a Ruger P97 manual, and other downloaded files.*

---

## 8. Bookmarks, Downloads, Logins and Extensions

### 8.1 Bookmarks

The examination identified **42 bookmark records**. The bookmark view includes creation date, URL, last accessed date, expiry, name/content, and browser profile. Bookmark artifacts can be useful because they may indicate webpages intentionally saved by the browser user, although the existence of a bookmark alone does not establish the reason it was saved.

![Figure 5: Recovered browser records showing repeated activity associated with Armscor.](media/image4.png)

*Figure 5. Recovered browser records showing repeated activity associated with Armscor.*

### 8.2 Downloads

Browser History Examiner identified **136 download records**. The supplied records include completed downloads and provide fields such as URL, local path, state, end time, and bytes downloaded. Of particular relevance to this examination are the Beretta 92A1 image and the Ruger P97 PDF identified above.

### 8.3 Saved Logins

**Ten saved-login records** were identified. The supplied screenshot shows login records associated with domains including LinkedIn, Netflix, Adobe services, PrivateEmail, Belkasoft, `cipher.hivedfir.com`, and Roadtrace. The records include hostname, origin URL, submit URL, username, creation date, last-used date, password-change information, and times used. Password values were not reproduced in this report.

![Figure 11: Saved login artifacts recovered from the browser profile.](media/image5.png)

*Figure 11. Saved login artifacts recovered from the browser profile.*

### 8.4 Form History and Email Addresses

The examination identified **661 form-history records** and **98 email-address records**. Examples in the supplied material include form activity associated with GitHub, Whois, VirusTotal, MXToolbox, and other services. These artifacts can provide supporting context for user activity and may help correlate browser interactions with other evidence.

![Figure 10: Form-history artifacts showing stored search/form values and associated domains.](media/image6.png)

*Figure 10. Form-history artifacts showing stored search/form values and associated domains.*

### 8.5 Browser Extensions

**Nine browser extensions** were identified. The supplied extension view includes AdGuard AdBlocker, Chrome Web Store Payments, Claude in Chrome, FastApply, Google Docs Offline, and other extensions. Extension artifacts can be relevant in a forensic examination because they establish software installed within the browser profile and may help explain browser behavior or identify additional sources of activity.

![Figure 8: Browser extensions recovered from the Chrome Default profile.](media/image7.png)

*Figure 8. Browser extensions recovered from the Chrome Default profile.*

---

## 9. Other Relevant Browser Activity

The profile contains evidence of extensive use of technology, professional, and online services. The Website Visits summary identifies high volumes of activity involving **Google, GitHub, LinkedIn, Gmail, X, TryHackMe, YouTube, ChatGPT, Google Drive, Flashscore, Claude, Canva**, and other domains. The examination also contains records associated with email services, forensic-training platforms, and technical websites.

The evidence therefore represents a broad browser profile rather than a single-purpose browsing session. This makes filtering and categorization particularly useful during triage.

---

## 10. Timeline and Correlation

The supplied artifacts provide a time range extending from at least **May 2026** through **18 August 2026**, with the Website Visits summary identifying activity from **21 May 2026 to 18 August 2026**. The browser records are displayed in UTC.

A relevant firearm-related sequence includes:
- **18 June 2026:** Handgun searches, including queries for handgun types, how to buy a gun, and how to load a double-action gun.
- **Later:** Firearm-related website and download activity, including Armscor and Mister Guns visits and downloads associated with a Beretta 92A1 image and Ruger P97 documentation.

Correlation across search, website-visit, and download artifacts is stronger than relying on a single browser artifact. The combination demonstrates repeated interaction with firearm-related information rather than one isolated search.

---

## 11. Comparison with Manual Autopsy Examination

The same browser evidence was also examined manually using Autopsy. The manual process required navigating the underlying browser artifacts and reviewing the recovered records through Autopsy's forensic interface. Browser History Examiner provided a more direct browser-centric workflow, with the major browser artifact classes exposed as dedicated categories.

For a focused browser examination, the GUI workflow reduces the amount of manual navigation required to locate common artifacts. Website visits, searches, downloads, bookmarks, logins, form history, and extensions can be reviewed from clearly labelled artifact categories and filtered by keyword, date, and time. This makes the tool particularly useful for rapid browser triage and straightforward examination tasks.

> This comparison does not mean that Browser History Examiner replaces a full forensic platform. Autopsy remains useful when browser evidence needs to be considered alongside filesystem, registry, application, and other forensic artifacts. The practical value demonstrated here is that a dedicated browser-history examiner can make a browser-focused investigation faster and more accessible while still exposing a useful set of forensic artifacts.

---

## 12. Findings and Interpretation

| Finding | Supporting Artifacts | Assessment |
| :--- | :--- | :--- |
| **BF-001** | 27,676 website visits | The browser profile contains extensive recorded web activity across numerous domains. |
| **BF-002** | 10,727 searches | The profile contains extensive recorded search activity, including identifiable search terms and search engines. |
| **BF-003** | Handgun-related searches | Queries included handgun types, how to buy a gun, and how to load a double-action gun. |
| **BF-004** | Armscor and Mister Guns visits | The browser records include visits to firearm-related websites. |
| **BF-005** | Beretta 92A1 image and Ruger P97 PDF downloads | The download records contain firearm-related material retrieved to the local system. |
| **BF-006** | 42 bookmarks | The profile contains bookmarked web resources with creation and access information. |
| **BF-007** | 136 downloads | The profile contains recorded download activity with URLs, local paths, and completion information. |
| **BF-008** | 10 saved logins | The profile contains saved-login records for multiple services. |
| **BF-009** | 9 extensions | The browser profile contains nine identified extensions, including AdGuard and Claude in Chrome. |

---

## 13. Limitations

- The supplied evidence consists of Browser History Examiner screenshots rather than the original browser database files, so this report does not independently re-parse the original databases.
- Browser artifacts can be deleted, modified, synchronized, or affected by browser configuration and version.
- Searches and website visits establish recorded browser activity but do not by themselves establish the user's physical presence at the computer for every event.
- The firearm-related artifacts establish browser research and downloaded material, but do not establish purchase, ownership, possession, or intent.
- Saved login artifacts show that credentials or login records were stored by the browser; they do not by themselves establish who entered the credentials or who subsequently used the account.
- Cookie, session, and cached artifacts can contain large amounts of automatically generated data and should be interpreted in context.

---

## 14. Conclusion

The Browser History Examiner examination successfully exposed a broad set of browser artifacts from the Chrome Default profile. The GUI provided direct access to website visits, searches, downloads, bookmarks, logins, form history, extensions, and other browser artifacts, together with filtering and summary capabilities that support rapid triage.

Of particular forensic significance, the browser evidence contains multiple **handgun-related searches, firearm-related website visits, and firearm-related downloads**. The combined artifacts support the assessment that the user was researching handguns and related firearm information during the period represented by the browser records. The evidence should not be extended beyond that conclusion without corroborating evidence.

The parallel manual examination using Autopsy demonstrated the value of both approaches. Autopsy provides a broader forensic examination environment, while Browser History Examiner provides a focused, GUI-friendly workflow for browser-centric investigations. For a browser-history case, the latter can substantially simplify the initial examination and identification of relevant activity.

---

---

