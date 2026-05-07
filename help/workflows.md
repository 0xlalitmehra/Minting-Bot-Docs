# Common Workflows

Recipe-style end-to-end flows for the things people actually do. Each one assumes you've done the [Quickstart](../getting-started/quickstart.md) and have wallets, RPCs, and (where relevant) proxies/captcha keys set up.

**On this page:**

* [Free OpenSea mint, 50 wallets](#free-opensea-mint-50-wallets)
* [WL drop where only some wallets are eligible](#wl-drop-where-only-some-wallets-are-eligible)
* [Schedule a mint for the exact drop time](#schedule-a-mint-for-the-exact-drop-time)
* [Consolidate winnings to a vault wallet](#consolidate-winnings-to-a-vault-wallet)
* [Top up many wallets from one source](#top-up-many-wallets-from-one-source)
* [Cancel a stuck transaction](#cancel-a-stuck-transaction)
* [Add a custom platform that's not built-in](#add-a-custom-platform-thats-not-built-in)

---

## Free OpenSea mint, 50 wallets

You've got 50 wallets and you want all of them to grab a free public mint on OpenSea.

1. **Wallets:** sidebar → Wallets → pick or create the EVM group with all 50 wallets. Make sure each has enough native token for **gas** (mint is free but gas isn't). On Base ~0.0005 ETH per wallet is plenty; on Ethereum more like ~0.005 ETH.
2. **RPCs:** Wallets bottom toolbar → pick a healthy RPC group for the chain. Refresh balances to confirm.
3. **Tasks:** sidebar → Tasks → **+ New Group** → EVM, platform OpenSea, name "OpenSea XYZ".
4. **+ Create Task:** select all 50 wallets → set RPC group → paste the OpenSea drop URL → click the active phase to auto-fill price/timestamp → quantity 1 → gas Auto.
5. **Run.** Bottom toolbar → tick all → **Run (50)**. Watch the table — successes go green, failures red.

{% hint style="info" %}
**Above ~20 wallets on a popular drop, turn on Batch mode** in the task params. Coordinated leader/follower sequencing improves landing rate vs sending 50 unrelated txs to the mempool.
{% endhint %}

## WL drop where only some wallets are eligible

You've got 200 wallets and a WL drop where you don't know which ones are on the list.

1. **WL Checker:** sidebar → WL Checker → wallet type **EVM** → pick the platform (OpenSea / Mintify / Scatter / Blever supported) → paste the drop URL → pick your 200-wallet group → **Check Eligibility**.
2. Wait for the run to finish. Top cards show **eligible / not eligible** counts.
3. **Spawn a sub-group:** at the bottom of the results, pick the WL phase from the **Create Group** dropdown → click **Create**. You now have a new wallet group containing *only* WL-eligible wallets.
4. **Tasks:** create a new task group, select this new sub-group, configure the WL phase, run.

{% hint style="success" %}
This whole flow takes ~2 minutes for 200 wallets. Without the WL Checker you'd be cross-referencing addresses by hand or wasting gas on ineligible wallets.
{% endhint %}

## Schedule a mint for the exact drop time

The drop announces "10 PM UTC". You want to be ready and walk away.

1. Build the task as you normally would — wallets, RPC, contract, gas, mint params.
2. **Mint Settings → Unix time:** convert the drop time to Unix using the [Dashboard → Unix Converter](../features/dashboard.md#unix-converter) (e.g., `1778097600` for 10 PM UTC on May 6, 2026). Paste it into the Unix time field.
3. **Run** the task. The row goes to **running** but waits in-process — you'll see "Waiting until: HH:MM:SS" counting down.
4. At the timestamp, the tx fires automatically.

{% hint style="warning" %}
**The app must stay open.** Schedules don't survive a quit. Close → reopen erases the in-process timer; you'd need to manually re-run.
{% endhint %}

## Consolidate winnings to a vault wallet

You minted 30 NFTs across 30 wallets. You want them all in one vault for listing.

1. **NFTs page:** sidebar → NFTs → pick chain family **EVM**, RPC group, paste the contract address → **Detect Standard** (auto-detects ERC-721 vs ERC-1155).
2. For ERC-1155, set the token ID range. For ERC-721, no range needed.
3. Click **CONSOLIDATE** → select all 30 source wallets → pick the destination (your vault) → confirm and run.
4. The app sends 30 sequential transfer txs (each source pays its own gas). Failed transfers don't block the rest.

Alternative: enable **Send mode** at task creation and set a destination in [Settings → Wallet Settings → Send NFTs To](../settings/settings.md#wallet-settings). Each successful mint auto-transfers immediately, no consolidation step needed.

## Top up many wallets from one source

Inverse of consolidation. You have one funding wallet and want to seed 50 mint wallets with 0.005 ETH each.

1. **Wallets page:** pick the EVM group with your 50 mint wallets.
2. Bottom toolbar → **Disperse** → pick exactly 1 source (the funding wallet) → pick the 50 destinations → set per-recipient amount (e.g., `0.005`) → confirm.
3. The funding wallet sends 50 transfers in sequence. Watch the source's balance drop accordingly.

For non-native tokens (USDC, ERC-20s), use **Token Disperse** in the same toolbar.

## Cancel a stuck transaction

A task is stuck on **confirming** for 10 minutes. The tx is in the mempool but not getting included because gas was set too low.

1. Go to [Wallets](../core-concepts/wallets.md), tick the affected wallets, → bottom toolbar → **Reset Nonce**.
2. The app self-broadcasts a 0-value tx to the same wallet at higher gas, replacing each pending tx in the mempool.
3. Once those replacement txs land, the original stuck txs are dropped and the local nonce is back in sync.
4. You can now create a new task with appropriate gas and try again.

{% hint style="info" %}
This works because EVM txs are uniquely identified by `(sender, nonce)`. Submitting a new tx with the same nonce + higher gas replaces the older one in the mempool.
{% endhint %}

## Add a custom platform that's not built-in

A new mint pad just dropped and there's no built-in support yet.

1. **Laboratory:** sidebar → Laboratory → **+ New platform**.
2. **Metadata tab:** name it, pick the chain ID, add the platform's hostname to URL Detection so pasted URLs auto-route to your manifest.
3. **Steps tab:** add an `http_request` step pointing at the platform's drop API. Use templates like `{{slug}}` to inject parsed values from the URL.
4. **Eligibility tab:** if the platform exposes phase data, set strategy to `phaseList` and reference your step output (e.g., `{{response.phases}}`).
5. **Save.** Switch to **Test mode**, paste a real drop URL, pick a wallet, click **Run**. Watch the execution log on the right.
6. If the dry-run is green, you can use this platform in the [WL Checker](../features/whitelist-checker.md) and (with Phase 2 features enabled) in [Tasks](../features/tasks.md).

Full walkthrough: [Laboratory → Authoring workflow](../features/laboratory.md#authoring-workflow-end-to-end).

---

Got a workflow that should be here? Open a request in Discord support — recipes get added based on what people actually ask about.
