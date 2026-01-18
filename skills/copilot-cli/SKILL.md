---
name: copilot-cli
description: Use when you need to run or troubleshoot GitHub Copilot CLI (npx @github/copilot), including sessions, permissions (--allow-tool/--deny-tool), MCP management (/mcp), or Copilot CLI agents.
---

# GitHub Copilot CLI Skill

## Goal
Use GitHub Copilot CLI (`@github/copilot`) safely and efficiently from the terminal (interactive sessions + one-shot prompts), with minimal-permission defaults.

## Quick start
- **Interactive (recommended for back-and-forth)**: `npx @github/copilot`
- **One-shot prompt**: `npx @github/copilot -p "<your task>" --add-dir .`
- **Resume**: `npx @github/copilot --continue`

## Decide the mode
- Use **interactive** when you expect multiple turns, need `/mcp` management, or want to iteratively grant tools.
- Use **`-p` one-shot** when automating, piping output, or you want deterministic “ask → answer”.

## Safety & permissions (default to least privilege)
- Prefer **scoping context** with `--add-dir <path>` over broad access.
- Avoid `--allow-all-paths` unless you fully trust the directory tree.
- Prefer enabling only what you need:
  - `--allow-tool 'write'` for edits
  - `--allow-tool 'shell(git:*)'` for git inspection
  - Combine with `--deny-tool` for dangerous subsets (example):
    - `npx @github/copilot -p "Generate a commit message" --allow-tool 'shell(git:*)' --deny-tool 'shell(git push)'`

## MCP & agents
- In interactive mode, use `/mcp show|add|edit|delete|enable|disable` to manage MCP servers.
- Use `--agent <name>` for domain workflows (example):
  - `npx @github/copilot --agent gitingest -p "Analyze repo structure" --add-dir E:\web\some-repo`

## Config, logs, and common fixes
- **Config**: `C:\Users\<username>\.copilot\config.json`
- **Logs**: `C:\Users\<username>\.copilot\logs\`
- **Encoding** (Windows): if output is garbled, set:
  - `[Console]::OutputEncoding = [System.Text.Encoding]::UTF8`

## Reference manual (load only when needed)
For the full command list, permission matrix, MCP examples, aliases, and troubleshooting, see:
- `references\copilot.md`

Tip: to find specifics quickly, use ripgrep:
- `rg -n "trusted_folders|/mcp|allow-tool|deny-tool|--resume|--continue" references\\copilot.md`

## When to use WSL Codex CLI instead
- Use Copilot CLI for tight, scoped Q&A and quick best-practice checks.
- Use WSL Codex CLI when you need Linux-native tooling or long multi-step refactors in Ubuntu.
- Entry: `C:\Users\11614\.codex\skills\wsl-codex-cli\scripts\wsl-codex.ps1`
- Reference: `C:\Users\11614\.codex\skills\wsl-codex-cli\SKILL.md`
