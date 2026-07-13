# MyKK PDF Viewer

A fast, mobile-first, **single-file** PDF viewer. Drag & drop a `.pdf`
anywhere on the page and start reading instantly. It's a **viewer, not an
editor** — no toolbars, no accounts, no uploads. Everything runs locally in
your browser.

🔗 **Live:** <https://pdf-viewer.us/>

![Single file](https://img.shields.io/badge/build-single%20HTML%20file-success) ![No build step](https://img.shields.io/badge/build%20step-none-success) ![License](https://img.shields.io/badge/license-MIT-blue)

> Part of the **[File Viewer](https://file-viewer.us/) family** — HTML, Markdown,
> ePUB, PDF, and Data each have their own dedicated viewer. Use the **☰ menu**
> in the header to jump between them.

## Features

- 📄 **Drag & drop anywhere** — drop a `.pdf` on the page to read it. You can
  also **paste** a copied file, or tap the page to open a file picker.
- 🏷️ **Document title in the header** — pulled from the PDF's metadata (with
  the file name as a fallback) and shown in the top-left.
- 🗂️ **Outline / bookmarks** — a list button in the header opens a slide-in
  panel built from the PDF's outline, with nested sections. Click any entry to
  jump straight to that page. PDFs without an outline get a simple page list.
- 📜 **Continuous scroll** — all pages flow in one smooth column, rendered
  lazily as you scroll so even large documents open quickly and stay light on
  memory.
- 🔍 **Zoom** — zoom in/out (buttons, or `+` / `-`), and tap **Fit** (or press
  `0`) to snap back to fit-width. Renders crisply on high-DPI screens.
- ⏮️⏭️ **Page navigation** — Previous/Next buttons and a "page x / y" readout
  in a floating toolbar; type a page number to jump, or use `←`/`→`,
  `PageUp`/`PageDown`, and `Home`/`End`.
- 🔤 **Selectable text** — a real text layer sits over every page, so you can
  select and copy text just like in a desktop reader.
- 📋 **Copy** — copies your current text selection, or the current page's text
  if nothing is selected.
- 🗑️ **Clear** — closes the PDF and empties the view.
- 🔑 **Password-protected PDFs** — you're prompted for the password when a PDF
  is encrypted.
- 🎨 **Pick any background color** — a color swatch in the header tints the app
  and the gutter around the pages (the pages themselves stay as white "paper").
  The UI adapts for contrast, and your choice is **remembered** — saved to a
  cookie, with a `localStorage` fallback so even a downloaded `file://` copy
  remembers it. Starts on white.
- 🫥 **Distraction-free** — while you read, the header hides when you scroll
  **down** and comes back when you scroll **up** (or after a few seconds of
  stillness).
- 📱 **Mobile-first** & responsive, with safe-area support for notched phones.
- 🔒 **Safe by default** — your files are parsed and rendered entirely in the
  browser and never leave your device.
- 🪶 **One file, no build** — `index.html` is self-contained and the viewer
  works offline, even straight from `file://`.
- 📊 **Privacy-friendly analytics** — the hosted site uses a self-hosted,
  cookieless [Plausible](https://plausible.io/) instance (see [Privacy](#privacy)).

## Quick start

**Just open it.** Download [`index.html`](index.html), double-click it, and
you're done — it runs locally with no server, no build, and no internet
connection.

Prefer a local server?

```sh
python3 -m http.server 8080   # then open http://localhost:8080
```

Any static web server works too — there is nothing to build.

## Deploy to Cloudflare Pages (auto-deploy from this repo)

Connect the repository in the Cloudflare dashboard
(**Workers & Pages → Create → Pages → Connect to Git**) and use:

| Setting                  | Value            |
| ------------------------ | ---------------- |
| Production branch        | `main`           |
| Framework preset         | `None`           |
| Build command            | *(leave blank)*  |
| Build output directory   | `/`              |

It's a static site — Cloudflare just serves `index.html`. The included
[`_headers`](_headers) file applies security headers (CSP, `nosniff`,
`frame-ancestors`, etc.) automatically. Every push to `main` then
auto-deploys.

Add your custom domain `pdf-viewer.us` under the project's **Custom domains**
tab — Cloudflare creates the DNS record and provisions SSL for you.

## How it works

Everything is in [`index.html`](index.html):

- Your HTML/CSS/UI and the app logic — no build step, no tooling.
- [**PDF.js**](https://github.com/mozilla/pdf.js) `v3.11.174` (Apache-2.0)
  parses and renders each page to a `<canvas>` and produces the selectable
  text layer, entirely in memory.
- PDF.js runs in a **Web Worker** so parsing and rendering stay off the main
  thread. The worker is created from an inline `blob:` URL, so there are **no
  network requests** — and if a browser blocks blob workers, PDF.js
  transparently falls back to running on the main thread (so it still works
  from `file://`).
- Pages are rendered **lazily** with an `IntersectionObserver`: only pages near
  the viewport hold a canvas, so memory stays bounded on long documents.

The library is embedded inline (minified, with its license banner retained),
so the viewer renders with **no network dependencies** and works fully offline.
The only external request is the analytics script on the hosted site (see
[Privacy](#privacy)).

> **Note:** because it bundles the full PDF.js engine, `index.html` is larger
> than a plain-text viewer (~1.4 MB). That's the price of rendering real PDFs
> with zero network dependencies — and it's still a single file with no build.

## Limitations

- Interactive PDF **forms** and **annotations** (widgets, comments) are not
  rendered — this is a reader for viewing and reading, not filling forms.
- Scanned PDFs with no embedded text have nothing to select or copy (there's
  no OCR).
- Very large or complex PDFs render page-by-page as you scroll; the first pages
  appear quickly while the rest render on demand.

## Privacy

Your files stay on your device — they're parsed and rendered in the browser and
never uploaded anywhere, and your content is never sent to any server.

The hosted site at **pdf-viewer.us** uses a self-hosted
[Plausible](https://plausible.io/) instance for **privacy-friendly, cookieless**
page analytics: aggregate visit counts only — no personal data, no cross-site
tracking, no advertising. Plausible automatically ignores `localhost` and
`file://`, so a downloaded local copy reports nothing.

The only thing stored on your device is your chosen background color (a cookie,
with a `localStorage` fallback).

## License

[MIT](LICENSE) © 2026 Michal Ferber, aka **TechGuyWithABeard**.

The bundled third-party library retains its own license (PDF.js: Apache-2.0) —
see [`LICENSE`](LICENSE) for details.
