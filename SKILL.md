---
name: repo-security-check
description: Scan a git repository for likely secrets such as API keys, tokens, passwords, private keys, and authorization headers, and optionally install a pre-commit hook that blocks commits containing suspicious values. Use when a user asks for a security check before commit, wants to verify a repository does not contain secrets, or wants automatic local secret scanning for future commits.
---

# Repo Security Check

## Overview

Scan a repository for common secret patterns, report likely leaks with file and line references, and optionally install a local `pre-commit` hook so future commits are checked automatically.

## Quick Start

1. Use `scripts/security_check.py` for the actual scan.
2. Start with `--staged-only` when the user is about to commit.
3. Use a full-repository scan when the user wants a broader confidence check.
4. Offer `--install-hook` when the user wants the same check to run before every commit.
5. Read `references/secret-scan-checklist.md` only if the repo still looks risky after the automated scan.

## Workflow

### 1. Scan the target repository

Prefer:

```powershell
python "<skill-root>/scripts/security_check.py" --repo "<repo-path>"
```

If the user is preparing a commit, prefer:

```powershell
python "<skill-root>/scripts/security_check.py" --repo "<repo-path>" --staged-only
```

The script checks common text files for patterns such as:

- GitHub tokens and PATs
- AWS access keys
- private key headers
- `authorization:` headers
- inline password / secret / token assignments

### 2. Interpret the result carefully

If the scan passes, say that no obvious secrets were found, but keep the confidence proportional to coverage.

If the scan finds matches:

- list findings first with file references
- distinguish between real secrets and placeholder examples
- if a real secret appears committed or staged, tell the user to remove it and rotate it
- if the secret may already be in git history, recommend history cleanup as a follow-up

Do not claim the repository is fully safe just because the pattern scan passed.

### 3. Install the pre-commit hook when asked

To install a local hook into the target repository:

```powershell
python "<skill-root>/scripts/security_check.py" --repo "<repo-path>" --install-hook
```

The generated hook runs the same script with `--staged-only` before each commit and blocks the commit if suspicious content is detected.

### 4. Expand the review only when needed

If the repository still feels risky after the scan, read `references/secret-scan-checklist.md` and then inspect:

- suspicious config files
- logs, exports, or dumps
- binary artifacts or archives
- git history for old leaks

## Output Style

For a quick check, prefer:

- `Result`: `no obvious secrets found` or `possible secret leak`
- `Coverage`: `staged files` or `full repository`
- `Findings`: 0-3 high-signal items
- `Next step`: install hook, rotate secret, or inspect history

For deeper reviews, keep findings first and include concrete file references.

## Safety Notes

- Treat matches as suspicious until verified; some may be placeholders or documentation examples.
- Do not print full secret values back to the user when a match is found.
- Local hooks are repository-local; installing one here does not protect other repositories automatically.
