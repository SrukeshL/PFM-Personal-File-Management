[README.md](https://github.com/user-attachments/files/31162249/README.md)
# PFM — Personal File Management

A free, open-source, **privacy-first, offline-first** personal document vault that
runs entirely in your browser. Track your IDs, financial, medical, insurance, legal,
immigration, property, and tax documents — with expiry reminders, an encrypted vault,
and best-effort OCR auto-fill. **No account and no server: your document data and
files stay local to the browser.** (OCR currently has a one-time CDN dependency;
after it loads, OCR runs locally — see [Privacy & security](#privacy--security-model).)

> Single self-contained HTML file. Open it in any modern browser and it works.
> Licensed under the **GNU General Public License v3.0** — free forever.

---

## Why

Important personal documents scatter across drawers, inboxes, and phone photos, and
renewals sneak up on you. PFM keeps a single encrypted index of what you have, when
it expires, and where the scan lives — on your own machine, under your own password.

It shares its architecture with [PLCCMS](https://github.com/SrukeshL/PLCCMS-Family-Court-Edition):
a self-contained HTML app, browser-native storage, and AES-GCM-256 encryption.

---

## Features

- **Vault lock.** One password gates the whole app. Document metadata is encrypted at
  rest with **AES-GCM-256** and a key derived via **PBKDF2 (SHA-256, 250,000 iterations)**.
  Attached files remain local to the browser's IndexedDB and are included in encrypted
  `.pfm` exports. There is no recovery — the vault password never leaves your device.
- **Document register.** Type, category, issuer, number, tags, issued/expiry dates, and notes.
- **File attachments.** Drag-drop or pick a scan/PDF/image; the file is embedded in
  **IndexedDB** alongside its record, with an inline image preview.
- **OCR auto-fill (local processing, best-effort).** Drop an image and an in-browser OCR engine
  reads it, then guesses the document type, number, expiry, issued date, and category.
  It fills only empty fields and tells you what it filled so you can verify. Accuracy is
  rough by design — it pattern-matches, it does not "understand" the document.
- **Dashboard.** Total documents, items expiring within 60 days, already-expired items,
  a renewal-alert list, and a by-category breakdown.
- **Search & filter.** Full-text search across type, issuer, number, tags, and notes,
  plus a category filter.
- **Encrypted export / import.** Download a portable, password-protected
  `.pfm` bundle containing your records *and* embedded files. Import merges into your vault.

---

## Quick start

1. Download **`pfm.html`**.
2. Open it in a modern browser (Chrome, Edge, Firefox, Safari).
3. Set a vault password on first launch — this creates your encrypted store.
4. Click **+ Add document**, fill the form (or drop a scan and let OCR pre-fill it), and save.

That's it. Your document metadata is stored encrypted in this browser profile, on this
device. Attached files remain local to the browser.

### Back up your data

Use **Export** regularly. The `.pfm` file is encrypted and safe to store in cloud
backup or on a USB drive. To move to a new device or browser, open `pfm.html` there,
create a vault, and **Import** the file.

---

## Privacy & security model

- **Encryption.** Document metadata (types, numbers, dates, notes, tags) is encrypted
  at rest with **AES-GCM-256** using a PBKDF2-SHA256 key (250k iterations, random 16-byte
  salt, random 12-byte IV per encryption). The vault password is held in memory only while
  unlocked and is discarded on lock. **Attached files** are stored as blobs in a separate
  IndexedDB store, local to the browser but **not individually encrypted at rest**; they
  are encrypted when included in a `.pfm` export. Encrypting attachments at rest is on the
  roadmap.
- **No recovery.** If you forget your password, the data cannot be decrypted. This is a
  deliberate consequence of real encryption — keep your password safe.
- **Document data and files remain local to the browser.** No network requests are
  made for your records or attachments — document metadata is encrypted in IndexedDB,
  while attached files remain local as browser-stored blobs.
- **OCR's one-time CDN dependency.** The OCR engine (Tesseract.js) loads from a CDN the
  first time you use auto-fill; after loading, OCR runs locally. Your document images are
  processed in the browser and are **not** uploaded. If you open PFM with no internet,
  everything works **except** OCR auto-fill, which reports that the engine is unavailable
  and asks you to fill fields manually. A fully-inlined, zero-network build is on the roadmap.

> **Do not commit your own documents or `.pfm` exports to any public repository.**
> The published `pfm.html` ships empty — it contains no personal data.

---

## Testing

An automated regression suite (Node.js + jsdom + fake-indexeddb) drives the app
headlessly and verifies the vault, documents, crypto, OCR parser, and export/import.

```bash
cd pfm-tests
npm install
npm test
```

When all suites pass, the final line printed is `✅ ALL SUITES PASSED.`
See [`pfm-tests/README.md`](pfm-tests/README.md) for details and per-suite coverage.

---

## Roadmap

- Encrypt attached files at rest in IndexedDB (metadata is already encrypted; attachments
  are currently stored as local blobs and encrypted only in `.pfm` exports).
- Inline the OCR engine + language data for a **truly zero-internet** build.
- OCR the first page of PDFs (render to canvas).
- Per-document renewal lead-times and a "missing documents" checklist.
- Optional biometric / WebAuthn unlock.

---

## Contributing

Issues and pull requests are welcome. Please run `npm test` in `pfm-tests/` before
opening a PR, and keep the app a single self-contained `pfm.html` with no build step.
If you change the OCR parser in `pfm.html`, mirror the change in `pfm-tests/tests/04_ocr.test.js`.

---

## License

Copyright (C) 2026 Srukesh Lothukunta.

This program is free software: you can redistribute it and/or modify it under the terms
of the **GNU General Public License v3.0** as published by the Free Software Foundation.
It is distributed WITHOUT ANY WARRANTY. See [`LICENSE`](LICENSE) for the full text.
