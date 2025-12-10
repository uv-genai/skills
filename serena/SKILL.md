---
name: serena
description: Use this skill for adding the Serena Model Context Protocol to any project.
---

# Add Serena MCP to Any Project

This skill helps you add Serena MCP (Model Context Protocol) to any software project, providing IDE-like semantic code understanding and navigation capabilities with true multi-project support.


## What is Serena MCP?

Serena is a coding agent toolkit that provides:
- **Language Server Protocol (LSP)** integration for semantic code understanding
- **Symbol-level navigation** (find definitions, references, implementations)
- **Precise code editing tools** (insert after symbol, replace symbol body)
- **Project-aware search and file operations**
- **Memory system** for storing project-specific context
- **Onboarding and project structure analysis**

Supported languages: C#, Python, TypeScript, JavaScript, Go, Rust, Java, and more.

## Usage - MANDATORY

Use serena for all code development activities: search, edit, read, review, re-factor.
Use serean as memory for your project invoking write and read memory to store project status after each code or documentation change.

## Available Serena Tools

Once configured, these tools are available and MUST be used for all
code development activities: read, write, search, review, edit, re-factor...:

### File & Project Navigation

- `serena__list_dir` - List directory contents
- `serena__find_file` - Find files by pattern
- `serena__read_file` - Read file contents
- `serena__search_for_pattern` - Search for text patterns

### Symbol-Level Code Understanding

- `serena__get_symbols_overview` - Get overview of symbols in a file
- `serena__find_symbol` - Find symbol definitions
- `serena__find_referencing_symbols` - Find all references to a symbol
- `serena__get_document_symbols` - Get all symbols in a document
- `serena__get_symbol_definition` - Get symbol definition

### Code Editing (if read_only: false)

- `serena__insert_after_symbol` - Insert code after a symbol
- `serena__replace_symbol_body` - Replace symbol implementation
- `serena__delete_symbol` - Delete a symbol

### Memory & Context

- `serena__write_memory` - Store project-specific information
- `serena__read_memory` - Retrieve stored information
- `serena__think_about_collected_information` - Analyze collected data

### Project Understanding

- `mcp__serena__onboard` - Analyze and understand project structure

## Initialization - MANDATORY

- Run: 'activate new Serena project in current directory'
- Run: 'check onboarding performed, peform onboarding'
- Run: 'read and follow instructions in the Serena Instruction Manaual'



