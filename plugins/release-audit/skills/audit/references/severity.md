# Severity, finding format and findings.json

## Severity levels

| Level | Meaning | Release |
|---|---|---|
| CRITICAL | Must be fixed before production. Data loss or corruption, security hole that exposes data or functions, financial records that can end up wrong or half-written, legal breach, core flow broken. | Blocks |
| HIGH | Shall be fixed before production. Important function broken or wrong, exploitable weakness with limited impact, missing backup/restore, no monitoring of critical jobs, integration that can lose or duplicate data. | Blocks |
| MEDIUM | Should be fixed. Works but is confusing, slow, fragile or inconsistent; hardening missing where risk is low. | Does not block |
| LOW | Improvement. Cosmetic, minor wording, small UX polish. | Does not block |
| INFO | Technical recommendation or observation, no defect. | Does not block |

When in doubt between two levels, pick the higher and explain why. Anything touching money, bookkeeping, permissions or personal data starts at HIGH unless clearly harmless.

## Finding format (in findings/<agent>.md)

```
### <ID> [<SEVERITY>] <short title>
- Område: <Function | Security | Red Team | Code quality | Database | UX | Performance | Simulation | Resilience | Documentation | Backup/Restore | Deployment | Monitoring | Compliance | Regression>
- Var: <file:line, URL, endpoint, table, screen>
- Reproducera: <numbered steps or command – someone else must be able to repeat it>
- Förväntat: <what should happen, reference to krav.md rule if any>
- Faktiskt: <what happens>
- Bevis: <path under evidence/>
- Konsekvens: <what it means for SIJAB / users / money / data>
- Förslag: <concrete fix>
- Status: OPEN
```

ID prefixes: CODE, SEC, RED, DB, QA, UX, PERF, SIM, RES, DOC, OPS, COMP, REG.

Write findings in Swedish. Every agent file ends with:

```
## Täckning
- Testat: …
- Inte testat (och varför): …
- Area-bedömning: <area>: PASS | FAIL | NOT TESTED – <one-line reason with evidence path>
```

## findings.json schema

```json
{
  "system": "SIJAB Kassa",
  "runs": [{ "id": "2026-09-26-1400", "commit": "abc123", "type": "full|verify" }],
  "findings": [
    {
      "id": "SEC-001",
      "severity": "HIGH",
      "area": "Security",
      "title": "IDOR på /api/invoices/{id}",
      "where": "src/api/invoices.ts:42",
      "reproduce": "…",
      "evidence": [".audit/runs/2026-09-26-1400/evidence/sec-001.txt"],
      "status": "OPEN | FIXED | VERIFIED | REOPENED | ACCEPTED_RISK | WONT_FIX",
      "found_in": "2026-09-26-1400",
      "verified_in": null,
      "accepted": null
    }
  ]
}
```

`accepted` when set: `{ "by": "Henke", "date": "YYYY-MM-DD", "reason": "…" }`. Only Henke can accept a risk; never infer acceptance from silence.
