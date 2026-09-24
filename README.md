# MetaClean Pro

Metadata, provenance and file forensics workbench — one HTML file, local processing, no installation.

[![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-f7df1e?logo=javascript&logoColor=black)](#requirements)
[![C2PA](https://img.shields.io/badge/C2PA-verified_offline-6f42c1)](#content-credentials-c2pa-verification)
[![Single file](https://img.shields.io/badge/single_file-HTML-orange)](#quick-start)
[![Offline](https://img.shields.io/badge/processing-offline-blue)](#privacy-and-offline-use)
[![License](https://img.shields.io/badge/license-MIT-green)](./LICENSE)

MetaClean Pro inspects embedded metadata, AI-origin hints, Content Credentials (C2PA), file signatures, binary structures and potential anomalies directly in your browser. It supports lossless image metadata cleaning, XMP editing, local pixel statistics and CSV or JSON exports for further review.

**Download the HTML → open it in your browser → drop your files.**

> [!NOTE]
> Forensic findings and AI indicators are heuristic. A risk score is not a malware verdict, and neither metadata nor pixel statistics can prove or disprove that an image was made with AI. Review findings in the context of the file.

## Quick start

1. Download [`MetaClean-Pro.html`](./MetaClean-Pro.html) using GitHub's **Download raw file** option, or download and extract the repository ZIP.
2. Open the downloaded HTML file in a modern browser.
3. Drag one or more files into the drop zone, or click it to browse.
4. Explore the **Cards**, **Files**, **Tags**, and **Forensics** views.
5. On a file card, use **Verify C2PA signature** or **Analyze pixels locally** for the optional deeper checks.

To try the tool without your own files, select **Inspect GPS sample**. Select **Generate GPS sample** to download the generated example image. The [`examples/`](./examples) folder contains two PNG files with embedded Content Credentials (one declaring GPT Image as generator, one with a Claude provider action) for testing the AI and C2PA features.

> [!TIP]
> The HTML file is about 11 MB because it embeds the C2PA verifier (including its WebAssembly runtime) and the C2PA trust lists, so verification works without a network connection.

## Requirements

A modern browser with JavaScript enabled. No package manager, build step, backend, or external download at runtime is required.

- SHA-256 hashing requires the Web Crypto API.
- C2PA verification requires WebAssembly support.
- Pixel analysis requires `createImageBitmap` and Canvas.
- Large files require sufficient browser memory.

## What it inspects

| Area | Details |
| --- | --- |
| File identity | File name, size, extension, browser MIME type, detected format, and signature |
| Image metadata | Supported EXIF, GPS, XMP, IPTC, ICC, PNG text, and WebP information |
| Media containers | Supported ISO-BMFF metadata and structural information |
| AI indicators | IPTC digital source type, known generator names, generation parameters, C2PA declarations |
| Content Credentials | C2PA manifests, actions, ingredients, signer, timestamp and asset binding |
| Pixel statistics | Luminance entropy, neighbor difference, smooth pixel pairs, JPEG recompression difference |
| Integrity indicators | Signature/extension mismatches, structural warnings, and trailing data |
| Binary structure | Format-aware chunk and segment maps with offsets and sizes |
| Entropy | Entropy measurements and window-based inspection |
| Strings | Extracted ASCII and UTF-16 LE/BE strings with offsets, classified as URL, path, e-mail-like, or command/script-like |
| Secondary signatures | Potential embedded or appended file signature candidates |
| Hashing | SHA-256 where supported by the browser |

Metadata coverage varies by format. The tool does not provide full ExifTool coverage.

## AI indicators

MetaClean Pro reads embedded metadata for signs of AI generation or AI involvement. No AI model or classifier is used; the result is based on what the file declares about itself.

| Evidence | Source |
| --- | --- |
| AI-generated declaration | IPTC `DigitalSourceType` = `trainedAlgorithmicMedia` (XMP or C2PA) |
| AI-assisted declaration | IPTC `DigitalSourceType` = `compositeWithTrainedAlgorithmicMedia` |
| Generator names | Known tools (e.g. GPT Image, DALL-E, Stable Diffusion, AUTOMATIC1111, ComfyUI, Midjourney, Adobe Firefly, NovelAI, InvokeAI, Fooocus) in tool fields such as `Software`, `CreatorTool` or `SoftwareAgent` |
| Generation parameters | Stable Diffusion-style `parameters` text (Steps, Sampler, Seed, CFG scale) |
| Workflows | ComfyUI `prompt`/`workflow` JSON with sampler nodes |
| Provider actions | C2PA provider actions such as `com.anthropic.claude.provided` |

Each file receives one of these labels: **AI-generated declaration**, **AI-assisted declaration**, **AI indicators found**, **AI provenance: provided by …**, or **Unknown — no AI indicators**. Generator names are only matched in tool/source fields, so a caption that merely mentions a tool is not treated as evidence.

"Unknown" does not mean "not AI": metadata is easy to remove or forge. Declarations read from C2PA are marked as unverified until the separate signature check has passed.

## Content Credentials (C2PA) verification

**Verify C2PA signature** runs a local cryptographic check with the bundled [`@contentauth/c2pa-web`](https://github.com/contentauth/c2pa-js) SDK (v0.14.5). It reports signature validity, asset binding, signer and timestamp trust separately.

| Result | Meaning |
| --- | --- |
| Provenance verified | Signature and asset binding valid, signer in the trust list |
| Signer not in trust list | Signature and asset binding valid, but the certificate is not on the bundled list |
| Result incomplete | The check ran but could not establish every property |
| Verification failed | The manifest is invalid; its declarations must not be treated as authenticated |
| No embedded Content Credentials | No manifest found |

- Supported formats: PNG, JPEG, WebP, SVG, TIFF, AVIF, HEIC, MP4, MOV, M4A, MP3, WAV and PDF.
- Limit: 100 MiB per file.
- Trust lists: official C2PA trust list and TSA trust list, snapshot of 2026-09-11.
- No network access: certificate revocation is not checked online.

MetaClean Pro is not a certified C2PA conforming validator.

## Pixel analysis

**Analyze pixels locally** computes deterministic statistics for PNG, JPEG and WebP images up to 30 MiB. Images are scaled to a maximum of 1024 px for the measurement.

| Measurement | Description |
| --- | --- |
| Luminance entropy | Spread of brightness values |
| Neighbor difference | Mean brightness difference between adjacent pixels |
| Smooth pixel pairs | Share of adjacent pixels with almost identical brightness |
| JPEG recompression difference | JPEG only: difference after re-encoding at quality 0.9, with an 8× amplified difference image (ELA-style) |

These measurements support manual review. Smooth regions and regular edges are normal in diagrams, illustrations and edited photos, so the result is always reported as "AI origin cannot be determined".

## Views and controls

| View | Purpose |
| --- | --- |
| **Cards** | Review individual files, previews, metadata, AI evidence, provenance, pixel analysis and file actions |
| **Files** | Compare files in a sortable, searchable table, including risk, AI indicators, provenance and pixel analysis columns |
| **Tags** | Inspect tag names, IDs, groups, values, and sources |
| **Forensics** | Review risk findings, entropy, segments, extracted strings, and the hex header |

Search file names and metadata values, filter by file type or metadata presence (including **Has AI indicators** and **AI origin unknown**), and sort by name, size, modification time, type, tag count, or risk score. Tag filters let you narrow results by file, group, and source.

## Metadata editing and cleaning

| Operation | Support |
| --- | --- |
| Edit XMP fields | Title, description, creator, copyright, keywords, and rating |
| Export XMP sidecar | Download edited metadata as a separate XMP file (any format) |
| Embed XMP | Supported JPEG, PNG, and WebP images |
| Remove metadata | Selected metadata structures in JPEG, PNG, and WebP |

Image metadata removal operates without re-encoding image data. Modified files are downloaded as copies; the original files are not overwritten.

| Format | Removed | Kept |
| --- | --- | --- |
| JPEG | EXIF/XMP (APP1), IPTC (APP13), comments, data after end of image | ICC profile, C2PA (APP11) |
| PNG | `tEXt`, `zTXt`, `iTXt`, `eXIf` | ICC profile, C2PA (`caBX`) |
| WebP | `EXIF`, `XMP `, `ICCP` | C2PA and image data |

> [!IMPORTANT]
> Cleaning does **not** remove Content Credentials (C2PA). A cleaned copy can still identify the generator, provider or editing history. Check the copy again before sharing it.

A typical cleaning workflow:

1. Load an image and inspect its metadata.
2. Use **Remove metadata & save copy**.
3. Load the downloaded copy into MetaClean Pro to inspect the remaining metadata.

Removal targets supported metadata structures. It does not guarantee removal of every identifying detail, hidden payload, or information visible in the image itself.

## Offline rules and updates

The recognition rules (generator names and provider actions) are a dated snapshot embedded in the HTML file. The **Offline updates** panel lets you:

- **Import rule pack** — load a local JSON file with additional generators or provider actions. Loaded files are rechecked immediately.
- **Export current rules** — download the active rules as JSON.
- **Restore bundled rules** — return to the embedded snapshot.
- **Save updated HTML** — download a new copy of `MetaClean-Pro.html` with the selected rules embedded. The copy does not contain your loaded files or results.

Rule pack format:

```json
{
  "schemaVersion": 1,
  "version": "2026.09.12",
  "updated": "2026-09-12",
  "tools": [
    { "name": "Midjourney", "aliases": ["midjourney"] }
  ],
  "providers": [
    { "name": "Claude", "action": "com.anthropic.claude.provided" }
  ]
}
```

Limits: up to 100 tools (1–10 literal aliases each), up to 100 provider actions (fully qualified `com.vendor.action` names), less than 128 KiB, and no update date in the future.

Rule packs are data only. They are not authenticated, do not update the C2PA verifier and do not make certificates trusted. Updating the verifier or trust lists requires a new application build. No updates are fetched automatically.

## Reports and exports

| Export | Contents |
| --- | --- |
| Batch JSON | Metadata records for loaded files, including AI, provenance and pixel analysis results |
| All tags CSV | Metadata tags across loaded files |
| Filtered tags CSV | Tags matching the current filters |
| Filtered files CSV | File summaries matching the current filters |
| Forensic JSON | The selected file's forensic findings |
| Rules JSON | The active recognition rules |

Use CSV exports for spreadsheet review and JSON exports for further processing.

## Privacy and offline use

File inspection, C2PA verification, pixel analysis and editing run locally in the browser. The application does not upload files, load external scripts or fetch updates.

GPS map links open OpenStreetMap. Following a link sends the displayed coordinates to that external service. The footer link opens the GitHub project page. Report exports may also contain metadata such as GPS coordinates, creator names, prompts or extracted strings from the inspected files.

## Advantages and limitations

| Advantages | Limitations |
| --- | --- |
| Local processing without file uploads | Large files can consume substantial browser memory |
| One portable HTML file, works offline | File size of about 11 MB due to the embedded verifier |
| Offline C2PA signature verification | Trust lists are a snapshot; revocation is not checked |
| Batch inspection and searchable tags | Metadata coverage depends on the format |
| Cleaning without image re-encoding | Embedded editing and cleaning support JPEG, PNG, and WebP only; C2PA is kept |
| AI indicators with supporting evidence | Missing metadata cannot rule out AI generation |
| Forensic findings with supporting details | Heuristic analysis can produce false positives or miss anomalies |

High entropy is common in compressed or encrypted content. Secondary signatures can occur by coincidence. Some analysis of very large files is sampled, and displayed results may be capped for performance.

## Third-party components

The HTML file embeds [`@contentauth/c2pa-web`](https://github.com/contentauth/c2pa-js) 0.14.5 with its c2pa-wasm runtime (MIT, © Adobe) and highgain 0.1.0 (ISC), plus the official C2PA trust lists. License texts and provenance notes are included as a comment in the HTML file.

## License

MetaClean Pro is released under the [MIT License](./LICENSE).

---

**MetaClean Pro** · Inspect metadata · Verify provenance · Clean supported images
