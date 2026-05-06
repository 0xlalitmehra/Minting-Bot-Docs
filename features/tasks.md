# Tasks

This is the core engine. Tasks are how you actually mint — one task per wallet, configured with a target contract, gas, mint params, and any platform-specific logic. You stage them, run them in batches, schedule them for drop time, and watch them land.

## Mental model

* **Tasks live inside groups.** A group is tied to a chain family (EVM, Solana, Sui, Aptos, Bitcoin) and a platform.
* **One task = one wallet.** If you're minting on 30 wallets, you have 30 tasks (in the same group, for the same drop).
* **Groups have shared gas.** When you bulk-edit gas across a group, every task in it updates.
* Tasks have a **status lifecycle** (idle → running → success/failed/stopped) and emit logs as they run.

## Creating a task group

Click **+ New Group** in the group strip at the top. Pick:

* **Group name** — e.g., "Mintify Drop XYZ"
* **Platform** — one of:
  * **Built-in EVM:** OpenSea, Blever, Element, HyperLaunch, Manifold, Mintify, Scatter, **Custom**
  * **Lab platforms:** anything you've authored in [Laboratory](laboratory.md) appears here too
  * **Non-EVM:** depends on what you've built; the chain family of the group filters the choices

The platform dictates what fields the task creation form will ask for. **Custom** is the escape hatch — you provide raw transaction calldata and the engine just signs and sends.

## Creating a task

With a group selected, click **+ Create Task**. A docked modal slides in. The form has several sections; what you see depends on the platform you picked.

### Resources

* **Wallets** — pick one or more from your wallet groups. Each selected wallet becomes one task.
* **RPC group** — which RPC group routes the transactions. Should be on the right chain.
* **Proxy group** *(optional)* — proxies to rotate through.
* **Captcha keys** *(optional, only if the platform needs it)* — which provider/key to use.

### Gas settings

Two modes:

* **Auto** — picks Normal / Fast / Instant from the live gas tracker. Easiest, fine for most drops.
* **Manual EIP-1559** — set your own **Max Fee** and **Max Priority Fee** in Gwei.
* **Manual Legacy** — single gas price (older chains / contracts that require it).

You can also set a **Gas Limit** override. If you skip it, the engine estimates from the contract.

### Mint parameters

