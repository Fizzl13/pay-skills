---
name: plaintext
title: "PlainText"
description: "Wallet approval risk checker: looks up an EVM wallet's live token and NFT approvals via GoPlus and returns a SAFE, CAUTION or RISK verdict with a plain-language explanation; also explains pasted approval payloads."
use_case: "Use for wallet security checks, token allowance and NFT setApprovalForAll review, revoke decisions, explaining a dapp approval or permit before signing, and drainer risk screening on Ethereum, Base, Arbitrum, Polygon, BSC and more."
category: security
service_url: https://smartcontractexplainer.onrender.com
version: v1
openapi:
  path: openapi.json
---

PlainText turns wallet permissions into plain language for people and
agents. `POST /api/check-wallet` looks up the live token (or NFT) approvals
of an EVM address through the GoPlus Security API and explains which
contracts can move the wallet's assets and how risky that is.
`POST /api/explain` does the same for an approval or permission payload you
already have (a dapp prompt, a permit, a scanner result), without an
on-chain lookup.

Both return `{ verdict: SAFE | CAUTION | RISK, explanation }`, so an agent
can branch on `verdict` and show `explanation` to the user.

Payment is USDC on Solana mainnet (Base is also accepted): $0.10 for a
wallet check, $0.05 for an explanation. A request that errors is not
settled.

## Identifier formats

- `address` is an EVM address (`0x` + 40 hex characters).
- `chain` is one of `ethereum`, `bsc`, `polygon`, `arbitrum`, `optimism`,
  `base`, `avalanche` (default `ethereum`); `kind` is `token` or `nft`
  (default `token`).

## Spend-aware usage

- Use `/api/explain` ($0.05) when you already have the approval payload;
  use `/api/check-wallet` ($0.10) only when you need the live on-chain list.
- Check the chain the user actually uses instead of looping over every
  chain; ask for `nft` only when the question is about NFTs.
- Keep `/api/explain` payloads small (under 5,000 characters): send the
  approval fields, not a whole transaction dump.
