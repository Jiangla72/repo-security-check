# Repo Security Check

A reusable Codex skill for scanning repositories for likely secrets and optionally installing a local pre-commit hook that blocks unsafe commits.

## What this skill does

- scans a repository for likely secrets such as API keys, passwords, tokens, authorization headers, and private key markers
- supports full-repository scans and staged-only scans before commit
- can install a local `pre-commit` hook into a target git repository
- reports suspicious findings conservatively without pretending every match is a confirmed leak

## Repository layout

- `SKILL.md`: main skill instructions
- `scripts/security_check.py`: reusable scanner and hook installer
- `references/secret-scan-checklist.md`: follow-up checklist for suspicious repositories
- `agents/openai.yaml`: UI metadata for Codex skill discovery

For Chinese instructions, see [README_zh.md](./README_zh.md).

## Basic usage

Scan a repository:

```powershell
python ".\scripts\security_check.py" --repo "D:\path\to\repo"
```

Scan only staged files:

```powershell
python ".\scripts\security_check.py" --repo "D:\path\to\repo" --staged-only
```

Install a pre-commit hook into a target repository:

```powershell
python ".\scripts\security_check.py" --repo "D:\path\to\repo" --install-hook
```

## Notes

- pattern-based scanning catches many accidental leaks, but it is not a substitute for full security review
- local hooks only protect the repositories where they are installed
- if a real secret was already committed, rotate it and consider cleaning git history

