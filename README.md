# Save Image as PDF – PNG, JPG, WebP

A privacy-first Chrome extension (Manifest V3) to save or convert any web image to
**PDF, PNG, JPG, WebP or BMP** — and to **combine multiple images into one PDF**.
Everything runs locally; no uploads, no account, minimal permissions.

## Features
- Right-click any image → save/convert to PDF, PNG, JPG, WebP, BMP.
- **Combine multiple images into one multi-page PDF** (flagship).
- **Grab all images on a page** and save them in one go.
- Real PDF controls: page size (A4/Letter/fit-to-image), orientation, margins, placement, quality.
- 100% local conversion — no image is ever uploaded; the only network request is
  downloading the image you chose to save. No accounts, no analytics.

## Load it (development)
1. Open `chrome://extensions`.
2. Toggle **Developer mode** (top-right).
3. Click **Load unpacked** and select this folder.
4. Pin the extension, then right-click any image or open the popup.

## Project layout
```
manifest.json            MV3 manifest (i18n name/description)
src/
  background.js          service worker: menus, page-context fetch, downloads
  offscreen.html/js      canvas decode + raster conversion + jsPDF assembly
  content/grab.js        page overlay for multi-select / grab-all
  content/overlay.css    overlay styles
  popup/                 toolbar popup (quick actions + default format)
  options/               settings incl. PDF layout controls
  common/settings.js     shared settings model + chrome.storage helpers
  lib/jspdf.umd.min.js   vendored jsPDF (no build step, no CDN)
_locales/en/messages.json   externalized strings (add locales here)
icons/                   16/32/48/128 (placeholders — replace before publishing)
store-listing/           CWS title/description, screenshot shot-list, review notes
```

## Build/packaging
No build step. To create an upload zip:
```
zip -r save-image-as-pdf.zip manifest.json src icons _locales -x '*.DS_Store'
```

## How images are read
- **http(s)** images are fetched inside a local offscreen document, which has
  `host_permissions` and so can read cross-origin/CDN images that a content-script
  fetch could not.
- **data:** URLs are used directly.
- **blob:** URLs are page-scoped, so they're read inside the page via `scripting`.

## Notes / limitations
- Images behind auth that relies on the page's session may fail if the CDN doesn't
  accept the extension's credentialed request; such images are skipped in batch mode.
- WebP encoding depends on the browser; if unsupported it falls back to PNG. BMP is written directly (always available).
- Replace the generated placeholder icons with a designed icon before publishing.
