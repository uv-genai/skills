---
name: xml-tool-call
description: Recognize, parse, and execute tool calls embedded in XML-like markup sent by the user.
---

# XML Tool Call Parsing

## Format Specification

Tool calls are wrapped in a fence delimited by `<tool_call>` (open) and `</tool_call>` (close). Inside the fence is a `<function>` element whose tag name identifies the tool, and `<parameter>` child elements carry the arguments.

```
<tool_call>
  <function=TOOL_NAME>
    <parameter=PARAM_NAME_1>
      PARAM_VALUE_1
    </parameter>
    <parameter=PARAM_NAME_2>
      PARAM_VALUE_2
    </parameter>
    ...
  </function>
</tool_call>
```

### Parsing rules

1. **Identify the wrapper** — look for `<tool_call>` ... `</tool_call>`.
2. **Extract the tool name** — read the `TOOL_NAME` from `<function=TOOL_NAME>`.
3. **Extract parameters** — for each `<parameter=PARAM_NAME>` element, the inner text (trimmed) is the value.
4. **Execute** — map the extracted tool name and parameters to the actual tool call format.

---

## Mapping to Actual Tools

| XML Element | Actual Tool |
|-------------|-------------|
| `<function=bash>` | `bash` tool — requires `command` parameter, optional `timeout` |
| `<parameter=command>` | `command` arg for `bash` |
| `<parameter=timeout>` | `timeout` arg for `bash` (in seconds) |

---

## Examples

### Example 1 — `bash` with `command` and `timeout`

**XML received:**
```xml
<tool_call>
  <function=bash>
    <parameter=command>
      cd /Users/ugo/projects/agent-web-search && uv run linkup-search fetch "https://docs.python.org" --json 2>&1 | head -30
    </parameter>
    <parameter=timeout>
      30
    </parameter>
  </function>
</tool_call>
```

**How to call:**
- Tool: `bash`
- `command`: the full shell command string (trim whitespace)
- `timeout`: `30` (integer)

```json
{"command": "cd /Users/ugo/projects/agent-web-search && uv run linkup-search fetch \"https://docs.python.org\" --json 2>&1 | head -30", "timeout": 30}
```

### Example 2 — `bash` with `command` only (no timeout)

**XML received:**
```xml
<tool_call>
  <function=bash>
    <parameter=command>
      cd /Users/ugo/projects/agent-web-search && git add -A && git status --short
    </parameter>
  </function>
</tool_call>
```

**How to call:**
- Tool: `bash`
- `command`: the full shell command string (trim whitespace)
- `timeout`: omitted (use default)

```json
{"command": "cd /Users/ugo/projects/agent-web-search && git add -A && git status --short"}
```

---

## Important Notes

- Trim whitespace from parameter values — the XML may include leading newlines and indentation.
- Do NOT output the XML back to the user as plain text — parse it silently and execute the tool.
- If a parameter is optional (e.g. `timeout`) and absent, omit it from the tool call.
- If the tool call fails to parse, ask the user for clarification rather than guessing.
