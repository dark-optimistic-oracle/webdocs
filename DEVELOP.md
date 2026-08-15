# Development Notes

Last updated: 2026-08-15

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

The 2026-08-15 update documents the exact private-fee boundary, the independent
YES/NO outcome-token semantics, actual-assertion settlement binding, dated audit
and call-log evidence, Leo 4.4.1 deployment requirement, and the verified
Testnet status: both the prediction market and oracle are accepted at edition
1 with prior state preserved. It contains no private credentials,
personal data, business projections, market-size figures, or competitor
comparisons.

The 2026-08-15 upgrade-rule follow-up also documents that Aleo's immutable
special `constructor` is distinct from the ordinary oracle function named
`initialize`. The candidate preserves the constructor and initializer
interfaces; a disposable Devnet upgrade confirmed the logic change is valid.
The public oracle upgrade was subsequently accepted in block `18745064` and
transaction
`at1900gz2klm9we2deqarpv2fpqhnjqjr3cvr43stxq4525l6s9zupq6r0v5p`.

## Local development and validation

```bash
pnpm install
pnpm run dev
pnpm run build
```

All real `.env*` files are ignored. Sanitized `*.example` templates may be
tracked if configuration is added later.
