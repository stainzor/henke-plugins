---
name: audit
description: >
  This skill should be used when the user says "/audit", "kör audit", "kör full production audit",
  "nu är appen färdig, granska den", "är den klar för produktion?", "release audit" or
  "production readiness", for any kind of system (app, website, webshop, POS, statistics, ERP,
  WordPress plugin, API, integration, automation). Also run it automatically as the final step
  whenever Claude has finished building an app or a larger feature, before calling anything done.
metadata:
  version: "0.1.0"
---

# Full Release Audit

Principle: nothing is "Production Ready" because the builder says the implementation is done. The system must pass separate, independent reviews that actively try to break it. Only the Release Judge can set APPROVED, and only on evidence.

Communicate with Henke in Swedish, short and direct. Never give him terminal steps; if he must do something manually, describe the GUI route (web, GitHub Desktop, WP-admin). He should never need to test anything himself.

## Ground rules

1. **Independence.** Every review phase runs in a separate subagent with fresh context. Give each agent only: project path, `.audit/config.md`, `.audit/krav.md`, its checklist, the run folder and target URLs. Never pass the build conversation, the builder's explanations or "how it's meant to work". Use the plugin agent of the same name when it is available as a subagent type; otherwise spawn a general-purpose agent whose prompt is the full content of the agent file `<name>.md` (in the plugin's `agents/` folder, or `~/.claude/agents/` when installed without the plugin system) plus the inputs above.
2. **Auditors never modify application code.** They may create test data, test scripts and evidence files inside `.audit/`.
3. **Evidence or it didn't happen.** Every PASS needs evidence: command output, test report, screenshot, log excerpt, query result. "Looks fine" is not evidence. No evidence → NOT TESTED.
4. **Test environment.** Destructive, attack and load tests run only against staging/test. Against production only read-only checks (SSL, headers, DNS, backup status, uptime). If no staging exists: stop and propose running `/audit-setup` to create one, or run the non-destructive parts and mark the rest NOT TESTED.
5. **Nothing live without an explicit yes:** no real payments, no mail to real customers, no writes to real accounting systems (Fortnox etc.), no deletion of real data.
6. **Hold the thread.** If Henke raises something else mid-audit, park it in a list and return to the audit.

## Single entry point – decide the mode automatically

Henke only ever says `/audit` or "granska den". Never ask him which mode; pick it:

1. No `.audit/config.md` or `.audit/krav.md` → run the `audit-setup` flow first, then continue to a full audit in the same go.
2. `.audit/findings.json` has findings with status OPEN/FIXED/REOPENED and code has changed since the last run → run the `audit-verify` flow.
3. Otherwise → full audit (below).

`audit-quick` is never something Henke runs: run it automatically yourself after each batch of code changes while building, and fix red results before handing back.

## Process

### Phase 0 – Prerequisites
1. Locate the code (connected folder, repo, server) and decide where to run commands: on Henke's computer via the device shell when the code is in a connected folder, otherwise in the local shell.
2. If `.audit/config.md` or `.audit/krav.md` is missing, run the `/audit-setup` flow first (it asks Henke only about unclear rules).
3. Create run folder `.audit/runs/<YYYY-MM-DD-HHMM>/` with `evidence/` and `findings/`.
4. Record git state: branch, commit SHA, uncommitted changes (uncommitted changes → note it; audit what is deployed to staging and flag the mismatch).
5. Create a task list with the phases below.

### Phase 1 – Map and baseline (orchestrator)
1. Map the project: stack, entry points, routes/pages, roles, integrations, cron jobs, data stores, system type(s). Update `.audit/config.md` if something is new.
2. Read the stack reference that applies: `references/stack-wordpress.md`, `references/stack-node.md`, `references/stack-python.md`, `references/stack-docker.md`.
3. Run the baseline: existing tests, lint, typecheck, dependency audit, secret scan (same as `/audit-quick`). Save output to `evidence/baseline.txt`. A red baseline is itself a finding.

