---
name: github-repo-triage
description: Use when you need to analyze a GitHub repository or produce reliable download links by combining gh (metadata/releases/download), gitingest (codebase digest), and copilot-cli (targeted Q&A and best-practice guidance).
---

# GitHub Repo Triage (gh + gitingest + Copilot)

## Purpose
One workflow to: (1) identify the exact repo, (2) get the correct download link (release asset/zip/source), and (3) analyze the codebase with the right tool for the question.

## Tool boundaries (when to use what)
- `gh` (GitHub CLI): repo metadata, default branch, README, issues/PRs, releases, and downloading release assets/source.
- `gitingest`: produce an LLM-friendly digest of the repo (tree + key files) for codebase understanding.
- `copilot-cli`: precise, narrow questions (e.g., "Windows download best practice") after you have the relevant context.

## JSON-first workflow (PowerShell)
Prefer JSON output from gh and parse it (avoid brittle text scraping).

Example: compute a default-branch source zip download URL:
```powershell
$r = 'owner/repo'
$info = gh repo view $r --json url,defaultBranchRef | ConvertFrom-Json
$zip = $info.url + '/archive/refs/heads/' + $info.defaultBranchRef.name + '.zip'
$zip
```

Example: list release asset download URLs:
```powershell
$r = 'owner/repo'
$rel = gh release view -R $r --json tagName,assets | ConvertFrom-Json
$rel.assets | Select-Object name,url,size
```

Tip: keep pipes JSON-safe (use ConvertFrom-Json rather than regex).

## Fast workflow
1) Resolve the repo
- If you have URL or `owner/repo`, confirm:
  - `gh repo view owner/repo --web` (or omit `--web` to print info)

2) Get download links (preferred order)
- Release assets (best for end-users):
  - `gh release view -R owner/repo --json tagName,assets`
  - `gh release download -R owner/repo --pattern "*.exe"`
- Source zip/tar (best for code):
  - `gh repo view -R owner/repo --json url,defaultBranchRef`
  - Source downloads live under `/archive/refs/heads/<branch>.zip` and `/archive/refs/tags/<tag>.zip`
- Clone (best for development):
  - `gh repo clone owner/repo`

3) Analyze code
- Quick structural understanding:
  - `gitingest E:\path\to\repo`
  - or `gitingest https://github.com/owner/repo`
- Reduce scope on huge repos:
  - `gitingest https://github.com/owner/repo -i "src/**" -e "node_modules/**"`

4) Ask precise questions with Copilot
- Use one-shot prompts for deterministic Q&A:
  - `npx @github/copilot -p "Explain the Windows download approach used in this repo" --add-dir .`
- Keep permissions minimal; add tools only when needed.

## Guardrails
- Private repos: authenticate `gh` and prefer env vars (no tokens in docs).
- Prefer `gitingest` before deep analysis; then use Copilot for pinpoint questions.

## Optional: deep changes via WSL Codex CLI
Use this when you want Codex to run in Ubuntu-22.04 (Linux tooling, pinned node/codex) while still working from Windows.

- If you installed `wsl-codex-cli`, run its `scripts/wsl-codex.ps1` from your skills root (path varies by tool).
- Common location (Windows): `%USERPROFILE%\.codex\skills\wsl-codex-cli\scripts\wsl-codex.ps1`

Example (multi-line prompt via stdin):
```powershell
$prompt = @'
Analyze this repo and suggest the safest Windows download approach (release assets vs source).
'@
$prompt | & "$env:USERPROFILE\.codex\skills\wsl-codex-cli\scripts\wsl-codex.ps1"
```

Guardrail: if you need the repo contents inside WSL, prefer cloning inside WSL or passing a small digest (e.g., from gitingest) instead of mounting huge trees.

## Related skills
- `copilot-cli`
- `wsl-codex-cli`
- `gitingest`
