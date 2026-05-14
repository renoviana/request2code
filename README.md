🌐 English | [Português](README.pt-BR.md) | [Español](README.es.md)

# request2code

A browser DevTools extension that captures network requests and converts them into ready-to-run code snippets. Available for Firefox and Chrome.

## Install

**Firefox** — Mozilla Add-ons Store:
[addons.mozilla.org/pt-BR/firefox/addon/request2code/](https://addons.mozilla.org/pt-BR/firefox/addon/request2code/)

**Chrome** — coming soon to the Chrome Web Store. For now, load it manually (see below).

## Features

- Captures all HTTP requests while DevTools is open
- Converts each request into executable code in 5 languages:
  - **Python** — `requests` library
  - **JavaScript** — `fetch` with `async/await`
  - **cURL** — command line
  - **PHP** — `curl_*` functions
  - **Go** — `net/http` package
- Correctly handles all body types: JSON, form-urlencoded, multipart/form-data, raw
- Extracts cookies from the `Cookie` header into a dedicated parameter (Python) or `-b` flag (cURL)
- Syntax highlighting for all languages
- Response viewer in three modes: Formatted, Interactive tree (JSON), Raw
- Filter by URL and resource type (XHR/Fetch, JS, CSS, Img, Doc, Other)
- Drag-to-resize between panels
- Preserve log (keeps requests when navigating to another page)
- UI available in Portuguese, English, and Spanish

## Repository structure

```
request2code/
├── firefox/             # Firefox extension (MV2, browser.* API)
│   ├── manifest.json
│   ├── devtools.html
│   ├── devtools.js
│   ├── panel.html
│   ├── panel.js
│   ├── lib/
│   │   └── converters.js
│   ├── _locales/
│   └── test/
├── chrome/              # Chrome extension (MV3, chrome.* API)
│   ├── manifest.json
│   ├── devtools.html
│   ├── devtools.js
│   ├── panel.html
│   ├── panel.js
│   └── lib/
│       └── converters.js
└── docs/
    ├── architecture.md
    └── adding-converters.md
```

Each folder is self-contained and can be packaged independently for store submission.

## Development

No build step, no bundler, no runtime dependencies. The code loads directly in the browser.

### Load in Firefox (temporary)

```
about:debugging → This Firefox → Load Temporary Add-on → select firefox/manifest.json
```

### Load in Chrome (temporary)

```
chrome://extensions → enable "Developer mode" → "Load unpacked" → select the chrome/ folder
```

### Reload after changes

| Modified file | What to do |
|---|---|
| `panel.js` / `panel.html` | Close and reopen the DevTools panel |
| `manifest.json` / `devtools.js` | Firefox: reload in `about:debugging` / Chrome: reload button in `chrome://extensions` |

### Tests

Tests cover `lib/converters.js` — pure functions with no browser API dependency.

```bash
npm test
```

Uses Jest. Test files are in `firefox/test/` and import `firefox/lib/converters.js`.

### Syncing changes between versions

`lib/converters.js` is duplicated in `firefox/lib/` and `chrome/lib/`. Any changes to converters or i18n must be applied to both files.

The only differences between versions are:
- `manifest.json` — manifest version, browser-specific settings
- `devtools.js` — `browser.*` (Firefox) vs `chrome.*` (Chrome)
- `panel.js` — `browser.devtools.network.*` (Firefox) vs `chrome.devtools.network.*` (Chrome)

### Adding a new output language

See [docs/adding-converters.md](docs/adding-converters.md).

### Publishing

**Firefox (AMO):**
```bash
cd firefox
npx web-ext lint
npx web-ext build
```

**Chrome (CWS):**
Zip the `chrome/` folder and upload it via the Chrome Developer Dashboard.
