# .audit/config.md template

```markdown
# Audit-konfiguration – <systemnamn>

## Systemtyp
<app / hemsida / webshop / kassa / statistik / ERP / plugin / API / integration> (kan vara flera)

## Miljöer
- Staging (får angripas och testas destruktivt): <URL>
- Produktion (endast läsande kontroller): <URL>
- Hur staging speglar produktion: <databas, data, skillnader>

## Testkonton (per roll – aldrig Henkes eget lösenord)
| Roll | Användarnamn | Var det skapas |
|---|---|---|
| Admin | | |
| Säljare | | |
| Kassa | | |
| Vanlig användare | | |

## Stack och baseline-kommandon
- Språk/ramverk: 
- Pakethanterare: 
- Lint: 
- Typecheck: 
- Tester: 
- Build: 
- Dependency audit: 
- Kör hela testsviten: 

## Integrationer
| Integration | Testläge/sandbox | Hur den sätts i testläge |
|---|---|---|
| Fortnox | | |
| WooCommerce | | |
| E-post | | |
| Betalning | | |

## Cron / schemalagda jobb
<lista>

## Rör inte
<data, konton, endpoints som auditörer inte får röra>
```
