---
name: web-tools
description: Provides four CLI utilities (search-tool, ddg-search, fetch-tool, download-tool) for web search, page fetch, and file download.
---

# Web Tools Skill for Coding Agents

## Overview
The **web-tools** package provides four command‑line utilities that coding agents can invoke to interact with the web:


| Tool | Purpose | Main modes |
|------|---------|-----------|
| **search-tool** | Perform a web search using the DuckDuckGo Instant Answer API. Returns instant answers from Wikipedia and curated sources. | `search-tool "<query>" -n <N>` |
| **ddg-search** | Perform a full web search by scraping DuckDuckGo HTML results. Returns comprehensive search results with title, url, and snippet. | `ddg-search "<query>" -n <N>` |
| **fetch-tool** | Load a single web page (headless Chrome) and return either the raw HTML or a structured JSON representation (title, headings, tables, links). | `fetch-tool "<url>" -m plain` or `fetch-tool "<url>" -m json` |
| **download-tool** | Download a single file (prefers `wget` if available, otherwise falls back to `requests`). Can output a JSON summary with file metadata. | `download-tool "<url>" -m plain` or `download-tool "<url>" -m json` |

All tools output **machine‑readable JSON** (or raw HTML for fetch‑tool plain mode) which downstream agents can parse and act upon.

### When to use which search tool?

| Scenario | Recommended Tool |
|----------|------------------|
| Quick lookup of a concept, person, or term | `search-tool` (Instant Answer API) |
| Full web search with comprehensive results | `ddg-search` (HTML scraping) |
| Finding specific websites, articles, or resources | `ddg-search` |
| Getting Wikipedia-style summaries | `search-tool` |


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
uv tool install "git+https://github.com/uv-genai/web-tools.git@v0.1.8"
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
- `ddg-search`
- `fetch-tool`
- `download-tool`

Verify installation:
```bash
search-tool --help
ddg-search --help
fetch-tool --help
download-tool --help
```


---

## 2. Search Tool (`search-tool`) - Instant Answers

### Command
```bash
search-tool "<query>" -n <N>
```
* `<query>` – the search string (quotes required if it contains spaces).
* `-n <N>` – number of results to return (default 10).

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
Each object represents a search result from the DuckDuckGo Instant Answer API. **Note**: This API only returns results from curated sources like Wikipedia. For full web search, use `ddg-search`.

### Example
```bash
search-tool "Python programming language" -n 3
```
```json
[
  {"title":"Python (programming language) - Wikipedia","url":"https://en.wikipedia.org/wiki/Python_(programming_language)","snippet":""},
  ...
]
```

---

## 3. DuckDuckGo Search (`ddg-search`) - Full Web Search

### Command
```bash
ddg-search "<query>" -n <N>
```
* `<query>` – the search string (quotes required if it contains spaces).
* `-n <N>` – number of results to return (default 10).

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
Each object represents a search result with a descriptive snippet. This tool scrapes DuckDuckGo HTML search results using Selenium, providing comprehensive web search coverage.

### Example
```bash
ddg-search "open source licenses" -n 3
```
```json
[
  {
    "title": "Open source license - Wikipedia",
    "url": "https://en.wikipedia.org/wiki/Open-source_license",
    "snippet": "Open-source licenses are licenses that comply with the Open Source Definition..."
  },
  {
    "title": "Choose a License - Creative Commons",
    "url": "https://creativecommons.org/choose/",
    "snippet": "Creative Commons licenses provide a flexible range of protections..."
  },
  ...
]
```

### When to use `ddg-search` vs `search-tool`

Use `ddg-search` when you need:
- Comprehensive web search results (not just Wikipedia/curated sources)
- Snippets/descriptions for each result
- To find specific websites, articles, or resources

Use `search-tool` when you need:
- Quick lookups from Wikipedia and other curated sources
- Faster results (no Selenium overhead)
- Lightweight API-based search

---

## 4. Fetch Tool (`fetch-tool`)

### Command
```bash
# Raw HTML (default)
fetch-tool "<url>" -m plain

# Structured JSON
fetch-tool "<url>" -m json
```
* `<url>` – the web page to retrieve.
* `-m plain` – prints the page's HTML to stdout (binary‑safe). 
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

## 5. Download Tool (`download-tool`)

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
`size` is the number of bytes, `sha256` is the file's SHA‑256 hash, and `error` contains any failure message.

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

## 6. Workflow Patterns for Agents

1. **Search → Fetch → Process**
   ```bash
   # 1) Get URLs from a search (use ddg-search for comprehensive results)
   urls=$(ddg-search "python web scraping" -n 5 | jq -r '.[].url')
   # 2) For each URL, fetch structured data
   for u in $urls; do
       fetch-tool "$u" -m json | jq '.'
   done
   ```

2. **Search → Download**
   ```bash
   # Find a direct download link via search, then download it
   dl_url=$(ddg-search "latest pandas wheel" -n 1 | jq -r '.[0].url')
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

4. **Quick lookup with search-tool**
   ```bash
   # Get Wikipedia-style instant answers
   search-tool "Python programming language" -n 3 | jq '.'
   ```

5. **Comprehensive search with ddg-search**
   ```bash
   # Full web search with snippets
   ddg-search "how to install docker on ubuntu" -n 5 | jq '.'
   ```

These patterns let a coding agent orchestrate web interactions without needing to write any additional code – just invoke the provided CLI utilities.

---

## 7. Error handling
* If a tool returns a non‑zero exit code, the agent should treat the operation as failed and inspect the `error` field (for `download-tool`) or the absence of output (for `search-tool`/`ddg-search`/`fetch-tool`).
* For `download-tool` JSON mode, check `status`. If it is `failed`, the `error` field contains a human‑readable description.
* For `fetch-tool` plain mode, a missing file or empty output usually indicates a network or rendering problem.
* For `ddg-search`, an empty array `[]` means no results were found or the page failed to load.

---

## 8. Extending the Skill
If new capabilities are required (e.g., POST requests, authentication, or crawling), add a new CLI entry point to `pyproject.toml` and implement the logic under `src/web_tools/`. Update this `SKILL.md` with the new usage examples.

---

*End of Skill description.*