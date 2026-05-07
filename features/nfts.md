# NFTs

The NFTs page is a bulk-action tool for moving NFTs between your own wallets. You point it at a contract, pick token IDs (for ERC-1155), and either **consolidate** (many wallets → one) or **disperse** (one wallet → many).

{% hint style="info" %}
**EVM only.** The page has buttons for Solana, Bitcoin, Sui, and Aptos at the top, but only EVM is wired up today. The other tabs show a "currently available only for EVM chains" notice.
{% endhint %}

## When to use this

After a successful mint across many wallets, you usually want everything funneled into a single wallet for listing. Doing that one transfer at a time, by hand, on 50 wallets, is awful. This page does it in one batch.

Disperse is the inverse: you have a stack of NFTs in one wallet and want to seed many wallets with one each (for incoming WL allocations, for example).

## Setup

At the top of the page:

1. **Chain family** — pick **EVM**. (Other tabs are stubs.)
2. **RPC tray** — pick which EVM RPC group to use for this batch. Filters to EVM groups only.
3. **Contract address** — paste the NFT contract.
4. Click **Detect Standard**. The app calls the contract and figures out whether it's **ERC-721** or **ERC-1155** (ERC-20 is detected too but the action UI doesn't support it).

You'll see a "Detected ERC-721" or "Detected ERC-1155" badge after a moment. If you get an error, the address is wrong, the chain is wrong, or the RPC is dead.

## Token IDs (ERC-1155 only)

ERC-1155 contracts mint multiple distinct tokens within one address. You need to tell the page **which token(s)** to move.

You have two options (mutually exclusive — the form disables one when the other is filled):

* **Single Token ID** — e.g., `42` to move only token #42.
* **Token ID Range** — Start and End. The app moves every token in the range (inclusive).

The range is **capped at 2000 IDs** to prevent UI freezes — if you need more, do it in chunks.

The form validates while you type:
* Non-numeric → rejected.
* End < Start → "reversed" error.
* Span > 2000 → "too large".
* Only one side filled → "incomplete".
* Once valid, you'll see "ok" and a count.

For ERC-721, no token ID input is needed — the page uses balance/ownership lookups directly.

## Consolidate

Many source wallets → one destination.

1. Set up contract + token IDs as above.
2. Click **CONSOLIDATE**. An action panel appears.
3. **Pick source wallets** — multi-select from your wallet groups. These are the wallets you're pulling NFTs from.
4. **Pick destination** — a single wallet to receive.
5. Confirm and run. The app sends a sequence of transfer transactions, one per source wallet.

Each source wallet pays its own gas (so make sure they have native token balance). Failed transfers don't block the rest — the batch continues.

## Disperse

One source wallet → many destinations.

1. Same contract + token ID setup.
2. Click **DISPERSE**. The action panel switches to disperse mode.
3. **Pick source wallet** — exactly one.
4. **Pick destinations** — the wallets to send to.
5. Confirm and run.

The source wallet pays gas for all transfers. For ERC-1155 with a range, the app distributes IDs in order across destinations. For ERC-721, it transfers individual tokens to each destination in sequence.

## Status feedback

The action panel shows progress as the batch runs. You'll see per-transfer success/failure and the resulting tx hashes. Once finished, results stay visible until you change the contract or close the page.

## Gotchas

* **Source wallets need native token for gas.** A wallet with NFTs but no ETH/MATIC/whatever will fail the transfer.
* **The detect step has a 500ms debounce** while you type — give it a moment after pasting.
* **ERC-1155 single ID overrides range.** If you fill both fields by accident, only the single ID is used.
* **Marketplace listings count as ownership.** If a wallet has an active listing on a token, the transfer can fail because the marketplace contract has approval. Cancel listings first if you hit weird transfer reverts.
* **Approvals.** Some workflows require setting `setApprovalForAll` on the source wallet first if the contract uses operator-style transfers. The app's transfer flow uses standard `transferFrom` / `safeTransferFrom`, which works for the vast majority of contracts.
* **OpenSea is the default lookup source.** If a contract doesn't index well there, you may see incomplete inventory. Use the explicit token ID inputs to override.

---

Next: [Laboratory](laboratory.md).
