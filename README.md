# ChronoRename Studio

A client-side photo sorting and batch renaming studio designed for macOS photographers, handling burst sequences, multi-camera shoots, and manual custom arrangements with sub-second precision.

## Architecture & Technology Stack

- **Zero-Build Single File**: `index.html` contains the entire application (HTML, CSS via Tailwind CDN, and vanilla ES6+ JavaScript).
- **Client-Side Privacy**: Runs completely offline in the browser. Photos never leave the user's computer.
- **EXIF / XMP Engine**: Powered by `exifr` (Full UMD bundle) with binary fallbacks for XMP packet extraction and SubSecTimeOriginal sub-second burst parsing.
- **High-Performance Memory & Thumbnail Engine**: Multi-tier client-side thumbnailing combining instant embedded EXIF preview extraction via `exifr.thumbnailUrl` with native `createImageBitmap`/Canvas downscaling (400px bounds) and `decoding="async"`. Prevents VRAM exhaustion and browser freezing when loading hundreds of high-resolution DSLR/mirrorless RAW and JPEG files.
- **Hardware-Accelerated Pointer & Native Drag Engines**: Table rows use native HTML5 drag-and-drop, while the fullscreen grid view utilizes an ultra-responsive, zero-latency Pointer Events engine (`pointerdown`, `pointermove`, `pointerup`) with direct coordinate resolution and edge auto-scrolling, eliminating the 2–3 second OS drag initialization lag on macOS and Chromium without external UI libraries.

## Core Features & Sorting Modes

1. **Chronological EXIF (`ASC` / `DESC`)**:
   - Parses EXIF/XMP timestamps down to millisecond precision (`SubSecTimeOriginal`, `SubSecTimeDigitized`).
   - Resolves multi-camera drift and burst sequence collision.
2. **Manual & Drop Sequencing (`MANUAL`)**:
   - **FIFO Drop Sequencing**: When dropping photos progressively, files preserve their exact arrival sequence without forcing an EXIF timestamp reorder.
   - **In-App Drag & Drop Reordering**: Table rows and grid cards can be freely dragged up or down to set the exact order of renaming. In grid view, an ultra-fast hardware-accelerated Pointer Events engine renders the visual insertion indicator line in sub-millisecond real time (0 ms latency, 60/120fps), eliminating the 2–3 second OS drag session delay on macOS and Chromium. Cards never shift or reflow during pointer movements; photos are physically reordered in the DOM only upon mouse release. Every point inside the grid is a valid drop target (card halves, gaps, padding, empty space below the last row), and auto-scroll activates smoothly when dragging near the viewport edges. External photo files dropped from Finder into the grid or dropzones continue to import via native file drop handlers.
   - **Multi-Selection Batch Reorder** (Grid View): `Ctrl/⌘+click` toggles individual cards, `Shift+click` selects contiguous ranges. Dragging any selected card moves the entire selection as a batch while preserving relative order. Double-click opens the metadata inspector.
   - **Snug Compact Grid Cards**: Minimalist cards displaying exclusively the uncropped image preview (`object-contain`) and the target file name, omitting unnecessary metadata noise.
   - **Fullscreen Grid Mode**: The `Griglia` segment of the view switcher opens the grid straight into its fullscreen window; no cramped inline grid is rendered in between. Fullscreen re-parents the live grid into `#gridFullscreenView`, a file-manager style window: a 52px chrome bar carrying the sequence title, the live selection status, zoom controls and an "Esci" button. The zoom controls pair the range slider (70px–250px, 10px per notch) with `−` / `+` magnifier buttons that step exactly one notch per click and disable themselves at the limits; the slider stays the single source of truth for min, max and step, above a grid that fills the viewport and scrolls on its own while the page behind it stays frozen. `Esc`, the toolbar button, the browser's native fullscreen exit or `Elenco` all close it and return to the list view, where the grid container is restored to its original slot in the DOM. Native Fullscreen API is requested on enter and released on exit (best-effort: the overlay fills the viewport either way).
   - **Quick Nudge Controls**: Dedicated ▲ and ▼ buttons on each table row provide instant single-click position shifting.
   - Automatically switches to manual sorting whenever an item is moved.
3. **Collision Detection & Duplicate Prevention**:
   - Live checks for duplicate target filenames.
   - Automatically highlights collisions before terminal script generation.
4. **macOS Native Scripting**:
   - Generates safe atomic `mv -n` shell commands.
   - Provides full 1-click Rollback (Undo) bash script generation.
