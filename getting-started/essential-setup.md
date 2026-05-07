# Essential Setup

Before you start minting, configure these four fields in **Settings**. The app will technically launch without them, but several core features (live gas tracking, contract introspection, simulation, send-mode) won't work correctly until they're filled in.

Open Settings (gear icon at the bottom of the sidebar).

{% hint style="warning" %}
**Treat this page as a prerequisite, not optional.** If you skip it and then run into "gas tracker shows nothing", "Contract Hex Generator can't load ABI", or "Send mode didn't transfer" later — the answer is almost always one of these four fields.
{% endhint %}

## 1. Alchemy API Key

The sidebar's live Ethereum gas tracker fetches data via Alchemy. Without a key, you either get nothing or hit a public-RPC rate limit within minutes.

**Get a free key:**

1. Sign up at [alchemy.com](https://www.alchemy.com/).
2. Create a new app on **Ethereum mainnet**.
3. From the app's dashboard, open the **API Key** section.

{% hint style="danger" %}
**Paste only the key — not the full URL.**

Alchemy's dashboard typically shows the URL like this:

```
https://eth-mainnet.g.alchemy.com/v2/AbC123xYz_yourKeyHere
```

Paste **only the part after `/v2/`** — `AbC123xYz_yourKeyHere` — into the **Alchemy API Key** field in Settings. The app appends the URL prefix internally.

If you paste the full URL, the gas tracker will silently fail.
{% endhint %}

After pasting, click **Save**, then **Test** to verify. A success toast means you're good.

## 2. Etherscan API Key

Used by the Dashboard's **Contract Hex Generator** (and anywhere else the app needs to read function signatures from a deployed contract) to auto-load verified contract ABIs.

**Get a free key:**

1. Sign up at [etherscan.io](https://etherscan.io/).
2. Go to your **Account → API Keys → + Add**.
3. Copy the key string.

Paste it into the **Etherscan API Key** field, click **Save**, then **Test**.

## 3. Simulation Wallet Address

The Dashboard's Contract Hex Generator has a **Simulate** option that runs a contract call without broadcasting it — useful for sanity-checking that a call would succeed, or that the calldata is shaped right.

For the simulation to be meaningful, it needs a "from" address — otherwise the call runs as if from `0x0` and any balance / allowance / ownership check returns zero-state.

**What to enter:** an EVM wallet address (40 hex chars, starts with `0x`). Use one of your funded minting wallets. The app only needs the **address**, not the private key.

## 4. Send NFTs To

The destination wallet for **Send mode** in tasks. When a task with Send mode enabled succeeds, the freshly minted NFT is auto-transferred to this address — no manual consolidation step needed.

**What to enter:** an EVM address you control. Most users point this at a cold / vault wallet so winnings go straight to safety the moment they're minted.

If you skip this and later turn on Send mode in a task, the task will succeed but the post-mint transfer will fail (no destination set).

---

Once all four fields are saved and green, you're set up. Continue with the [Quickstart](quickstart.md) for a 5-minute test mint, or dive into [Wallets](../core-concepts/wallets.md) to build your minting infrastructure properly.
