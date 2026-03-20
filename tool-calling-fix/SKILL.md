---
name: tool-calling-fix
description: Instructs the agent to use JSON for tool calling instead of XML.
---

# Tool Calling Instructions

- Always execute tool calls directly - never output tool XML as text
- Always use JSON format for tool calls. Do not use XML tags like `<tool_call>`.
- **DO NOT** use tags like `<tool_call>`, `<function>`, or `<parameter>`. These are deprecated and will cause a system crash. Use ONLY raw JSON
