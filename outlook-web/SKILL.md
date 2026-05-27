---
name: outlook-web
description: |
  Navigate and extract data from Outlook Web (https://outlook.cloud.microsoft/mail) via Kimi WebBridge.
  Use this skill when the user asks to read emails, search messages, extract subjects/senders/dates,
  or interact with their Outlook inbox. Works with the user's live login session.
---

# Outlook Web Automation

This skill assumes Kimi WebBridge is running (`~/.kimi-webbridge/bin/kimi-webbridge status` → `running:true`, `extension_connected:true`).
All commands below use session `"outlook"` — adjust if working across multiple sites.

## 1. Open Outlook

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"navigate","args":{"url":"https://outlook.cloud.microsoft/mail/","newTab":true},"session":"outlook"}'
```

Wait **3 seconds** for the SPA to hydrate before interacting.

## 2. Verify the page loaded — use screenshots, not just snapshot

The accessibility `snapshot` is detailed for the chrome (ribbon, folder pane) but the **message list may appear empty** even when emails are visually present. Always confirm state with a screenshot:

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"screenshot","args":{"path":"/tmp/outlook-state.png"},"session":"outlook"}'
```

Then `Read` the returned `.path` to inspect visually.

## 3. Message list anatomy (what you see in screenshots)

- **Focused / Other tabs** at top of message list. Toggle via click.
- **Pinned** section — pinned emails always visible at top.
- **Date-grouped sections** — "Today", "Yesterday", "This week", etc.
- Each row shows: avatar, sender name, subject, time/date, attachment/flag icons.
- Sort control defaults to "By Date" (grouped) or can be "Newest on top" (flat).

## 4. Searching for emails by sender/subject

### Option A: Use the search box + evaluate JS (most reliable)

```bash
# Focus search box, type query, press Enter
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"evaluate","args":{"code":"(()=>{const i=document.querySelector(\"[role=combobox]\")||document.querySelector(\"input[type=text]\");if(i){i.focus();i.value=\"from:Pascal\";i.dispatchEvent(new Event(\"input\",{bubbles:true}));i.dispatchEvent(new KeyboardEvent(\"keydown\",{key:\"Enter\",bubbles:true}));return \"searched\";}return \"no input\";})()"},"session":"outlook"}'
```

Wait 2–3 seconds, then screenshot to see results.

### Option B: Use `fill` + `evaluate` Enter dispatch

```bash
# Fill the search box
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"fill","args":{"selector":"@e3","value":"from:Pascal"},"session":"outlook"}'

# Dispatch Enter on active element
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"evaluate","args":{"code":"document.activeElement.dispatchEvent(new KeyboardEvent(\"keydown\",{key:\"Enter\",bubbles:true}))"},"session":"outlook"}'
```

> **Tip:** Search suggestions appear as a dropdown *before* pressing Enter. These suggestions sometimes show email subjects, senders, and dates — useful for quick reconnaissance without executing the full search.

## 5. Clicking an email to open it

The `@e` refs from snapshot on message list rows are unreliable. Use **coordinate clicks via evaluate** or **click on visible text**:

### Coordinate click (fastest)

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"evaluate","args":{"code":"document.elementFromPoint(500,420)?.click()"},"session":"outlook"}'
```

Adjust x,y based on screenshot. The message list is roughly x=350–750, y=300–900.

### Click by visible text

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"evaluate","args":{"code":"(()=>{for(const s of document.querySelectorAll(\"span\")){if(s.textContent.includes(\"Formalisation of DSTG\")){const r=s.closest(\"[role=option]\")||s.closest(\"div\")?.parentElement?.parentElement;if(r){r.click();return \"clicked\";}}}return \"not found\";})()"},"session":"outlook"}'
```

After clicking, wait 1–2 seconds and screenshot to confirm the reading pane populated.

## 6. Extracting email content from the reading pane

Once an email is selected, the reading pane (right side) contains the full message. Use `snapshot` for structured text, or `evaluate` to extract specific fields:

### Structured extraction via evaluate

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"evaluate","args":{"code":"(()=>{const sender=document.querySelector(\"[data-testid=senderName]\")?.textContent||\"\";const subject=document.querySelector(\"[data-testid=subjectLine]\")?.textContent||\"\";const date=document.querySelector(\"[data-testid=dateColumn]\")?.textContent||\"\";const body=document.querySelector(\"[role=main]\")?.innerText?.substring(0,2000)||\"\";return JSON.stringify({sender,subject,date,body});})()"},"session":"outlook"}'
```

> Note: Outlook uses dynamic class hashes, so `data-testid` attributes are more stable than class-based selectors.

### Fallback: snapshot the reading pane

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"snapshot","session":"outlook"}'
```

