# ChronoRename Studio

A client-side photo sorting and batch renaming studio designed for macOS photographers, handling burst sequences, multi-camera shoots, and manual custom arrangements with sub-second precision.

## Architecture & Technology Stack

- **Zero-Build Single File**: `index.html` contains the entire application (HTML, CSS via Tailwind CDN, and vanilla ES6+ JavaScript).
- **Client-Side Privacy**: Runs completely offline in the browser. Photos never leave the user's computer.
- **EXIF / XMP Engine**: Powered by `exifr` (Full UMD bundle) with binary fallbacks for XMP packet extraction and SubSecTimeOriginal sub-second burst parsing.
- **Native HTML5 Drag & Drop**: Native row and grid drag reordering without bloated external UI libraries.

## Core Features & Sorting Modes

1. **Chronological EXIF (`ASC` / `DESC`)**:
   - Parses EXIF/XMP timestamps down to millisecond precision (`SubSecTimeOriginal`, `SubSecTimeDigitized`).
   - Resolves multi-camera drift and burst sequence collision.
2. **Manual & Drop Sequencing (`MANUAL`)**:
   - **FIFO Drop Sequencing**: When dropping photos progressively, files preserve their exact arrival sequence without forcing an EXIF timestamp reorder.
   - **In-App Drag & Drop Reordering**: Table rows and grid cards can be freely dragged up or down to set the exact order of renaming. In grid view, a live animated FLIP placeholder dynamically shifts other cards to visually reveal the landing destination before mouse release.
   - **Multi-Selection Batch Reorder** (Grid View): `Ctrl/⌘+click` toggles individual cards, `Shift+click` selects contiguous ranges. Dragging any selected card moves the entire selection as a batch while preserving relative order. Double-click opens the metadata inspector.
   - **Snug Compact Grid Cards**: Minimalist cards displaying exclusively the uncropped image preview (`object-contain`) and the target file name, omitting unnecessary metadata noise.
   - **Zoom Controls & Fullscreen Grid Mode**: Dual-mode zoom slider allows resizing cards between 70px and 250px in both standard and fullscreen views. Fullscreen expands to the entire screen with sticky toolbar and `Esc` key dismissal.
   - **Quick Nudge Controls**: Dedicated ▲ and ▼ buttons on each table row provide instant single-click position shifting.
   - Automatically switches to manual sorting whenever an item is moved.
3. **Collision Detection & Duplicate Prevention**:
   - Live checks for duplicate target filenames.
   - Automatically highlights collisions before terminal script generation.
4. **macOS Native Scripting**:
   - Generates safe atomic `mv -n` shell commands.
   - Provides full 1-click Rollback (Undo) bash script generation.
