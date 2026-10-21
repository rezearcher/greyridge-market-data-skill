# greyridge-market-data-skill

A Hermes / agent skill: live crypto **market data** for a Base/EVM trading or
analysis agent, served by the [Grey Ridge x402 API](https://api.greyridgesignals.ai).

- **Spot prices** (DefiLlama) — `/crypto/prices`
- **Top coins & trending** (CoinGecko) — `/crypto/markets-top`, `/crypto/trending`
- **DeFi yields** — `/defi/yields`
- **One-call market brief** — `/brief/market`

Free `curl` preview, no key or account. Full/unattended use is paid per call with
[x402](https://x402.org) in USDC on Base ($0.001–$0.01/call).

```bash
curl -s "https://api.greyridgesignals.ai/crypto/prices/preview?ids=bitcoin,ethereum,solana"
```

Skill definition: [`skills/greyridge-market-data/SKILL.md`](skills/greyridge-market-data/SKILL.md).
Companion skill: [greyridge-token-safety-skill](https://github.com/rezearcher/greyridge-token-safety-skill)
(pre-trade honeypot/rug check for Base tokens).

MIT licensed. Verdicts/data are heuristics and sourced from third parties — not financial advice.
