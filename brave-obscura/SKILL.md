---
name: brave-obscura
description: Web search with Brave and clean page fetching with Obscura (a local headless browser). Use to search the web and fetch any result as clean, chrome-free text — no Linkup and no API credits for fetching. Pairs Brave (search) with Obscura (fetch/render).
---

# Brave + Obscura Web Skill

## Overview

Two complementary tools, no Linkup required:

| Tool | Role | Needs |
|------|------|-------|
| **brave-search** | Web search (Brave's independent index) | `BRAVE_API_KEY` |
| **obscura** | Fetch + render any page locally as clean content | local binary only (free, no key) |

**Core pattern:** search with Brave → fetch the best results with Obscura as **clean, scoped content**.

Obscura is a ~70 MB Rust headless browser that renders JavaScript like a real browser, so it works on SPAs and JS-heavy pages — and it costs nothing per call.

---

## Prerequisites

### Brave
`brave-search` on `$PATH` and a `BRAVE_API_KEY` (free tier: 2,000/mo from https://api-dashboard.search.brave.com/).

### Obscura
The `obscura` binary. If built into a target dir, alias it:
```bash
alias obscura=/tmp/obscura-target/debug/obscura
# or install permanently:
cargo install --path <obscura-src>/crates/obscura-cli
```
`scrape` mode additionally needs the `obscura-worker` binary (`cargo build -p obscura-cli --bin obscura-worker`).

### ⚠️ If `BRAVE_API_KEY` appears unset — try a login shell first
Agents usually run in a **non-login, non-interactive** shell (`bash -c`) that does **not** source `~/.zprofile`, `~/.profile`, or `~/.bash_profile`. A key exported there looks empty. **Before reporting it unset, retry through a login shell:**
```bash
zsh -lc 'brave-search "query" -n 5 --json'
bash -lc 'brave-search "query" -n 5 --json'
fish --login -c 'brave-search "query" -n 5 --json'
# presence check without leaking the secret:
zsh -lc 'echo BRAVE=${BRAVE_API_KEY:+<set>}'
```
Only report unset if it is absent across these login shells. (Obscura needs no key.)

---

## 1. Search with Brave

```bash
brave-search "<query>" -n <N>            # formatted results
brave-search "<query>" -n <N> --json     # machine-readable
```
JSON shape: `{ query, num_results_found, results: [{ title, url, description, engine }] }`.

---

## 2. Fetch with Obscura — CLEAN SCOPED CONTENT

This is the important part. A raw page dump is full of navigation, sidebars, menus, and cookie banners. To get **just the article/content**, scope the fetch to the content container.

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
# 1. Search
brave-search "neutral atom quantum computers" -n 5 --json > results.json

# 2. Fetch each result as clean content
for url in $(jq -r '.results[].url' results.json); do
  obscura fetch "$url" --selector "article, main, [role=main], #content" --dump text --quiet
done
```
If a page returns empty or still-chrome-heavy, widen the selector list or fall back to `--dump text` without a selector and post-strip.

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

---

## Notes & limits
- Obscura renders JavaScript (full browser engine) — good for SPAs and dynamic pages.
- **Reentrancy abort on some JS-heavy pages.** Certain pages can trigger an upstream reentrancy bug (`op_initial_frame` re-entering `op_register_document_realm` → `panic in a function that cannot unwind` → SIGABRT / exit 134). This affects **both the CDP `serve` path AND the CLI `fetch` path** (observed aborting on a JS-heavy news article via plain `fetch`). It is page-specific, not universal — most pages fetch fine.
  - **If a fetch aborts:** retry a different source from the search results, or use `--dump original` (bypasses the browser/JS layer entirely) to get the raw body. For a clean-text fallback when the rendered path aborts, `--dump original` + a local HTML→text strip is the workaround.
- `--dump original` bypasses rendering — use it for non-HTML resources (JSON, images).
- For a page where no content selector works, `--dump text` (whole page) + a post-process strip is the fallback.

---

## Quick reference
```bash
# search
brave-search "query" -n 5 --json
# clean content (the go-to)
obscura fetch "<url>" --selector "article, main, [role=main], #content" --dump text --quiet
# clean + stealth (bot-walled)
obscura fetch "<url>" --selector "article, main" --dump text --stealth --quiet
# raw JSON API
obscura fetch "<url>" --dump original --quiet
```
