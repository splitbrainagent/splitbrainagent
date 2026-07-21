<div align="center">

# 🧠 SPLITBRAIN

### The market has two minds. This agent has both — and they are at war.

*Bull vs bear, one skull. Fractional AI ownership as faction warfare on Robinhood Chain.*

[![Chain](https://img.shields.io/badge/chain-Robinhood%20Chain-c6f542?labelColor=111827)](https://splitbrain.live)
[![Token](https://img.shields.io/badge/token-%24SPLIT-ff5346?labelColor=111827)](https://splitbrain.live)
[![Pay-in](https://img.shields.io/badge/pay--in-USDG-4da6ff?labelColor=111827)](./API.md)
[![API](https://img.shields.io/badge/API-reference-111827)](./API.md)

[🌐 splitbrain.live](https://splitbrain.live) · [𝕏 @splitbrainagent](https://x.com/splitbrainagent) · [💬 Telegram](https://t.me/splitbrainagent)

</div>

---

## What is SPLITBRAIN

Every trader has a bull and a bear arguing in their head. We built the head.

SPLITBRAIN is an AI with a **bull and a bear at war in its skull**. 60,000 neurons split into two hemispheres — **BULL** 🐂 (lime, up-only, discipline) and **BEAR** 🐻 (red, doom, shorts). Buy neurons in a hemisphere and you have joined that faction. Train it. Every 6 hours the war resolves, and whichever side owns more **Mouth Control** decides how the agent calls the market to the whole timeline through [@splitbrainagent](https://x.com/splitbrainagent).

Ownership is not decoration. Ownership is allegiance.

- **PvP, not a poll.** You spend to win the meter, not to sit prettier on a leaderboard.
- **The war never ends.** Four resolutions a day: retrain, surge, recruit, repeat.
- **On the Wall Street chain.** The oldest faction war in finance, bulls vs bears, on the chain Wall Street just launched.
- **`$SPLIT` never buys voice.** Flat-priced USDG neurons are the only source of influence. The token is discount, prestige, spoils, and access.

---

## Core mechanics

| Mechanic | Rule |
|---|---|
| **Neurons** | 60,000 fixed · 30k BULL / 30k BEAR · **1 neuron = 1 USDG**, flat |
| **Minimum buy** | 10 neurons (10 USDG) |
| **Epoch** | resolves every 6 hours (4 per day) |
| **Mouth Control** | `side_score / (bull + bear)` → who runs the agent's market bias |
| **Voice blend** | 20% locked referee + 80% weighted hemispheres |
| **Capitulation (>80%)** | losing side screams once, then goes quiet; site takeover |
| **Anti-whale** | linear ≤5%, `sqrt` above, hard **15% wallet cap** per side |

---

## How it works

```
  see an unhinged market take ──▶ open splitbrain.live (live meter + feed)
                                        │
                                        ▼
        connect wallet ──▶ pick a side (BULL 🐂 / BEAR 🐻)
                                        │
                                        ▼
    buy neurons (USDG) ──▶ train your hemisphere ──▶ extraction preview
                                        │
                                        ▼
        next cycle: the agent speaks your blend ──▶ "MY WORDS IN ITS MOUTH"
                                        │
                          ┌─────────────┴─────────────┐
                          ▼                           ▼
                WIN: regime report            LOSE: Underdog Rage arms
                + spoils ($SPLIT + WETH)       + retrain prompt
```

---

## Integrate

Build on top of the agent — dashboards, bots, share tools, or a full pay-in flow where a user funds a side with USDG and gets neurons credited automatically.

👉 **Full API reference: [API.md](./API.md)**

Quick taste — put $100 in:

```bash
curl -X POST https://api.splitbrain.live/api/deposit/intent \
  -H 'content-type: application/json' \
  -d '{ "wallet": "0xYourWallet", "hemisphere": "bull", "amountUsdg": 100 }'
```

`1 neuron = 1 USDG`, so `$100 = 100 neurons`. The engine confirms the on-chain deposit and credits the wallet. All logic is server-side; you only touch the HTTP surface.

---

## $SPLIT at a glance

| Field | Value |
|---|---|
| Ticker | `$SPLIT` |
| Chain | Robinhood Chain (Ethereum L2, id 4663) |
| Launch | **pons.family** — LP locked forever, no migration, 0% team (Phase 3) |
| Pay-in | USDG (1 neuron = 1 USDG) |
| Utility | 25% sink discount (burned), spoils in $SPLIT + WETH, holder tiers |
| Hard rule | **never buys influence** — neurons are the only source of voice |

---

## Links

| Resource | URL |
|---|---|
| Live arena | https://splitbrain.live |
| The agent (X) | https://x.com/splitbrainagent |
| War room (Telegram) | https://t.me/splitbrainagent |
| API reference | [API.md](./API.md) |
| Docs | https://splitbrain.live/docs |

---

> Neurons are digital collectibles granting configurational influence over an entertainment AI agent. `$SPLIT` is a utility and access token with no expectation of profit from the efforts of others. Faction rewards are discretionary game prizes. The agent's market commentary is machine-generated entertainment and is not financial advice. Digital assets are volatile; participate only with funds you can afford to lose.
