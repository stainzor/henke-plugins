# Release Audit

Ett oberoende granskningssystem som säkerställer att inget klassas som "Production Ready" bara för att bygget säger att det är klart. Systemet måste först klara flera separata granskningar som aktivt försöker hitta fel – på allt du bygger: appar, hemsidor, kassasystem, statistik, ERP, plugins, API:er och integrationer.

## Så använder du det

Ett enda kommando: **`/audit`** – eller skriv bara "nu är appen klar, granska den".

Claude väljer själv vad som behövs:
- Första gången på ett projekt → sätter upp allt och kör sedan full granskning
- Finns öppna fynd som åtgärdats → verifierar bara dem och kör regression
- Annars → full release audit

Den snabba löpande kontrollen (lint, tester, beroenden) kör Claude automatiskt medan den bygger. De andra kommandona (`/audit-setup`, `/audit-verify`, `/audit-quick`) finns kvar internt men behöver aldrig skrivas.

## Granskarna

Tio oberoende agenter, var och en med egen kontext och utan kännedom om hur systemet byggdes:

- **Code Reviewer** – kod och arkitektur
- **Security Auditor** – defensiv säkerhetsgranskning (OWASP Top 10)
- **Red Team** – försöker aktivt kringgå behörigheter på din egen testmiljö
- **Database Auditor** – dataintegritet, transaktioner, återställning
- **QA Engineer** – provar varje funktion i webbläsaren, inte bara happy path
- **UX Auditor** – gränssnitt, användbarhet, responsivitet, tillgänglighet
- **Performance Auditor** – prestanda under realistisk datamängd
- **Production Auditor** – backup (testar faktisk återställning), drift, deployment, övervakning
- **Compliance Auditor** – bokföringslagen, GDPR, kassaregisterlagen, tillgänglighetslagen
- **Release Judge** – ser bara fynd och bevis, sätter APPROVED/BLOCKED

## Resultat

Varje körning ger en rapport (`AUDIT.md` i projektet + ett Claude Docs-dokument) med:

- Beslut: APPROVED eller BLOCKED
- En hård tabell: PASS/FAIL/NOT TESTED per område, varje PASS med bevis
- Fynd klassade CRITICAL / HIGH / MEDIUM / LOW / INFO
- Bara de beslut som kräver dig, som frågor med rekommendation

`AUDIT.md` börjar med `RELEASE STATUS: APPROVED` eller `RELEASE STATUS: BLOCKED`, vilket GitHub-spärren kan läsa för att stoppa en release som inte är godkänd.

## Regler

- Ingen session som byggt en funktion får godkänna sin egen kod – granskarna körs i egen kontext.
- Varje PASS kräver bevis (testkörning, skärmbild, loggutdrag). "Ser bra ut" räknas inte.
- Attacker och destruktiva tester körs bara mot staging/test, aldrig mot produktion.
- Inget skarpt (riktiga betalningar, kundutskick, ekonomiändringar) utan ditt uttryckliga ja.
- Öppna CRITICAL eller HIGH → BLOCKED tills de är åtgärdade eller du uttryckligen accepterat risken.

## Kräver

- För webbläsartester: din dator på och nåbar, eller en webbläsare i sessionen.
- För full nytta: en staging-/testmiljö (annars markeras attack- och destruktiva tester NOT TESTED).
- Compliance-delen är riskflaggning, inte juridisk rådgivning.
