---
name: greyridge-market-data
description: Live crypto market data for a Base/EVM trading agent — spot prices (DefiLlama), top coins & trending (CoinGecko), DeFi yields, and a one-call market brief. Free preview with curl, cheap paid x402 routes ($0.001–$0.01/call in USDC on Base) for full/unattended use.
version: 0.1.0
---

# Grey Ridge market data — prices, yields and a market brief for your agent

Use when an agent needs current crypto prices, top/trending coins, DeFi yields,
or a quick market snapshot before reasoning, quoting a user, or sizing a trade.

## When to use

- User asks "what's the price of X", "what's trending", "best yields on Base", or "how's the market".
- A trading/analysis loop needs fresh spot prices, market caps, or yield data.

## Get spot prices (no wallet, no key, no signup)

```bash
curl -s "https://api.greyridgesignals.ai/crypto/prices/preview?ids=bitcoin,ethereum,solana"
```

Preview returns a free 1-of-N sample. Each coin has `price`, `change_24h`,
`symbol`, `confidence` and `timestamp`. Full list (up to 25 tokens) via the paid
route below.

## Other routes

```bash
# Top DeFi yields (free sample)
curl -s "https://api.greyridgesignals.ai/defi/yields/preview?chain=base"
```

Each pool has `project`, `chain`, `symbol`, `pool` id, `apy` (+ base/reward split)
and 1d/7d/30d APY trend.

## Paid routes (x402, USDC on Base — full data, unattended use)

No API key or account; pay per call with x402. Prices are the live `402` amount:

| Route | Price | Returns |
|-------|-------|---------|
| `GET /crypto/prices?coins=<ids>` | $0.004 | Spot price/24h-change/symbol/confidence for up to 25 CoinGecko ids |
| `GET /crypto/markets-top?limit=<n>` | $0.003 | Top coins by market cap: price, mcap, 24h vol, 24h change (limit up to 100) |
| `GET /crypto/trending` | $0.003 | Trending coins now: name, symbol, mcap rank, 24h change |
| `GET /defi/yields?chain=<chain>` | $0.001 | Top DeFi lending/LP yields with APY breakdown + IL risk |
| `GET /brief/market` | $0.01 | One-call brief: Fear & Greed, global mcap/dominance, movers, trending, top DeFi fee earners |

### Make a paid call (runnable — this is the whole answer)

The free preview is rate-limited and returns a 1-of-N sample, so for an
**unattended agent or any real/high-volume use the paid route is the default**,
not a fallback. There's no key, account, or gas ETH — the x402 scheme is gasless
EIP-3009: you sign a USDC authorization inline and the facilitator settles it.

```js
// npm i x402-fetch viem   —  then: EVM_PRIVATE_KEY=0x... node pay.mjs
import { createWalletClient, http } from "viem";
import { privateKeyToAccount } from "viem/accounts";
import { base } from "viem/chains";
import { wrapFetchWithPayment } from "x402-fetch";

const account = privateKeyToAccount(process.env.EVM_PRIVATE_KEY); // Base EOA holding a little USDC
const walletClient = createWalletClient({ account, chain: base, transport: http() });
const pay = wrapFetchWithPayment(fetch, walletClient); // 2nd arg is a viem WalletClient, NOT a bare account

const r = await pay("https://api.greyridgesignals.ai/defi/yields?chain=base"); // $0.001 — swap for any route above
console.log(r.status, await r.json());
```

That one call signs + settles the USDC and returns the full data. The private key
is a normal Base EOA with a little USDC; nothing leaves the process except the
signed authorization the x402 facilitator submits.

Prefer Python / LangChain / CrewAI? Same thing via the helper (pin the commit):
`pip install "git+https://github.com/rezearcher/greyridge-x402-tools@main"`.

## Limits (stated plainly)

- Prices/trending/markets are CoinGecko + DefiLlama sourced; freshness is their
  cadence (seconds–minutes), not a live orderbook. `confidence` is passed through.
- The free preview is rate-limited; unattended/high-volume use needs the paid route.
- Not financial advice.
- Measured status (uptime/latency window): https://github.com/rezearcher/x402-data-api/blob/main/docs/STATUS.md
