---
name: audit-verify
description: >
  This skill should be used when the user says "/audit-verify", "verifiera fixarna",
  "kolla att felen är lösta" or after fixing findings from a previous /audit run. It re-checks
  only the findings from the latest audit run and runs regression, with fresh independent agents.
metadata:
  version: "0.1.0"
---

# Audit Verify

Verify that the findings from the latest `/audit` run are actually resolved AND that the fixes did not break anything else. Do not re-run the whole audit; that is `/audit`.

Communicate with Henke in Swedish, short and direct.

## Process

1. Read `.audit/findings.json`. Take every finding with status OPEN, FIXED or REOPENED. If none, tell Henke there is nothing to verify.
2. Create a new run folder `.audit/runs/<timestamp>/` with `type: verify` and record the current git commit. If the commit is unchanged since the findings were marked fixed, warn that nothing was actually changed.
3. **Verify each finding with a fresh agent of the matching type** (code-reviewer, security-auditor, red-team, database-auditor, qa-engineer, ux-auditor, performance-auditor, production-auditor, compliance-auditor). Give the agent only the finding (reproduce steps + expected) and the environment — not the fix, not how it was fixed. It must reproduce the original problem and confirm it no longer happens.
   - Resolved and evidence proves it → status VERIFIED.
   - Still reproducible → status REOPENED, with new evidence.
4. **Regression:** run the full automated test suite and every critical flow in `.audit/krav.md` (browser + API) to catch anything the fixes broke. Any new problem is a new finding (fresh ID), not a reopen. Record the **Regression** area PASS/FAIL.
5. **Re-judge:** launch `release-judge` with the updated findings and evidence. It reissues the per-area table and the release decision.
6. Update `AUDIT.md` (first line `RELEASE STATUS: …`) and the Docs report. Append this run to the history section rather than deleting the previous one.
7. Tell Henke: which findings are now VERIFIED, which are REOPENED, any new regressions, and the new decision.

## Rules
- Fresh context per verification; never let the agent that (or the session that) applied the fix confirm its own fix.
- A finding flips to VERIFIED only on evidence, never on "should be fixed now".
- Keep looping `/audit` fixes → `/audit-verify` until the Release Judge sets APPROVED or Henke records an ACCEPTED_RISK.
