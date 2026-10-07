---
name: free-web
description: Completely free, API-key-free web search and page fetching. Uses DuckDuckGo (via the ddg-search CLI) for search and Obscura (a local headless browser) for clean, chrome-free content. Use when you want to search the web and read pages with zero API keys and zero cost.
---

# Free Web Skill (DuckDuckGo + Obscura)

## Overview

A **100% free, no-API-key** web stack:

| Tool | Role | Needs |
|------|------|-------|
| **ddg-search** | Web search via DuckDuckGo (scrapes DDG HTML) | nothing — no key |
| **obscura** | Fetch + render any page locally as clean content | local binary only — no key |

**Core pattern:** search with `ddg-search` → fetch the best result with Obscura as **clean, scoped content**.

Because neither tool needs an API key, there is **no need to run through a login shell** (`zsh -lc` etc.) — just call them directly.

---

## Prerequisites

### ddg-search
`ddg-search` on `$PATH` (ships with the `web-tools` skill set; installed at `~/.local/bin/ddg-search`). No key, no signup.

### Obscura
The `obscura` binary. If built into a target dir, alias it:
```bash
alias obscura=/tmp/obscura-target/debug/obscura
# or install permanently:
cargo install --path <obscura-src>/crates/obscura-cli
```
`scrape` mode additionally needs the `obscura-worker` binary (`cargo build -p obscura-cli --bin obscura-worker`).

> No `BRAVE_API_KEY`, no `LINKUP_API_KEY`, no login-shell wrapper required.

---

## 1. Search with DuckDuckGo (`ddg-search`)

```bash
ddg-search "<query>"            # first 10 results (JSON)
ddg-search "<query>" -n 5       # first N results (JSON)
```
Output is a **JSON array** (always — there is no `--json` flag):
```json
[
  { "title": "Fusion power - Wikipedia",
    "url": "https://en.wikipedia.org/wiki/Fusion_power",
    "snippet": "Fusion power is a potential method of electric power generation..." },
  ...
]
```
Pull URLs with `jq`:
```bash
ddg-search "<query>" -n 5 | jq -r '.[].url'
```

---

## 2. Fetch with Obscura — CLEAN SCOPED CONTENT

A raw page dump is full of navigation, sidebars, menus, and cookie banners. To get **just the article/content**, scope the fetch to the content container.

### The rule
```bash
obscura fetch "<url>" --selector "<content selector>" --dump text --quiet
```
- `--selector "<css>"` limits extraction to the matching element.
- `--dump text` returns clean visible text with the page chrome stripped.
- `--quiet` suppresses progress logs.

### ⚠️ Critical gotcha: `--selector` scopes `text`, NOT `markdown`
Verified behavior:
- `--selector "..." --dump text` → **scoped, chrome-free** ✅
- `--selector "..." --dump markdown` → **ignores the selector, returns the full page with nav chrome** ❌

**So for clean output, always pair `--selector` with `--dump text`.** Use `--dump markdown` only when you want the whole page (links preserved) and don't mind the chrome.

### Choosing the content selector
`--selector` uses `querySelector` semantics (first match). Pass a **comma-separated fallback list** so it works across many site types:
```bash
obscura fetch "<url>" --selector "article, main, [role=main], #content, .post-content, .entry-content" --dump text --quiet
```

Common containers by site type:
| Site type | Selector |
|-----------|----------|
| Generic / blogs | `article, main, [role=main], #content` |
| Wikipedia | `#mw-content-text` |
| WordPress / CMS blogs | `.entry-content, .post-content, article` |
| Docs sites | `main, .markdown-body, .theme-doc-markdown, .md-content` |
| News | `article, [itemprop=articleBody], main` |

### Verify the content is clean
```bash
obscura fetch "<url>" --selector "article, main, #content" --dump text --quiet > out.txt
grep -c "Main menu\|Sign in\|Navigation\|Cookie" out.txt   # 0 = chrome stripped
```

### Bot-protected pages — add `--stealth`
```bash
obscura fetch "<url>" --selector "article, main" --dump text --stealth --quiet
```
`--stealth` gives a consistent browser fingerprint + TLS impersonation + tracker blocking.

---

## 3. Workflow: search → clean fetch

```bash
# 1. Search (free, no key)
ddg-search "fusion energy" -n 5 > results.json

# 2. Fetch each result as clean content
for url in $(jq -r '.[].url' results.json); do
  obscura fetch "$url" --selector "article, main, [role=main], #content" --dump text --quiet
done
```
If a page returns empty or is still chrome-heavy, widen the selector list or fall back to `--dump text` without a selector and post-strip.

---

## 4. Other useful Obscura dumps

```bash
obscura fetch "<url>" --dump markdown   # full page as markdown (links preserved, includes chrome)
obscura fetch "<url>" --dump html       # full rendered DOM
obscura fetch "<url>" --dump links      # every link + its text
obscura fetch "<url>" --dump original   # raw body — JSON APIs, images, CSS, JS (bypasses browser)
obscura fetch "<url>" --dump assets     # sub-resource URLs (one JSON per line)
obscura fetch "<url>" --dump cookies    # cookie jar incl. HttpOnly (session tokens)
```

### Batch fetch
```bash
obscura fetch --file urls.txt --concurrency 5 --quiet
# one JSON status line per URL: {url, ok, status, content_type, bytes, elapsed_ms}
# (# comment lines and blank lines in the file are skipped)
```

### Structured scrape (JS eval → JSON)
```bash
obscura scrape "<url>" -e "({ title: document.querySelector('h1').textContent, paras: document.querySelectorAll('p').length })" -q
# requires the obscura-worker binary
```

### Bonus: search via Obscura directly (no ddg-search)
Obscura can render the DuckDuckGo results page and extract the links itself:
```bash
obscura fetch "https://html.duckduckgo.com/html/?q=YOUR+QUERY" --dump links --quiet
```

---

## Notes & limits
- **Reentrancy abort on some JS-heavy pages.** Certain pages can trigger an upstream reentrancy bug (`op_initial_frame` re-entering `op_register_document_realm` → `panic in a function that cannot unwind` → SIGABRT / exit 134). This affects **both the CDP `serve` path AND the CLI `fetch` path** (observed aborting on a JS-heavy news article via plain `fetch`). It is page-specific, not universal — most pages fetch fine.
  - **If a fetch aborts:** retry a different source from the search results, or use `--dump original` (bypasses the browser/JS layer entirely) to get the raw body. For a clean-text fallback when the rendered path aborts, `--dump original` + a local HTML→text strip is the workaround.
- `--dump original` bypasses rendering — use it for non-HTML resources (JSON, images).
- DuckDuckGo scraping is rate-limited; keep `-n` modest and add small delays for bulk queries.
- For a page where no content selector works, `--dump text` (whole page) + a post-process strip is the fallback.

---

## Quick reference
```bash
# search (free, no key)
ddg-search "query" -n 5
# clean content (the go-to)
obscura fetch "<url>" --selector "article, main, [role=main], #content" --dump text --quiet
# clean + stealth (bot-walled)
obscura fetch "<url>" --selector "article, main" --dump text --stealth --quiet
# raw JSON API
obscura fetch "<url>" --dump original --quiet
```
