# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-page HTML application: **Base64 编码转换工具** (Base64 Encoder/Decoder Tool)

- **Main file**: `file2base64.html` - Self-contained HTML/CSS/JS application
- **Functionality**: Bidirectional conversion between files (images, ZIP, PDF, etc.) and Base64/Data URI format
- **No build system**: Static HTML file opens directly in browser

## Architecture

```
file2base64.html
├── CSS Styles (inline, ~230 lines)
├── HTML Structure (~280 lines)
└── JavaScript Logic (~320 lines)
    ├── File → Base64 (encoding via FileReader.readAsDataURL)
    └── Base64 → File (decoding via atob() + Blob)
```

## Development

### Running the Application

Open `file2base64.html` directly in a web browser:

```bash
open file2base64.html
```

### Key Features

- **Drag & drop** file upload with preview
- **Image preview** for image/* MIME types
- **File icon preview** for non-image files (ZIP, PDF, etc.)
- **Copy to clipboard** for Base64 output
- **Download as text file** for Base64 results
- **Paste from clipboard** for Base64 input
- **MIME type detection** with file extension mapping (60+ types supported)
- **Blob-based handling** for large file performance

### Core Functions

| Function | Purpose |
|----------|---------|
| `processFile(file)` | Reads file via FileReader, outputs Data URI |
| `base64ToBlob(base64, mimeType)` | Converts Base64 string to Blob for download |
| `isImageFile(mimeType)` | Checks if MIME type starts with `image/` |
| `getExtensionFromMime(mimeType)` | Maps MIME types to file extensions |
| `formatSize(bytes)` | Formats file size (B/KB/MB) |
| `showStatus(element, message, isError)` | Displays temporary status messages |

## File Structure

```
tool_file2base64/
├── file2base64.html      # Main application (single file, ~630 lines)
├── template.png          # Reference image for UI design
└── .superpowers/         # Brainstorming session artifacts
```
