# Stack: Node / TypeScript (Express, Next.js, React, Vite, etc.)

## Baseline commands
- `npm ci` (lockfile must exist and install cleanly)
- `npm run lint`, `npx tsc --noEmit`, `npm test`, `npm run build`
- `npm audit --omit=dev`; `npx depcheck` for unused/missing deps
- Bundle size: build output / `npx vite-bundle-visualizer` or Next build report
- E2E: Playwright (`npx playwright test`); use existing Chromium, never `playwright install`

## Specific checks
- Server-side validation for every endpoint (zod/joi/etc.), not only client-side
- Auth middleware on every private route, including API routes in Next.js and server actions
- No secrets in client bundle (`NEXT_PUBLIC_`, `VITE_` variables reviewed; grep built JS for keys)
- Unhandled promise rejections, missing `await`, error middleware returns no stack traces in production
- ORM: N+1 (Prisma `include` in loops), transactions (`$transaction`) around multi-write operations, migrations committed and reversible
- SSRF: any server-side fetch of user-supplied URLs
- Memory leaks: intervals/listeners not cleared, growing in-memory caches; run a soak test and watch RSS
- `helmet` or equivalent headers, CORS allowlist not `*` with credentials