* **Quantity** — how many tokens per transaction.
* **Mint price** — in Wei (or the chain's smallest unit). Set to 0 for free mints. **Per token, not total.**
* **Max retries** — if the tx fails (revert, network error), how many times to retry.
* **Delay before mint (ms)** — wait this long after starting the task before sending the tx.
* **Timestamp** — Unix timestamp (seconds) for [scheduling](#scheduling). Leave at 0 for "run immediately".
* **Spam mode** — retries aggressively on every error; for ultra-competitive drops where you'd rather burn a few wallets than miss.
* **Monitor** — keep watching the tx after broadcast for re-orgs or replacement. Default off.
* **Batch mode** — coordinate leader/follower mints. See [Batch mode](#batch-mode).
* **Send mode** — auto-transfer the minted NFT to a destination wallet after success. Set the destination in [Settings → Wallet Settings → Send NFTs To](../settings/settings.md#wallet-settings).

### Platform-specific fields

Each platform exposes its own form section. Examples:

* **OpenSea / Mintify / Scatter / HyperLaunch:** phase selectors, claim configurations.
* **Manifold:** claim index.
* **Blever:** drop info auto-loaded; pick the phase.
* **Custom:** paste raw hex calldata, or pick an ABI method + params.
* **Lab:** the inputs declared by your manifest.

### Transaction payload

For built-in platforms, this is auto-generated from the contract + phase data. For **Custom** tasks, this is where you paste hex or build the call. The [Dashboard's Contract Hex Generator](dashboard.md#contract-hex-generator) is the easy way to produce one.

Click **Create** and a row per selected wallet appears in the table, all in the **idle** state.

## The task table

Each row shows:

| Column | Content |
|---|---|
| ☑ | Selection checkbox |
| Wallet | Truncated address (mask toggle in header redacts to `*****`) |
| Platform | Logo or manifest icon |
| Gas | Mode + prices ("Auto-FAST", "150 / 160 Gwei"). Mask toggle works here too. |
| Status | A badge — see lifecycle below |
| Actions | ▶ run · ⏹ stop · 🗑 delete · 🔁 repeater |

Selected rows highlight cyan. Errored rows show red. Successes go green.

## Status lifecycle

```
idle → running → confirming → success
                    ↓
                  failed
                    
Any state → stopped (user clicked stop)
```

With **batch mode**: `waiting → running → ...`
With **send mode**: `success → transferring → transferred`

| Status | What it means |
|---|---|
| **idle** | Created but not started, or stopped |
| **running** | Tx submitted to the network |
| **confirming** | In mempool, awaiting block inclusion |
| **success** | Confirmed; the NFT is yours |
| **failed** | Reverted or network errored after retries |
| **stopped** | You clicked stop |
| **waiting** *(batch only)* | Follower waiting for leader |
| **transferring** *(send mode)* | Auto-transferring to destination |
| **transferred** | Delivered to destination |

Failed rows show an error chip with the truncated reason; click to see the tx hash on the explorer (if there is one).

## Running tasks

* **Single task** — ▶ icon on the row.
* **Bulk run** — tick the checkboxes (or the header checkbox to select all in the group), then **Run** in the bottom toolbar. Selected tasks fire in parallel.
* **Stop** — works on a running row, or in bulk on selected.
* **Delete** — only enabled on non-running tasks. Stop first.

## Scheduling

Set the **Timestamp** field to a Unix time (seconds since epoch). When you start the task:

1. It transitions to **running** immediately.
2. But it doesn't broadcast yet — it waits in-process until `now >= timestamp`.
3. The row shows "Waiting until: HH:MM:SS" while it counts down.
4. At the timestamp, it fires.

> **One-time, absolute schedules only.** There's no recurring cron. If you want a task to fire at the same time on three days, create three tasks. Use the [Dashboard's Unix Converter](dashboard.md#unix-converter) to translate human times into Unix.

Setting a past timestamp = "run immediately". You can pre-stage tasks scheduled hours in advance and they'll just wait.

## Repeater

Click 🔁 on a row to set a repeat count. The task will run that many times in succession (mints N times, advancing nonce each time, on the same wallet).

* The row shows progress as `2/5`, `3/5`, etc.
* After the limit is hit, the task auto-stops.
* Useful for FCFS public mints where you want as many as you can grab from one wallet.

## Batch mode

When `Batch mode` is enabled on a group with 2+ wallets, the engine coordinates them:

* The first task you run becomes the **leader**.
* The rest become **followers**, sit in the **waiting** state.
* The leader actually broadcasts the tx; followers fire in lockstep right behind.

This protects against RPC mempool spam when 50 wallets all hit the same contract from the same machine. Useful for leader/follower drops where the platform expects a transaction order.

## Bulk operations

The bottom toolbar (when rows are selected):

* **Run (N)** — start them all
* **Stop (N)** — halt the running ones
* **Delete (N)** — remove (idle/stopped only)
* **Edit Gas** — opens a modal that bulk-updates gas across **all tasks in the current group**. This is more about the group than the selection.

## Display preferences

Header toggles let you hide the **Wallet Address** column or the **Gas** column. Useful for screenshots, or if you want a denser view on a small screen.

## Tips for actually landing mints

* **Test with one wallet first.** Always. On any new platform, a one-wallet dry-run catches platform-specific footguns before you spam 50 wallets into the void.
* **Prefer batch mode for >20 wallets** on a single contract. RPCs will rate-limit you and the leader/follower coordination significantly improves landing rate.
* **Match RPC group to the contract's chain.** The runner doesn't validate this for you — if you point a Base RPC at an Ethereum contract, every task will fail.
* **Use scheduled timestamps for advertised drops.** Don't sit there clicking Run at the millisecond — schedule, walk away, come back to a green table.
* **Watch the first 1–2 results before spamming Retry.** If task 1 reverts with `InsufficientFunds`, hitting Retry on the rest is wasted gas. Open the failed row, read the error, fix the inputs, then re-run.
* **The Custom platform is for advanced cases.** If a built-in platform exists for your drop, use it. Custom skips most of the safety checks.

---

Next: [NFTs](nfts.md).
