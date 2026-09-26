# Standard nasty inputs and situations

Use on every input field, API parameter, import file and flow. Not only happy path.

## Values
- Empty, only spaces, null/missing field, field removed from the request entirely
- 0, -1, -0.01, 0.005 (rounding), 1e9, 99999999999999999999, NaN, "abc" in number fields
- Decimal comma vs point: "1,5" and "1.5"; thousands separators "1 000"
- Very long text (10 000+ chars), a single 5 000-char word, line breaks, tabs
- Swedish characters åäöÅÄÖ, é, ü; emojis 👍🏽🇸🇪; RTL text; zero-width characters
- HTML/script: `<script>alert(1)</script>`, `"><img src=x onerror=alert(1)>`
- SQL-ish: `' OR '1'='1`, `'; DROP TABLE x;--`
- Path-ish: `../../etc/passwd`, `..\..\windows\win.ini`
- Dates: 29 Feb, 31 Dec → 1 Jan, sommartid switch (last Sunday March/October), far past/future, wrong format
- Duplicates: same org.nr, same e-mail with different case, same article number
- Swedish formats: org.nr/personnummer with and without dash, postnummer "831 30" vs "83130", phone +46/07

## Behaviour
- Double-click / double submit on every create/pay/send button
- Browser back after submit; reload mid-operation; two tabs editing the same record
- Session timeout / logged out mid-flow; expired token
- Network failure or slow network during save (throttle in browser devtools)
- Server error from an integration (simulate with invalid credentials in staging)
- Very large lists (pagination, sorting, filtering with 100 000 rows)
- Import of empty file, wrong columns, wrong encoding (ANSI vs UTF-8, BOM), 50 000 rows, Excel with formulas
- Export with åäö – opens correctly in Excel?
- Mail/PDF with åäö, long names, many rows (page breaks), missing logo
- Permissions: every action tried as every role, including logged out
