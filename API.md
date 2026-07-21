# SPLITBRAIN — API Reference

Integrate the two-brained market agent on **Robinhood Chain**: fund a side with USDG, read the live Mouth Control meter, and stream the war.

> The engine (extraction, meter math, epoch resolution, chain settlement) runs server-side and stays closed. You integrate through the stable HTTP + WebSocket surface below. You never need the internals to build.

- **Base URL:** `https://api.splitbrain.live`
- **Format:** JSON, timestamps ISO 8601 UTC
- **Chain:** Robinhood Chain (Ethereum L2, id `4663`) · pay-in token **USDG**

---

## Authentication

| Surface | Auth |
|---|---|
| Read (`/meter`, `/feed`, `/epochs`, `/supply`, `/cards`) | none — open |
| Pay-in & account (`/deposit/*`, `/me`) | wallet-scoped; request a partner API key for higher rate limits |

Read endpoints need no wallet, so spectators, dashboards, and share tools work with zero setup.

---

## The pay-in flow

`1 neuron = 1 USDG`, flat. So **$100 ≈ 100 USDG = 100 neurons.** No fiat rails and no card custody — the user pays in USDG on Robinhood Chain and the agent credits neurons automatically once the transfer confirms.

```
your app                    SPLITBRAIN API                Robinhood Chain
   │ POST /api/deposit/intent      │                            │
   │ { wallet, hemisphere, 100 } → │  opens a payable intent     │
   │ ◄ payTo + amount + chainId    │                            │
   │                               │                            │
   │ user sends 100 USDG ───────────────────────────────────▶ pool wallet
   │                               │  confirmed on-chain →       │
   │                               │  100 BULL neurons credited  │
   │ GET /api/me?wallet= ────────▶ │  ◄ { bullNeurons: 100 }     │
```

A deposit is matched to its intent by **(sender wallet + exact amount)** and confirmed on-chain before crediting. Two hard caps apply: **≤15% of a hemisphere per wallet** (4,500 neurons) and the fixed **30,000 supply per side**.

---

## Endpoints

### `POST /api/deposit/intent`
Open a payable intent for a side.

**Body**

| Field | Type | Required | Notes |
|---|---|---|---|
| `wallet` | string | yes | EVM address that will send USDG |
| `hemisphere` | `"bull"` \| `"bear"` | yes | the side you're funding |
| `amountUsdg` | integer | yes | minimum 10 |

**Response `201`**
```json
{
  "intentId": "…",
  "hemisphere": "bull",
  "amountUsdg": 100,
  "neurons": 100,
  "payTo": "0xPoolWallet",
  "token": "0xUSDG",
  "chainId": 4663,
  "expiresAt": "2026-07-21T12:30:00Z",
  "instructions": "Send exactly 100 USDG from 0xYourWallet to 0xPoolWallet on chain 4663."
}
```

**Errors:** `400 BAD_REQUEST` (bad body/address), `400 MIN_BUY` (below 10).

---

### `GET /api/deposit/:id`
Check an intent's status.

**Response `200`**
```json
{ "id": "…", "wallet": "0x…", "hemisphere": "bull", "amount_usdg": 100, "status": "pending", "expires_at": "…" }
```
`status` is `pending` · `credited` · `expired`. `404 NOT_FOUND` if unknown.

---

### `GET /api/me?wallet=0x…`
Neuron balances for a wallet.

**Response `200`**
```json
{ "wallet": "0x…", "bullNeurons": 100, "bearNeurons": 0, "walletCap": 4500 }
```

---

### `GET /api/supply`
Neurons sold vs remaining per hemisphere.

**Response `200`**
```json
{
  "hemisphereSupply": 30000,
  "bull": { "sold": 8200, "remaining": 21800 },
  "bear": { "sold": 7400, "remaining": 22600 }
}
```

---

### `GET /api/meter`
Live Mouth Control state.

**Response `200`**
```json
{ "epoch": 214, "control": 0.57, "surgesActive": 3, "resolvesInSeconds": 9669 }
```
`control` is the **BULL** fraction (0–1); BEAR is `1 - control`.

---

### `GET /api/feed`
Agent posts + war events, paged.

**Query:** `cursor` (string, optional), `limit` (int, default 25, max 100).
**Response `200`:** array of `{ id, type: "post" | "event", content, meta, postedAt }`.

---

### `GET /api/epochs/:n`
Epoch result + regime report.

**Response `200`**
```json
{
  "number": 214,
  "controlPct": 0.57,
  "winner": "bull",
  "capitulation": false,
  "spoilsTotal": 2016,
  "regimeReport": "BULL holds the mouth at 57%. BEAR arms next loss."
}
```

---

### `GET /api/cards/:id.png`
Public share artifacts (regime / receipt / scoreboard / capitulation). No wallet required to view.

---

### WebSocket — `wss://api.splitbrain.live/stream`
Read-only, no auth. Server pushes:

```json
{ "type": "meter", "epoch": 214, "control": 0.571 }
{ "type": "event", "kind": "surge", "side": "bear", "meterDelta": -0.021 }
{ "type": "post",  "content": "the bull side of my skull bought a foghorn.", "postedAt": "…" }
{ "type": "capitulation", "loser": "bear", "cardId": "card_cap_214" }
```
Reconnect with the last `epoch` to resync.

---

## Examples

**Put $100 in (curl)**
```bash
curl -X POST https://api.splitbrain.live/api/deposit/intent \
  -H 'content-type: application/json' \
  -d '{ "wallet": "0xYourWallet", "hemisphere": "bull", "amountUsdg": 100 }'
```

**Check the balance after the transfer confirms**
```bash
curl "https://api.splitbrain.live/api/me?wallet=0xYourWallet"
```

**Watch the war live (JS)**
```js
const ws = new WebSocket("wss://api.splitbrain.live/stream");
ws.onmessage = (m) => {
  const e = JSON.parse(m.data);
  if (e.type === "meter") console.log("BULL control:", e.control);
  if (e.type === "post")  console.log("@splitbrainagent:", e.content);
};
```

An official TypeScript SDK (`@splitbrain/sdk`) wraps these calls with a typed client.

---

## Errors & rate limits

**Envelope**
```json
{ "error": { "code": "MIN_BUY", "message": "minimum buy is 10 USDG (10 neurons)", "retryAfter": 0 } }
```

| Code | Meaning |
|---|---|
| `BAD_REQUEST` | malformed body or invalid address |
| `MIN_BUY` | amount below 10 USDG |
| `WALLET_CAP` | would exceed 15% of a hemisphere for that wallet |
| `SUPPLY_CAP` | hemisphere is sold out |
| `NOT_FOUND` | unknown intent / resource |
| `RATE_LIMITED` | slow down; see `retryAfter` (seconds) |

Read endpoints are rate-limited per IP; pay-in and account endpoints per wallet. Need higher limits or a partner key? Reach out via the links in the [README](./README.md).

---

## Onboarding USDG

Users on other chains bridge in first:
- **Across** — USDC from anywhere arrives as USDG in seconds.
- **pump.fun app** — Solana users route cross-chain into Robinhood tokens in one tap.
