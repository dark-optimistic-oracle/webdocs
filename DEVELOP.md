# Development Notes

Last updated: 2026-07-27

## Responsibility

This repository is the public functional documentation site. It contains the
protocol purpose, lifecycle, privacy boundary, architecture, client
capabilities, and integration notes that would otherwise make the web app
README too large.

It intentionally excludes business plans, personal information, market-size
figures, and comparisons reserved for a future white paper.

## Implementation

- Static Vite documentation site.
- Cross-links to the website, web app, and prediction-market demonstration.
- GitHub Actions deployment from `main`; Jekyll is not used.
- Production base path: `/webdocs/`.
- Future custom-domain target: the `docs` subdomain.

The documented oracle ABI must remain synchronized with `core/src/main.leo`.
The current asserter settlement call is `collect_assertion_award`, not the
retired `collect_assertion_cost`.

## Local development and validation

```bash
pnpm install
pnpm run dev
pnpm run build
```

All real `.env*` files are ignored. Sanitized `*.example` templates may be
tracked if configuration is added later.
