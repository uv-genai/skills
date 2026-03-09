---
name: web-tools
description: Provides three CLI utilities (search-tool, fetch-tool, download-tool) for web search, page fetch, and file download.
---

# Web Tools Skill for Coding Agents

## Overview
The **web-tools** package provides three command‑line utilities that coding agents can invoke to interact with the web:


| Tool | Purpose | Main modes |
|------|---------|-----------|
| **search-tool** | Perform a web search using the DuckDuckGo Instant Answer API and return the first N results as JSON. | `search-tool "<query>" -n <N>` |
| **fetch-tool** | Load a single web page (headless Chrome) and return either the raw HTML or a structured JSON representation (title, headings, tables, links). | `uv fetch-tool "<url>" -m plain` or `fetch-tool "<url>" -m json` |
| **download-tool** | Download a single file (prefers `wget` if available, otherwise falls back to `requests`). Can output a JSON summary with file metadata. | `download-tool "<url>" -m plain` or `download-tool "<url>" -m json` |

All tools output **machine‑readable JSON** (or raw HTML for fetch‑tool plain mode) which downstream agents can parse and act upon.


## 1. Prerequisites & Installation

### Requirement: `uv`
The **web-tools** package relies on `uv` for dependency management and execution. If `uv` is not installed, install it first:

```bash
# Install uv (one-time setup)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Installing the web-tools package

#### Option A: Install from GitHub (recommended for latest version)
```bash
# Install directly from the main branch
uv tool install "git+https://github.com/uv-genai/web-tools.git"

# Or install a specific tag/version
uv tool install "git+https://github.com/uv-genai/web-tools.git@v0.1.6"
```

#### Option B: Clone and install locally
```bash
git clone https://github.com/uv-genai/web-tools.git
cd web-tools
uv venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
uv sync                     # installs all dependencies
```

After installation, the following commands will be available on your `$PATH`:
- `search-tool`
- `fetch-tool`
- `download-tool`

Verify installation:
```bash
search-tool --help
fetch-tool --help
download-tool --help
```


---

## 2. Search Tool (`search-tool`)

### Command
```bash
search-tool "<query>" -n <N>
```
* `<query>` – the search string (quotes required if it contains spaces). n* `-n <N>` – number of results to return (default 10).

### JSON output schema
```json
[
  {
    "title": "string",
    "url": "string",
    "snippet": "string"
  },
  ...
]
```
Each object represents a search result. Agents can iterate over the array, extract URLs, and feed them to `fetch-tool` or `download-tool`.

### Example
```bash
search-tool "open source licenses" -n 3
```
```json
[
  {"title":"Open source licenses – Wikipedia","url":"https://en.wikipedia.org/wiki/Open_source_license","snippet":""},
  {"title":"Open‑source license – The Week","url":"https://theweek.com/...","snippet":""},
  {"title":"Choosing a License – The Week","url":"https://theweek.com/...","snippet":""}
]
```
---

## 3. Fetch Tool (`fetch-tool`)

### Command
```bash
# Raw HTML (default)
fetch-tool "<url>" -m plain

# Structured JSON
fetch-tool "<url>" -m json
```
* `<url>` – the web page to retrieve.
* `-m plain` – prints the page’s HTML to stdout (binary‑safe). 
* `-m json` – prints a JSON object with extracted structure.

### JSON output schema (when `-m json`)
```json
{
  "title": "string",
  "headings": [
    {"level": int, "text": "string"},
    ...
  ],
  "tables": [
    {"headers": ["string"], "rows": [["string", ...]], ...
  ],
  "links": [
    {"text": "string", "href": "string"},
    ...
  ]
}
```
The `headings` array contains all `<h1>`‑`<h6>` tags with their level. `tables` includes any `<table>` found (headers + rows). `links` captures every `<a href>`.

### Example
```bash
fetch-tool "https://example.com" -m json
```
```json
{
  "title":"Example Domain",
  "headings":[{"level":1,"text":"Example Domain"}],
  "tables":[],
  "links":[{"text":"More information...","href":"https://www.iana.org/domains/example"}]
}
```
---

## 4. Download Tool (`download-tool`)

### Command
```bash
# Plain mode – writes file to current directory (or -o destination)
download-tool "<url>" -m plain

# JSON summary mode
download-tool "<url>" -m json
```
* `-o <path>` – optional destination filename/path. If omitted, the basename of the URL is used.
* `-m plain` – streams the file bytes to stdout (useful for pipelines).
* `-m json` – prints a JSON summary after the download completes.

### JSON output schema (when `-m json`)
```json
{
  "url": "string",
  "dest": "string",
  "status": "ok" | "failed",
  "size": int | null,
  "sha256": "string" | null,
  "error": "string" | null
}
```
`size` is the number of bytes, `sha256` is the file’s SHA‑256 hash, and `error` contains any failure message.

### Example
```bash
download-tool "https://example.com/file.zip" -m json
```
```json
{
  "url":"https://example.com/file.zip",
  "dest":"file.zip",
  "status":"ok",
  "size":1245789,
  "sha256":"c3b0e5a5e5f2c8c9d4a5b9e3e7f2d9c6a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
  "error":null
}
```
---

## 5. Workflow Patterns for Agents

1. **Search → Fetch → Process**
   ```bash
   # 1) Get URLs from a search
   urls=$(search-tool "python web scraping" -n 5 | jq -r '.[].url')
   # 2) For each URL, fetch structured data
   for u in $urls; do
       fetch-tool "$u" -m json | jq '.'
   done
   ```
2. **Search → Download**
   ```bash
   # Find a direct download link via search, then download it
   dl_url=$(search-tool "latest pandas wheel" -n 1 | jq -r '.[0].url')
   download-tool "$dl_url" -m json
   ```
3. **Fetch → Download assets**
   ```bash
   # Extract all links from a page and download each as a file
   fetch-tool "https://example.com" -m json \
       | jq -r '.links[].href' \
       | while read link; do
           download-tool "$link" -m json
         done
   ```

These patterns let a coding agent orchestrate web interactions without needing to write any additional code – just invoke the provided CLI utilities.

---

## 6. Error handling
* If a tool returns a non‑zero exit code, the agent should treat the operation as failed and inspect the `error` field (for `download-tool`) or the absence of output (for `search-tool`/`fetch-tool`).
* For `download-tool` JSON mode, check `status`. If it is `failed`, the `error` field contains a human‑readable description.
* For `fetch-tool` plain mode, a missing file or empty output usually indicates a network or rendering problem.

---

## 7. Workflow Patterns for Agents

---

## 8. Extending the Skill
If new capabilities are required (e.g., POST requests, authentication, or crawling), add a new CLI entry point to `pyproject.toml` and implement the logic under `src/web_tools/`. Update this `SKILL.md` with the new usage examples.

---

*End of Skill description.*
