---
name: presign-guard
title: "presign-guard"
description: "Is it safe to sign or buy? Green/orange/red verdicts with reason codes for Solana, EVM, XRP Ledger and Algorand transactions, approvals and Permit/Permit2/EIP-3009/Seaport signatures, Solana and EVM token checks, and wallet approval audits."
use_case: "Use before your agent signs a Solana, EVM, XRPL or Algorand transaction, approval or EIP-712 signature, buys or accepts a token (honeypot, rug-pull signs, taxes, liquidity), or to find which token approvals on a wallet to revoke. Never sign on red."
category: security
service_url: https://presign-guard.onrender.com
version: v1
openapi:
  path: openapi.json
---

Pre-sign and pre-buy safety checks for AI agents, paid per call with x402. Each answer is a verdict (green, orange or red) with machine-readable reason codes, so an agent can act on it without a human reading it first.

- **`POST /v1/check`** ($0.01, Base or Solana): send the transaction, token approval or EIP-712 signature (Permit, Permit2, EIP-3009 x402 payment, Seaport) your agent is about to sign. Checks who gets access, unlimited allowances, flagged or OFAC-sanctioned spenders, plain-wallet spenders, unverified contracts and the token itself. A Solana transaction (`type: "solana"`, base64) is decoded instruction by instruction and simulated: a token account handed to someone else is red; an unlimited delegate, a durable nonce or a change the instructions don't show is orange. An XRP Ledger transaction (`type: "xrpl"`, the unsigned `tx` JSON): a new regular key or signer list, a disabled master key, AccountDelete or fake RLUSD is red. An Algorand transaction or group (`type: "algorand"`, base64 msgpack): a rekey, closing the account or an asset holding to someone else, or fake USDC is red; the group is simulated.
- **`POST /v1/check/explain`** ($0.03, Base or Solana): the same verdict plus a plain-language explanation in English or Dutch.
- **`GET /v1/token`** ($0.01, Base or Solana): is this token safe to buy, hold or accept? Solana and EVM tokens: mint or freeze authority, honeypot, buy/sell tax, LP lock, liquidity, age, holder concentration, plus market data.
- **`GET /v1/approvals`** ($0.02, Base or Solana): every open ERC-20 allowance of an EVM wallet, which spenders are risky and which to revoke, with a revoke.cash link.

Sources: GoPlus, on-chain RPC and PG1 (OFAC SDN list, domain age of the requesting site). An MCP server with a free quick verdict is at `https://presign-guard.onrender.com/mcp`.

## Spend-aware usage

- **Call once per signature, right before signing.** The verdict is for that exact payload; there is no need to re-check an unchanged request.
- **Use `/v1/check`, not `/v1/check/explain`, when the agent acts on the verdict itself.** The explanation is for showing a human and costs three times as much.
- **Act on the grade, not the prose:** proceed on green, ask the user on orange, never sign on red.
- **Check a token once, then reuse the answer** for repeated trades of the same token in the same session.
