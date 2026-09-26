# .audit/krav.md template

Definition av "rätt". Utan detta kan QA inte sätta PASS.

```markdown
# Acceptanskriterier – <systemnamn>

## Kritiska flöden (regression kör alltid dessa)
1. <t.ex. Skapa order → lager minskar → faktura skapas → mail skickas>
2. ...

## Affärsregler
| Regel | Förväntat | Källa | Bekräftad av Henke |
|---|---|---|---|
| Moms | 25% på X, 12% på Y | | |
| Avrundning | öresavrundning enligt … | | |
| Rabatt | | | |
| Lagersaldo | får ej bli negativt | | |
| Fakturanummer | löpande utan luckor | | |
| Attest | order över X kr kräver godkännande | | |
| Behörighet | säljare får ej se inköpspriser | | |

## Per flöde
### <Flöde>
- Roller som får utföra: 
- Indata: 
- Förväntat resultat: 
- Får INTE kunna hända: 
```

Endast oklara/antagna regler tas upp med Henke, som frågor med rekommenderat svar.
