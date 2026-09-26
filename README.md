# Henkes Claude-plugins

## release-audit – oberoende granskning före produktion

### Installera (en gång per server/dator)

Skriv i Claude Code:

```
/plugin marketplace add stainzor/henke-plugins
/plugin install release-audit@henke-plugins
```

Starta om Claude Code. Klart – fungerar sedan i alla projekt.

### Uppdatera till senaste versionen

Be Claude Code köra:

```
claude plugin marketplace update henke-plugins
claude plugin update release-audit@henke-plugins
```

Starta sedan en ny session.

### Använda

Skriv `/audit` eller "nu är appen klar, granska den".
