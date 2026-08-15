# Dark Optimistic Oracle Documentation

Dark Optimistic Oracle is a privacy-preserving optimistic oracle on Aleo. It provides a verifiable lifecycle for assertions about off-chain events while using private records for disputed voting activity and voter settlement.

- Website: https://dark-optimistic-oracle.github.io/website/
- App: https://dark-optimistic-oracle.github.io/webapp/
- Documentation: https://dark-optimistic-oracle.github.io/webdocs/

## Purpose

An optimistic oracle lets an application publish a claim that is accepted unless somebody challenges it. This avoids requiring every assertion to be actively voted on. When a challenge does occur, Dark Optimistic Oracle uses economically staked voting and Aleo private records to resolve it.

Potential consumers include prediction markets, insurance, decentralized finance, supply-chain automation, gaming, and governance systems that need a verifiable outcome for information originating outside a blockchain.

## Protocol lifecycle

1. **Assert:** An asserter submits a claim identifier, public metadata, a content hash, economic terms, and deadlines.
2. **Dispute:** A disputer challenges the claim before its dispute deadline and posts the matching public DOOR amount.
3. **Vote:** A token holder spends a private DOOR record to create a voting right, then confirms or denies the assertion.
4. **Settle:** After the applicable deadline, eligible participants collect the public role settlement, a private voter award, or an unused-right refund.

The deployed Aleo program enforces the deadlines and settlement conditions.

## Privacy model

The design combines public protocol state with private Aleo records.

Public state includes:

- Assertion metadata and content hashes.
- Dispute and voting deadlines.
- Economic parameters.
- Asserter and disputer addresses.
- Aggregate confirm and deny totals.

Private records include:

- Voting rights.
- Vote receipts.
- Voter token inputs and change.
- Voter awards and unused-right refunds.

Voting-right purchase, voting, private voter-award, and unused-right-refund
requests use private fees. Private records and a private fee can hide record
ownership and the fee payer, but the selected `confirm` or `deny` transition and
the aggregate counters remain public. Integrators should derive their privacy
expectations from the deployed program and Aleo network semantics.

## Claim content

The web app hashes claim text locally and submits only the resulting field to the oracle program. The app does not upload, publish, or retain the original text.

Anyone who needs to verify the assertion later must have:

- The exact original claim text.
- The encoding and hashing procedure used by the client.
- The assertion ID needed to retrieve the on-chain record.

An integrating application can publish this material through its own content store or indexer.

## Architecture

```text
Browser
  ├─ Provable API → program availability, height, public mappings
  └─ Shield wallet → authorization and transaction submission
                         │
                         ▼
              dark_optimistic_oracle.aleo
                         │
                         ▼
                  token_registry.aleo
```

The browser prepares transaction requests and the wallet authorizes them. The oracle stores public state and invokes the canonical token registry for DOOR operations.

## Web app capabilities

The current interface:

- Retrieves public assertion state by known assertion ID.
- Creates assertions with locally hashed claim content.
- Submits disputes.
- Creates private voting rights.
- Submits confirm and deny transactions.
- Submits asserter, disputer, voter-award, and unused-right settlement transactions.
- Disables transaction controls when testnet or the oracle program cannot be verified.

Aleo mappings are key-value stores and do not expose a complete assertion index. Consumers must retain assertion IDs or provide an external index.

## Aleo testnet

The hosted app expects:

- `dark_optimistic_oracle.aleo`, deployed from the `core` repository.
- The canonical testnet `token_registry.aleo`.
- Shield connected to Aleo testnet.

The app reads the official Provable API to verify the program and obtain the current height. The `token-registry-workaround` repository is only needed for a local devnet.

As of the 2026-08-15 verification, `doo_prediction_market.aleo` is accepted at
edition 1 with existing market state preserved. The audited oracle edition-1
candidate is committed and tested, but the public oracle remains at edition 0:
its large upgrade transactions landed in Testnet blocks below the consensus
deployment-capacity threshold and were aborted without fees. Treat the hosted
oracle app as QA against the earlier contract until edition 1 is independently
confirmed.

Aleo's special upgrade-policy `constructor` cannot be changed, but the oracle's
application function named `initialize` is not that constructor. Function and
finalize logic may change when their public interfaces remain compatible. The
candidate keeps the constructor byte-for-byte unchanged and preserves the
initializer's types. A disposable Devnet edition-0 to edition-1 upgrade and
post-upgrade initialization confirmed that the live blocker is Testnet block
deployment capacity, not initializer immutability.

## Sample prediction market

The prediction-market demonstration uses the oracle as its truth layer. It is a
separate GitHub Pages site containing its explanation, interface, and bundled
contract source.

For each market `<x>`, the market registers two independent token-registry
assets, `YES<x>` and `NO<x>`. They are not DOOR. Public credits provide neutral
collateral; DOOR is used only for oracle bonds and voting. Settlement accepts a
post-close oracle assertion whose title and canonical claim hash bind it to the
market. The actual settlement assertion is recorded even when it differs from
the suggested ID stored at market creation.

After a valid outcome is settled, the losing outcome token has no redemption
path and is worth zero to this contract. Winning tokens burn for a proportional
share of the complete remaining collateral pool, so the winning supply assumes
the collateral value previously backing both outcomes.

- Demo: https://dark-optimistic-oracle.github.io/predmkt/
- Source and audit: https://github.com/dark-optimistic-oracle/predmkt

## Program calls

- `create_assertion`
- `dispute_assertion`
- `new_voting_right`
- `confirm`
- `deny`
- `collect_assertion_award`
- `collect_dispute_award`
- `collect_voting_award`
- `refund_voting_right`

## Audit evidence

The three implementation repositories maintain dated `AUDIT.md` chapters with
findings, dispositions, verification limits, and upgrade status. The webapp and
prediction-market repositories also maintain `LOG.md` with human-readable Aleo
read/transaction sequences, public parameters, accepted transaction IDs, and
failed or aborted attempts. Browser exports are client-generated diagnostic
evidence and must be checked against on-chain transactions and mappings.

## Current scope and roadmap

The current product surface covers the core assertion, dispute, private-record voting, and settlement lifecycle.

Governance, cross-chain bridges, foreign-chain contracts, and remote assertion consumers are future integration stages. They require separate design, security review, implementation, and deployment work and are not presented as current functionality.

## Local development

For the oracle program and local network, follow the `core` repository documentation. The local token-registry workaround is not part of the public testnet deployment.

Public contract builds and upgrades require Leo 4.4.1. Deployment wrappers
refuse older versions; set `LEO_BIN=/path/to/leo-4.4.1` when that binary is not
the default `leo` on `PATH`. Mainnet workflows remain locked and were not used.

For this documentation site:

```bash
pnpm install
pnpm run dev
```

## GitHub Pages

GitHub Actions builds and deploys this site from `main`. In the repository’s Pages settings, select **GitHub Actions** as the source.

The production build uses `/webdocs/` as its Vite base path. A future custom domain can be attached through GitHub Pages without changing documentation routes.
