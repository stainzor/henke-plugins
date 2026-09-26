---
name: audit-quick
description: >
  This skill should be used when the user says "/audit-quick", "snabb koll", "continuous audit"
  or wants a fast automated check during development (not a full release audit). Runs lint, tests,
  typecheck, dependency audit and a secret scan in a few minutes and reports red/green. Also run it
  automatically after making a batch of code changes, before handing back to the user.
metadata:
  version: "0.1.0"
---

# Audit Quick (continuous)

A fast, automated safety net for everyday development. No browser testing, no attack testing, no judge — that is `/audit`. Aim to finish in a few minutes and just report red/green with the specifics.

Run in the project (device shell for a connected folder, otherwise local shell). Communicate with Henke in Swedish.

## Steps (run all that apply to the stack; skip missing tools and note it)
1. **Git sanity:** current branch, uncommitted changes, that it builds from a clean checkout mentally noted.
2. **Lint / format:** the project's linter (eslint, phpcs, ruff, …).
3. **Typecheck:** tsc --noEmit, phpstan, mypy/pyright, as applicable.
4. **Tests:** the unit/integration suite. Report pass/fail counts and any failures.
5. **Build:** the production build succeeds (if the project has one).
6. **Dependency audit:** npm/composer/pip audit — list only High/Critical advisories.
7. **Secret scan:** grep the diff/working tree for obvious secrets (API keys, tokens, passwords, private keys, `.env` committed). Use patterns like `api[_-]?key`, `secret`, `password\s*=`, `BEGIN .*PRIVATE KEY`, provider key prefixes.

Determine the stack from the project (see the `audit` skill's `references/stack-*.md` for the right commands).

## Output
A short table:

```
| Kontroll | Status | Detalj |
|---|---|---|
| Lint | ✅/❌ | 0 fel |
| Typer | ✅/❌ | |
| Tester | ✅/❌ | 42/42 |
| Build | ✅/❌ | |
| Beroenden | ✅/⚠️ | 1 high |
| Hemligheter | ✅/❌ | inga |
```

Then one line: green → "Klart, inga blockerare. Kör /audit innan produktion." Red → list exactly what failed. Do not fix here unless Henke asks; this check just reports. This is not a release gate — only `/audit` decides production readiness.