### Phase 2 – Independent reviews (parallel where possible)
Launch in parallel, each with its own fresh context:

| Agent | Area(s) in the final table |
|---|---|
| `code-reviewer` | Code quality |
| `security-auditor` | Security |
| `database-auditor` | Database |
| `performance-auditor` | Performance |
| `production-auditor` | Backup/Restore, Deployment, Monitoring |
| `compliance-auditor` | Compliance |

Then, since they share the browser and test data, run sequentially:

| Agent | Area |
|---|---|
| `qa-engineer` | Function |
| `ux-auditor` | UX |
| `scenario-simulator` | Simulation |
| `resilience-auditor` | Resilience |
| `docs-auditor` | Documentation |
| `red-team` | Red Team |

`scenario-simulator` first builds a usage model of how THIS app is really used (who, where, device, connection, rhythm) and only simulates scenarios relevant to it; `resilience-auditor` reuses that model for outage and offline scenarios. Red Team runs last and gets nothing from the security auditor – different results are the point.

Each agent writes `findings/<agent>.md` using the format in `references/severity.md`, with evidence files in `evidence/`, and a coverage section (what was tested, what was not and why).

### Phase 3 – Regression
If this run follows fixes from an earlier run, or code changed during the audit: rerun all critical flows from `.audit/krav.md` (automated suite + browser) and record the result as area **Regression**. On a first audit, Regression = the automated suite and the critical flows run green after all fixes in this run.

### Phase 4 – Judgment
1. Merge all findings into `.audit/findings.json` (schema in `references/severity.md`) with stable IDs (`SEC-001`, `QA-004` …). Deduplicate; keep the highest severity.
2. Launch `release-judge` with only the findings, evidence and coverage – not the code, not the build history. It sets PASS / FAIL / NOT TESTED per area and the release decision.
3. Write the report:
   - `AUDIT.md` in the project root (template in `references/report-template.md`). The first line must be exactly `RELEASE STATUS: APPROVED` or `RELEASE STATUS: BLOCKED` – the release gate in CI reads it.
   - The same report as a Claude Docs document titled `Audit – <system> – <date>` when Docs is available, so Henke can read it anywhere. Fall back to only `AUDIT.md` + sending it in chat.
4. Tell Henke in 3–6 lines: decision, area table result, number of findings per severity, and a numbered list of only the decisions that need him (each as a question with a recommended answer).

### Phase 5 – Fix and verify
1. Fix in the main session, without asking: all clear bugs and security holes. If user manuals, in-app help or the operations handbook are missing or outdated, write/update them (in `docs/` in the project, task-based, per role, in Swedish) as part of the fix, and link them from inside the app. Ask Henke only about things that change behaviour, business rules, design or cost money.
2. Add or update an automated test for every fixed CRITICAL/HIGH so it cannot come back.
3. Run `/audit-verify` (fresh agents). Repeat fix → verify until the Release Judge sets APPROVED or Henke explicitly accepts a remaining risk (recorded as ACCEPTED RISK with his reason and date).

## Release rule
- Any open CRITICAL or HIGH → **BLOCKED**.
- Any area FAIL or NOT TESTED → **BLOCKED**, unless Henke has accepted it as ACCEPTED RISK with a reason.
- Never write APPROVED yourself; only the Release Judge's verdict is copied into `AUDIT.md`.

## What always remains for humans
List in the report, with a concrete proposal (who, what, how long): Safari/real iPhone testing, physical hardware (receipt printer, card terminal, scanner), real user acceptance test with the staff who will use it, and an external penetration test when the system handles payments or highly sensitive data.

## Additional resources
- `references/severity.md` – severity levels, finding format, findings.json schema
- `references/report-template.md` – AUDIT.md / Docs report template
- `references/edge-cases.md` – the standard nasty-input list all testers use
- `references/stack-*.md` – stack-specific checks and commands