Look for `role="main"` (Reading Pane) in the tree.

## 7. Downloading email attachments

Once an email is open in the reading pane, use `snapshot` to find the "Download all" button. It appears as a `role="button"` with `name="Download all"` in the attachment section of the reading pane.

### Step 1: Snapshot to find the button

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"snapshot","session":"outlook"}'
```

Look for the attachment section — it appears near the bottom of the reading pane tree, after `role="main"`. You'll see:
- Individual attachments listed as `role="option"` with filename and size
- `role="button" name="Save all to OneDrive"`
- `role="button" name="Download all"` — this is the one to click

### Step 2: Click "Download all"

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"click","args":{"selector":"@e176"},"session":"outlook"}'
```

Replace `@e176` with the actual `@e` ref from your snapshot — the ref number varies per session.

### Result

The browser bundles all attachments into a single `.zip` file and downloads it to the user's default download location (typically `~/Downloads` on macOS). The filename is derived from the email subject (e.g., `UWA CI Wang-Pawsey- ARC ITTC FLiQC Project Agreement.zip`).

### Downloading a single attachment

To download only one attachment, click the `@e` ref for that specific `role="option"` in the attachment list, or use its "More actions" button (`role="button" name="More actions"`) for a context menu with a download option.

## 8. Scrolling the message list

Standard `window.scrollTo` does **not** work — the message list is an internal scrollable div. Use wheel events:

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -H 'Content-Type: application/json' \
  -d '{"action":"evaluate","args":{"code":"(()=>{const el=document.elementFromPoint(500,600);if(el){el.dispatchEvent(new WheelEvent(\"wheel\",{deltaY:800,bubbles:true}));return \"scrolled\";}return \"none\";})()"},"session":"outlook"}'
```

Then screenshot again to see newly loaded items.

## 9. Common keyboard shortcuts via evaluate

| Action | Code |
|--------|------|
| Close modal/menu | `document.dispatchEvent(new KeyboardEvent('keydown',{key:'Escape',bubbles:true}))` |
| Press Enter | `document.activeElement.dispatchEvent(new KeyboardEvent('keydown',{key:'Enter',bubbles:true}))` |

## 10. Known quirks & workarounds

| Problem | Cause | Fix |
|---------|-------|-----|
| `snapshot` shows empty message list | Virtualized list not yet rendered | Wait 3s, or use screenshot |
| `@e` ref click does nothing on email row | Ref stale or row not interactive | Use coordinate click or text-based JS click |
| Search returns no results | Need to press Enter to execute | Use evaluate to dispatch Enter after fill |
| Sort dropdown stuck open | Context menu from right-click | Dispatch Escape key |
| Reading pane says "Nothing is selected" | Email row click didn't register | Re-click, possibly with different coordinates |

## 11. Full workflow example: find last N emails from a sender

```bash
# 1. Health check
~/.kimi-webbridge/bin/kimi-webbridge status

# 2. Open Outlook
curl -s -X POST http://127.0.0.1:10086/command \
  -d '{"action":"navigate","args":{"url":"https://outlook.cloud.microsoft/mail/","newTab":true},"session":"outlook"}'

sleep 3

# 3. Search for sender
curl -s -X POST http://127.0.0.1:10086/command \
  -d '{"action":"evaluate","args":{"code":"(()=>{const i=document.querySelector(\"[role=combobox]\");i.focus();i.value=\"from:Sender Name\";i.dispatchEvent(new Event(\"input\",{bubbles:true}));i.dispatchEvent(new KeyboardEvent(\"keydown\",{key:\"Enter\",bubbles:true}));return \"ok\";})()"},"session":"outlook"}'

sleep 2

# 4. Screenshot to see results
curl -s -X POST http://127.0.0.1:10086/command \
  -d '{"action":"screenshot","args":{"path":"/tmp/outlook-results.png"},"session":"outlook"}'

# 5. Read screenshot with Read tool
# → Inspect to identify email rows

# 6. Click first result
curl -s -X POST http://127.0.0.1:10086/command \
  -d '{"action":"evaluate","args":{"code":"document.elementFromPoint(500,420)?.click()"},"session":"outlook"}'

sleep 1

# 7. Extract content
curl -s -X POST http://127.0.0.1:10086/command \
  -d '{"action":"evaluate","args":{"code":"(()=>{const m=document.querySelector(\"[role=main]\");return m?m.innerText.substring(0,2000):\"no main\";})()"},"session":"outlook"}'
```

## 12. Closing the session

```bash
curl -s -X POST http://127.0.0.1:10086/command \
  -d '{"action":"close_session","session":"outlook"}'
```
