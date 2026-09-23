---
name: x402-doctor
title: "x402 Doctor"
description: "Checks x402 paid endpoints without paying them: a $0.001 pre-payment verdict for buyers (go/caution/no_go, recommended option, reasons) and a $0.01 full diagnosis of challenge, accepts[], settlement and discovery with fix hints."
use_case: "Use before paying an unknown x402 or pay-per-call API (is it safe, will the payment settle, is it within budget), when an x402 API returns errors, when debugging a 402 challenge, or when validating a new paid route for agents and x402scan."
category: devtools
service_url: https://x402-doctor.onrender.com
version: v1
openapi:
  path: openapi.json
---

x402 Doctor probes an x402 endpoint the way a paying agent would and stops at
the 402 challenge. It never pays the endpoint it checks, so it is safe to run
against any URL. Two paid routes:

- `GET /api/v1/preflight` ($0.001): for buyers, before paying an endpoint
  they have not used before. Returns `verdict` (`go`, `caution`, `no_go`),
  `safe_to_pay`, the `recommended_option` (cheapest payable USDC option, on
  `network` if given) and `reasons`: the payment would not settle (bad
  payTo/amount, missing fee payer or EIP-712 domain, Solana payout wallet
  without a token account), over `max_usd`, charges more than its OpenAPI
  advertises, not HTTPS, unknown token. Cached for 10 minutes.
- `GET /api/v1/diagnose` ($0.01): for sellers debugging their own endpoint.
  Every check (challenge envelope, each `accepts[]` option, resource URL,
  Solana settlement readiness, Bazaar and OpenAPI discovery, browser
  paywall) as pass, warn or fail with a hint to fix it.

Payment is USDC on Solana mainnet (Base is also accepted). Invalid input is
rejected with HTTP 400 before payment.

## Spend-aware usage

- Use `preflight` before a payment and `diagnose` only when you are fixing
  the endpoint yourself.
- Pass `max_usd` (your budget) and `network` (the chain your wallet pays
  on) to preflight so the verdict answers your actual question.
- Preflight results are cached for 10 minutes; one check per endpoint per
  session is enough.
- Pass `method=POST` when the endpoint only accepts POST.
