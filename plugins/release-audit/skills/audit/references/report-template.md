# Report template (AUDIT.md and Docs report)

The first line of AUDIT.md must be exactly one of these (CI release gate reads it):

```
RELEASE STATUS: APPROVED
RELEASE STATUS: BLOCKED
```

Then:

```
# Production Readiness Review – <system>

Datum: <YYYY-MM-DD HH:MM> · Commit: <sha> · Miljö: <staging-URL> · Körning: <run-id> (<full|verify>)

## Beslut
<APPROVED / BLOCKED> – <one sentence why>

## Områden
| Område | Status | Bevis |
|---|---|---|
| Funktion | PASS/FAIL/NOT TESTED | evidence/… |
| Security | | |
| Red Team | | |
| Code quality | | |
| Database | | |
| UX | | |
| Performance | | |
| Backup/Restore | | |
| Deployment | | |
| Monitoring | | |
| Compliance | | |
| Regression | | |

## Fynd
| Nivå | Öppna | Åtgärdade | Accepterad risk |
|---|---|---|---|
| CRITICAL | | | |
| HIGH | | | |
| MEDIUM | | | |
| LOW | | | |
| INFO | | | |

### CRITICAL – måste åtgärdas före produktion
<findings, full format>
### HIGH – ska åtgärdas före produktion
### MEDIUM – bör åtgärdas
### LOW – förbättring
### INFO – teknisk rekommendation

## Beslut som kräver Henke
1. <question> – Rekommendation: <answer>

## Accepterade risker
<ID, Henke's reason, date>

## Kvar för människor
<Safari/iPhone, hardware, user acceptance test, external pentest – who, what, how long>

## Täckning
<per area: tested / not tested and why>
```

Keep it hard: no "det verkar bra", no praise, no padding. Every PASS points to evidence.
