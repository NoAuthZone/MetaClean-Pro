# MetaClean Pro

Metadata inspector and file forensics workbench — one HTML file, local processing, no installation.

[![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-f7df1e?logo=javascript&logoColor=black)](#requirements)
[![Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen)](#requirements)
[![Single file](https://img.shields.io/badge/single_file-HTML-orange)](#quick-start)
[![Offline](https://img.shields.io/badge/processing-offline-blue)](#privacy-and-offline-use)

MetaClean Pro inspects embedded metadata, file signatures, binary structures, and potential anomalies directly in your browser. It supports image metadata cleaning, XMP editing, and CSV or JSON exports for further review.

**Download the HTML → open it in your browser → drop your files.**

> [!NOTE]
> Forensic findings are heuristic indicators. A risk score is not a malware verdict or proof of tampering; review findings in the context of the file.

## Quick start

1. Download [`metaclean_pro.html`](./metaclean_pro.html) using GitHub's **Download raw file** option, or download and extract the repository ZIP.
2. Open the downloaded HTML file in a modern browser.
3. Drag one or more files into the drop zone, or click it to browse.
4. Explore the **Cards**, **Files**, **Tags**, and **Forensics** views.

To try the tool without your own files, select **Inspect GPS sample**. Select **Generate GPS sample** to download the generated example image.

## Requirements

A modern browser with JavaScript enabled. No package manager, build step, backend, or external JavaScript library is required.

SHA-256 hashing depends on the browser exposing the Web Crypto API. Large files require sufficient browser memory.

## What it inspects

| Area | Details |
| --- | --- |
| File identity | File name, size, extension, browser MIME type, detected format, and signature |
| Image metadata | Supported EXIF, GPS, XMP, IPTC, PNG text, and WebP information |
| Media containers | Supported ISO-BMFF metadata and structural information |
| Integrity indicators | Signature/extension mismatches, structural warnings, and trailing data |
| Binary structure | Format-aware chunk and segment maps with offsets and sizes |
| Entropy | Entropy measurements and window-based inspection |
| Strings | Extracted ASCII and UTF-16 LE/BE strings with offsets |
| Secondary signatures | Potential embedded or appended file signature candidates |
| Hashing | SHA-256 where supported by the browser |

Metadata coverage varies by format. The tool does not provide full ExifTool coverage.

## Views and controls

| View | Purpose |
| --- | --- |
| **Cards** | Review individual files, available previews, metadata, and file actions |
| **Files** | Compare files in a sortable, searchable table |
| **Tags** | Inspect tag names, IDs, groups, values, and sources |
| **Forensics** | Review risk findings, entropy, segments, extracted strings, and the hex header |

Search file names and metadata values, filter by file type or metadata presence, and sort by name, size, modification time, type, tag count, or risk score. Tag filters let you narrow results by file, group, and source.

## Metadata editing and cleaning

| Operation | Support |
| --- | --- |
| Edit XMP fields | Title, description, creator, copyright, keywords, and rating |
| Export XMP sidecar | Download edited metadata as a separate XMP file |
| Embed XMP | Supported JPEG, PNG, and WebP images |
| Remove metadata | Selected metadata structures in JPEG, PNG, and WebP |

Image metadata removal operates without re-encoding image data. Modified files are downloaded as copies; the original files are not overwritten.

A typical cleaning workflow:

1. Load an image and inspect its metadata.
2. Use the available metadata removal action.
3. Download the modified copy.
4. Load that copy into MetaClean Pro to inspect the remaining metadata.

Removal targets supported metadata structures. It does not guarantee removal of every identifying detail, hidden payload, or information visible in the image itself.

## Reports and exports

| Export | Contents |
| --- | --- |
| Batch JSON | Metadata records for loaded files |
| All tags CSV | Metadata tags across loaded files |
| Filtered tags CSV | Tags matching the current filters |
| Filtered files CSV | File summaries matching the current filters |
| Forensic JSON | The selected file's forensic findings |

Use CSV exports for spreadsheet review and JSON exports for further processing.

## Privacy and offline use

File inspection and editing run locally in the browser. The standalone application does not upload files or require a server connection for analysis.

GPS map links open OpenStreetMap. Following a link sends the displayed coordinates to that external service. Report exports may also contain metadata such as GPS coordinates, creator names, or extracted strings from the inspected files.

## Advantages and limitations

| Advantages | Limitations |
| --- | --- |
| Local processing without file uploads | Large files can consume substantial browser memory |
| One portable HTML file | Browser API availability can affect individual features |
| Batch inspection and searchable tags | Metadata coverage depends on the format |
| Cleaning without image re-encoding | Embedded editing and cleaning support JPEG, PNG, and WebP only |
| Forensic findings with supporting details | Heuristic analysis can produce false positives or miss anomalies |

High entropy is common in compressed or encrypted content. Secondary signatures can occur by coincidence. Some analysis of very large files is sampled, and displayed results may be capped for performance.



---

**MetaClean Pro** · Inspect metadata · Review file structures · Clean supported images
