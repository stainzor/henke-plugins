---
name: audit-setup
description: >
  This skill should be used when the user says "/audit-setup", "förbered projektet för audit",
  the first time /audit runs on a project, or when `.audit/config.md` or `.audit/krav.md` is missing.
  It sets up the audit for a project: config, acceptance criteria (krav), test accounts, an automated
  test suite scaffold and an optional GitHub Actions release gate.
metadata:
  version: "0.1.0"
---

# Audit Setup

Prepare a project so `/audit` and `/audit-quick` can run cleanly. Do this once per project, and re-run when the system changes materially. Ask Henke only about things that cannot be derived from the code, and only the unclear ones.

Communicate with Henke in Swedish, short and direct. GUI routes only, never terminal steps for him.

## Steps

1. **Detect the project:** stack, framework, package manager, where it runs (staging + production URLs), how it deploys, database, integrations, cron jobs, roles, system type(s). Read README/CLAUDE.md.

2. **Create `.audit/config.md`:** fill in everything detected —
   - System name and type(s): app / website / webshop / POS / statistics / ERP / plugin / API / integration
   - Environments: staging URL (the one auditors may attack), production URL (read-only checks only)
   - Test accounts per role (ask Henke for these — never his own password; ask him to create test accounts, describe the GUI route)
   - Integrations and how to run them in test/sandbox mode
   - Stack and the exact baseline commands (lint/typecheck/test/build/audit)
   - Anything auditors must NOT touch

3. **Create `.audit/krav.md` (acceptance criteria) — the definition of "correct":**
   - Derive rules from the code and docs: per flow, the inputs, expected result, and business rules (VAT, rounding, discounts, prices, stock, permissions, approval, dates/timezone).
   - List the critical flows that regression must always re-run.
   - Present to Henke **only the rules that are unclear or assumed**, as questions with a recommended answer. Everything he doesn't object to is treated as confirmed. Keep this short — he hates manual busywork.

4. **Staging check:** if there is no staging/test environment, explain why one is needed (attacks and destructive tests must not hit production) and propose the simplest way to get one for this stack. If impossible, note that those phases will be NOT TESTED.

5. **Automated test suite scaffold:** if the project has little or no automated testing, scaffold it so `/audit` can leave regression tests behind —
   - Unit tests for the calculation/business rules in `krav.md`
   - End-to-end tests (Playwright, using the session's Chromium) for the critical flows
   - A single command to run everything, documented in `.audit/config.md`

6. **CI release gate (if Henke chose GitHub Actions):** copy `references/ci/audit-quick.yml` into `.github/workflows/` (adjust commands to the stack) so lint/types/tests/dependency-audit run on every push — red/green visible in GitHub Desktop and the web. Optionally add `references/ci/release-gate.yml`, which fails a release/tag build unless `AUDIT.md` begins with `RELEASE STATUS: APPROVED`. Henke merges via GitHub Desktop or the web — give him that route, not git commands.

7. **`.gitignore`:** ensure `.audit/runs/` (evidence, screenshots, large data) is git-ignored, while `.audit/config.md`, `.audit/krav.md`, `.audit/findings.json` and `AUDIT.md` are committed.

8. Tell Henke what was set up, list the test accounts he needs to create, and confirm the unclear krav rules. Then he can run `/audit`.

## References
- `references/config-template.md` — `.audit/config.md` template
- `references/krav-template.md` — `.audit/krav.md` template
- `references/ci/audit-quick.yml` — continuous check workflow
- `references/ci/release-gate.yml` — release gate that reads AUDIT.md
