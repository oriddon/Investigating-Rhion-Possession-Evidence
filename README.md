# SBT-DF204 – Case Study 4: Investigating Rhion Possession Evidence

## Overview

This repository documents my forensic investigation for **SBT-DF204 – Computer Forensics Case Studies**, Case Study 4: **Investigating Rhion Possession Evidence**.

The investigation involved an offline examination of a supplied USB disk image and network packet captures. The aim was to preserve the evidence, recover relevant files, examine possible hidden content, reconstruct FTP and HTTP transfers, validate recovered objects, and correlate the findings using a UTC timeline.

All analysis was carried out in an isolated Kali Linux laboratory environment using working copies of the supplied evidence.

---

## Objectives

The investigation focused on the following objectives:

* Preserve the supplied evidence and verify working copies using SHA-256 hashes.
* Examine the USB disk image and identify its filesystem structure.
* Recover deleted or recoverable files from the USB image.
* Identify recovered file types and detect duplicate files.
* Review image and document metadata.
* Examine recovered images for possible hidden content.
* Analyse FTP control and data sessions.
* Reconstruct files transferred through FTP.
* Examine and validate the protected ZIP archive.
* Analyse HTTP requests and responses.
* Reconstruct relevant HTTP objects.
* Correlate findings across USB, FTP and HTTP evidence.
* Produce a cautious conclusion based only on technically supported findings.

---

## Evidence Examined

The supplied case package contained:

```text
RHINOUSB.dd
rhino.log
rhino2.log
rhino3.log
```

The original evidence files were preserved, hashed and copied into a separate working directory before examination.

> Raw case evidence is not included in this repository.

---

## Tools Used

The investigation was completed using tools available in Kali Linux, including:

* SHA-256 utilities
* `file`
* `stat`
* Sleuth Kit

  * `img_stat`
  * `mmls`
  * `fsstat`
  * `fls`
  * `istat`
  * `icat`
* PhotoRec
* ExifTool
* Antiword
* Stegseek
* Binwalk
* `cmp`
* Wireshark
* Tshark
* 7-Zip
* John the Ripper
* `zip2john`

---

## Investigation Workflow

### 1. Evidence Preservation

The original evidence archive and extracted files were hashed using SHA-256. Separate working copies were created and verified before analysis.

### 2. USB Disk Image Examination

The USB image was identified as a **FAT16 filesystem** beginning at sector 0.

The root directory contained:

```text
gumbo1.txt
gumbo2.txt
```

A deleted-directory search did not return deleted directory entries, so file carving was used to examine recoverable material in free space.

### 3. File Recovery

PhotoRec recovered **132 files** from the USB image.

Recovered material included:

* Text files
* JPEG images
* GIF images
* A Microsoft Word document

Hash comparison showed that two recovered JPEG filenames represented identical binary content.

### 4. Metadata and Document Examination

ExifTool was used to examine recovered image and document metadata.

Antiword was used to safely extract text from the recovered Microsoft Word document without opening or executing the original file.

The document contained references to Rhino photographs, hiding photographs and reformatting a USB device. These statements were treated as contextual evidence and not as proof of authorship or user identity.

### 5. Hidden-Content Examination

Unique recovered JPEG files were examined using Stegseek.

Stegseek did not identify a valid Steghide seed in the tested JPEG files.

Binwalk was also used to inspect the recovered images for additional embedded signatures. The recorded results showed normal image signatures without separate embedded-file signatures.

These findings did not independently confirm hidden Steghide content.

### 6. FTP Analysis

Analysis of `rhino.log` identified FTP communication between:

```text
Client: 137.30.122.253
Server: 137.30.120.40
```

FTP `STOR` commands showed uploads of:

```text
rhino1.jpg
rhino3.jpg
contraband.zip
```

Relevant FTP control and data streams were identified and the transferred objects were reconstructed from the packet capture.

### 7. Protected Archive Analysis

The reconstructed `contraband.zip` file measured **230,566 bytes**, matching the amount reported by the FTP server.

The archive was identified as being encrypted with **ZipCrypto**.

An authorised offline course-only password-recovery procedure was used against the supplied training archive.

The archive successfully validated and contained:

```text
rhino2.jpg
```

