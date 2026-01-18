# Antigravity Skills

A small, curated set of practical Agent Skills.

## Included skills
- `skills/github-repo-triage`: GitHub repo analysis + reliable download links (gh + gitingest + copilot-cli).
- `skills/copilot-cli`: GitHub Copilot CLI usage and troubleshooting.

## Install (Windows)
Copy the folders you need into your skills root, for example:
- Gemini: `C:\Users\11614\.gemini\skills`
- Codex: `C:\Users\11614\.codex\skills`

Or create Junctions to keep one source of truth (recommended):

```powershell
New-Item -ItemType Junction -Path C:\Users\11614\.codex\skills\github-repo-triage -Target C:\Users\11614\.gemini\skills\github-repo-triage
New-Item -ItemType Junction -Path C:\Users\11614\.codex\skills\copilot-cli -Target C:\Users\11614\.gemini\skills\copilot-cli
```