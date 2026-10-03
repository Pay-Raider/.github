<div align="center">

# PayRaider

**Check a Stellar payment corridor before you pay out.**

One API call tells an off-ramp or payout app whether a corridor is safe to pay on right now, and what to do if it isn't.

[Backend](https://github.com/Pay-Raider/payraider-backend) · [Web app](https://github.com/Pay-Raider/payraider-app) · [Plugin & SDKs](https://github.com/Pay-Raider/payraider-plugin) · [Contracts](https://github.com/Pay-Raider/payraider-contracts) · [Mobile](https://github.com/Pay-Raider/payraider-mobile)

</div>

---

## The problem

Apps that pay people out over Stellar (off-ramps, remittance and payroll apps, wallets with cash-out) send money into payment corridors they do not control. When a corridor is degraded, or does not have enough liquidity for the amount, the payout fails or gets stuck. The app absorbs the cost, and the customer loses trust.

Today these apps send blind. There is no simple way to ask, at the moment of payment, *"is this route healthy?"*

## What PayRaider does

Before the money moves, the app asks PayRaider about the payment:

```
USDC → NGN, $2,500
```

PayRaider looks at recent payments on that corridor on the Stellar ledger and answers with one decision:

| Decision | Meaning |
| --- | --- |
| ✅ **proceed** | The corridor is healthy for this amount. Pay. |
| ⚠️ **caution** | Something is marginal. Pay with care, or reroute. |
| ⛔ **hold** | Something failed. Don't pay on this corridor now. |
| ❔ **unknown** | No recent data. No guess is made. |

Every answer comes with the reasons behind it and, when there is one, a healthier corridor to the same currency.

## How it decides

| Check | What it looks at |
| --- | --- |
| Success rate | Share of recent payments on the corridor that succeeded, failed ones included |
| Liquidity | How large the payment is compared with the corridor's observed liquidity |
| Sample size | Whether there is enough recent activity to trust the numbers |
| Health score | Overall corridor health, combining success, volume and activity |

Any failed check means **hold**; any marginal check means **caution**. The data is observed on the ledger, not predicted by a model.

## How teams use it

- **REST API**: `GET /api/v1/preflight?source_asset=USDC&destination_asset=NGN&amount_usd=2500`
- **SDKs**: TypeScript and Python, with a React hook
- **AI agents**: an MCP server, so assistants and support bots can run the check
- **Claude**: a plugin with a `/preflight` command

## Pricing

- **Free** to try, with no sign-up or API key.
- **API keys**, created by signing in with a Stellar wallet, give an app its own higher limit.
- **Pro plan**, paid in **USDC on Stellar**. The API verifies the payment on the ledger itself, so there are no cards, accounts or billing integrations.

## Repositories

| Repository | What it is | Stack |
| --- | --- | --- |
| [payraider-backend](https://github.com/Pay-Raider/payraider-backend) | The API: corridor data, the pre-payment check, wallet sign-in, API keys, USDC billing | Rust · Axum · SQLite |
| [payraider-app](https://github.com/Pay-Raider/payraider-app) | Web app: corridor explorer, pre-payment check, self-serve API keys and upgrades | Next.js · TypeScript |
| [payraider-plugin](https://github.com/Pay-Raider/payraider-plugin) | MCP server, Claude plugin, and the TypeScript, Python and React SDKs | TypeScript · Python |
| [payraider-contracts](https://github.com/Pay-Raider/payraider-contracts) | Soroban contract that anchors hashes of analytics snapshots on-chain | Rust · Soroban |
| [payraider-mobile](https://github.com/Pay-Raider/payraider-mobile) | Mobile app (early stage) | React Native |

## Built on Stellar

- **Ledger data**: corridor health comes from Stellar payments read through Horizon.
- **Wallet sign-in**: accounts are Stellar wallets, proven by signing a challenge.
- **USDC payments**: plans are bought and verified on-chain.
- **Soroban**: analytics snapshots are hashed and anchored on-chain, so published numbers can be checked against the ledger.

## Status

The API, web app, SDKs, plugin and billing are built and covered by automated tests, and have been run end to end on Stellar testnet. Next: a public deployment, and pilots with off-ramp partners.

Interested in piloting PayRaider on your corridors? Open an issue in [payraider-backend](https://github.com/Pay-Raider/payraider-backend/issues).

<div align="center">

<sub>Open source under the Apache 2.0 license.</sub>

</div>