A SHA-256 comparison showed that this image was binary-identical to a JPEG recovered from the USB image.

This provided the strongest technical correlation between the USB and FTP evidence.

### 8. HTTP Analysis

The HTTP captures were analysed using Tshark and Wireshark.

Relevant reconstructed objects included:

```text
rhino4.jpg
rhino.exe
```

`rhino4.jpg` was identified as a JPEG image.

`rhino.exe` was reconstructed as a **145,920-byte PE32 Windows executable**.

The executable was identified and hashed but was **not executed**.

---

## Key Findings

The investigation established that:

* The USB image contained a FAT16 filesystem.
* PhotoRec recovered 132 files from free space.
* Duplicate JPEG content was identified using SHA-256 and byte comparison.
* A recovered Word document contained references to hiding Rhino photographs.
* Image-level Stegseek and Binwalk testing did not independently confirm hidden Steghide content.
* FTP traffic showed uploads of Rhino-related files and a protected ZIP archive.
* The reconstructed `contraband.zip` matched the size reported by the FTP server.
* The archive contained `rhino2.jpg`.
* `rhino2.jpg` was binary-identical to a JPEG recovered from the USB image.
* HTTP traffic included requests for `rhino4.jpg` and `rhino.exe`.
* `rhino.exe` was identified as a Windows executable but was not executed.

The evidence supports a strong technical relationship between the recovered USB material and the recorded network activity.

However, technical artefacts such as IP addresses, usernames, filenames and matching hashes do not by themselves establish which individual physically operated a device or performed an action.

---

## Repository Structure

A suggested repository structure is:

```text
SBT-DF204-Case-Study-4/
│
├── README.md
│
├── report/
│   └── SBTDF204_CaseStudy4_Report.pdf
│
├── screenshots/
│   ├── evidence-preservation/
│   ├── usb-analysis/
│   ├── recovery/
│   ├── metadata/
│   ├── steganography/
│   ├── ftp-analysis/
│   └── http-analysis/
│
├── logs/
│   ├── evidence_hashes.txt
│   ├── filesystem_details.txt
│   ├── recovered_file_hashes.txt
│   ├── stegseek_results.txt
│   ├── ftp_control_summary.txt
│   ├── ftp_transfer_summary.txt
│   └── final_case_summary.txt
│
└── documentation/
    ├── evidence_log.md
    └── timeline.md
```

---

## Limitations

The investigation has several important limitations:

* Recovered files do not automatically prove how they originally arrived on the USB device.
* PhotoRec carving may remove original filenames, paths and filesystem context.
* Statements contained in a recovered document do not establish who wrote the document.
* Negative Stegseek and Binwalk results do not rule out every possible concealment method.
* Network identifiers establish technical relationships but do not identify the human user.
* One `rhino3.jpg` FTP transfer occurred in ASCII mode and produced line-ending warnings.
* Embedded metadata timestamps may originate from systems with unknown timezone settings.
* The reconstructed executable was not executed, so no conclusions were made about its runtime behaviour.

---

## Ethical and Evidence-Handling Notice

This repository relates to a **fictional educational digital-forensics case study**.

To protect the integrity of the course evidence, this repository does **not** include:

* The original USB disk image
* Original packet captures
* Recovered case media
* The protected archive
* Recovered passwords
* Credentials
* Wordlists
* Reconstructed executable files
* Other sensitive case artefacts

The forensic work was performed only against authorised course materials in an isolated laboratory environment.

---

## Conclusion

The investigation demonstrated how disk-image recovery, metadata analysis, steganography testing, FTP reconstruction, protected-archive analysis and HTTP traffic examination can be combined to build an evidence-led forensic account.

The strongest correlation was the matching SHA-256 value between an image recovered from the USB image and `rhino2.jpg` extracted from the reconstructed FTP archive.

The available evidence supports a high-confidence technical relationship between the recovered media and the captured network activity while remaining insufficient, on its own, to attribute those actions to a specific person.

---

## Course

**SBT-DF204 – Computer Forensics Case Studies**

**Case Study 4 – Investigating Rhion Possession Evidence**

---

## Disclaimer

This repository is maintained for academic and educational purposes only. No real-world systems, accounts, hosts or services were accessed during the investigation.
