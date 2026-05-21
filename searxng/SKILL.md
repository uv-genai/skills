---
name: searxng
description: Search the web using a self-hosted SearXNG metasearch engine.
---

# SearXNG Search Skill

## Overview
Search the web using a self-hosted SearXNG metasearch engine. Returns clean JSON results with no CAPTCHAs or bot detection.

## Tool
```bash
searx "<query>" [-n N] [-c category] [-l language]
```

## Options
| Flag | Description | Default |
|------|-------------|---------|
| `-n N` | Number of results | 10 |
| `-c category` | Search category | `general` |
| `-l language` | Language code | `en` |

Categories: `general`, `news`, `images`, `videos`, `files`, `it`, `science`, `social media`

## Output
One JSON object per line (NDJSON) with fields: `title`, `url`, `snippet`.

## Examples
```bash
# Basic search
searx "Rust async programming" -n 5

# News search
searx "AI regulation" -c news -n 3

# Science search
searx "quantum computing breakthrough" -c science
```

## When to use
- Any web search task
- Finding documentation, articles, or resources
- Lookups that need comprehensive results (not just Wikipedia)
