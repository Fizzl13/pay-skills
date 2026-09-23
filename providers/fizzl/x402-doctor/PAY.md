---
name: x402-doctor
title: "x402 Doctor"
description: "Diagnoses an x402 paid endpoint without paying it: 402 challenge format, accepts[] network/asset/amount/payTo, resource URL, Solana settlement readiness, Bazaar and OpenAPI discovery and browser paywall, each check with a fix hint."
use_case: "Use when an x402 or pay-per-call API returns errors, before paying an unknown x402 endpoint, when debugging a 402 challenge, validating a new paid route, or checking why agents or indexers like x402scan do not accept a service."
category: devtools
service_url: https://x402-doctor.onrender.com
version: v1
openapi:
  path: openapi.json
---

x402 Doctor probes an x402 endpoint the way a paying agent would, stops at
the 402 challenge, and reports every check as pass, warn or fail with a hint
to fix it. It never pays the endpoint it diagnoses, so it is safe to run
against any URL.

Checks cover the challenge envelope (x402 v1/v2, header and body), each
`accepts[]` option (network, USDC asset, atomic amount, payTo format,
EIP-712 domain), the declared resource URL, Solana settlement readiness
(payout token account, fee payer), Bazaar discovery metadata, OpenAPI
`x-payment-info` and the browser paywall.

One call costs $0.01 USDC on Solana mainnet (Base is also accepted). Invalid
input (not a URL, not http(s), unsupported method) is rejected with HTTP 400
before payment.

## Spend-aware usage

- Pass `method=POST` when the endpoint only accepts POST; without it the
  doctor tries GET and then POST.
- Diagnose once, fix, then re-run: results only change when the endpoint
  changes.
- `overall` summarises the report; read `checks[]` with `status` `fail` or
  `warn` first, their `hint` says what to change.
