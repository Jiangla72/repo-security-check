# Secret Scan Checklist

Use this checklist when the first scan reports no findings but the repository still feels risky.

## Look for

- `.env`, `.npmrc`, `.pypirc`, credential JSON, or ad-hoc config files
- copied terminal output that may contain bearer tokens or cookies
- sample configs that were filled with real credentials instead of placeholders
- generated exports, logs, or debug dumps that may contain auth headers
- binary files or archives that the simple text scan cannot inspect safely

## Follow-up steps

1. scan staged files first if the user is preparing a commit
2. scan the full repository if the risk may come from older files
3. inspect git history when a secret might have been committed previously
4. if you find a real secret, tell the user to rotate it and consider rewriting git history
