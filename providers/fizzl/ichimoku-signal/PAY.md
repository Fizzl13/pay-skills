---
name: ichimoku-signal
title: "Ichimoku Signal"
description: "Live Ichimoku Cloud signal for a crypto pair from Binance.US candles: bullish, bearish or neutral, price vs. cloud, tenkan/kijun cross and the tenkan, kijun and senkou A/B line values as JSON."
use_case: "Use for Ichimoku Cloud analysis, crypto trend direction, above/below-the-cloud checks, tenkan-kijun crosses, trading agent entry filters and technical signals for pairs like SOL-USDT or BTC-USDT on 1m to 1M timeframes."
category: finance
service_url: https://ichimoku-signal.onrender.com
version: v1
openapi:
  path: openapi.json
---

Ichimoku Signal computes the Ichimoku Cloud for a spot pair listed on
Binance.US and returns a single verdict (`bullish`, `bearish` or `neutral`)
together with the inputs behind it: where the price sits relative to the
cloud (`above_cloud`, `below_cloud`, `in_cloud`), the tenkan/kijun cross and
every line value. Candles are fetched live on each call.

One call costs $0.02 USDC on Solana mainnet (Base is also accepted). The
service validates the pair before the payment step: an unknown pair returns
HTTP 400/404 without a charge, and a request that errors after payment is
not settled.

## Identifier formats

- `pair` is a Binance.US spot pair: `BTC-USDT`, `ETH-USDT`, `SOL-USDT`, or
  without the dash (`BTCUSDT`). Case-insensitive.
- `interval` is one of `1m 3m 5m 15m 30m 1h 2h 4h 6h 8h 12h 1d 3d 1w 1M`
  (default `1h`).

## Spend-aware usage

- One call answers one pair on one timeframe; ask for the timeframe the task
  needs instead of sweeping all of them.
- The signal only changes when a candle closes: reuse a result for the rest
  of the current candle (for example up to an hour on `1h`) instead of
  polling.
- Use the `signal` field for a quick go/no-go and the line values only when
  the user asks for the detail.
