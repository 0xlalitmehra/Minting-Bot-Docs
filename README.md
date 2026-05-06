# Welcome to Degens

> **All-in-one blockchain automation tool for the Degens community.**

Degens is a desktop app that handles the parts of minting that suck — managing wallets, juggling RPCs, checking whitelist eligibility, running mints across multiple wallets, and authoring custom platform integrations when something new drops.

It's built as a native desktop app (Tauri), runs locally on your machine, and stores everything — wallets, RPCs, proxies, captcha keys, manifests — in a local database. **Nothing about your wallets ever leaves your computer.**

## What you can do with it

| Feature | What it's for |
|---|---|
| **Wallets** | Generate or import wallets across EVM, Solana, Bitcoin, Sui, and Aptos. Group them, refresh balances, consolidate or disperse. |
| **RPCs** | Manage RPC endpoint groups per chain. Ping, sort by latency, kick the slow ones. |
| **Proxies** | Add proxy lists in `ip:port[:user:pass]` format. Test against a target URL. |
| **Captcha** | Plug in API keys for CapMonster, 2Captcha, or CapSolver. Tasks pull from these automatically. |
| **Dashboard** | Quick utilities — gas calculator (with floor-based aggression slider), contract hex generator, PnL calc, unix converter. |
| **Whitelist Checker** | Bulk-check eligibility across OpenSea, Mintify, Scatter, and Blever. Spawn a new wallet group from the eligible ones in one click. |
| **Tasks** | The core engine. Schedule mints, run them in batches, retry on failure, auto-forward NFTs. |
| **NFTs** | Bulk consolidate or disperse ERC-721 and ERC-1155 tokens across your wallets. |
| **Laboratory** | Build your own platform integrations as JSON manifests. Dry-run them before going live. |

## Who this is for

If you mint NFTs as a hobby, a hustle, or both — and you're tired of doing it ten times in a row by hand — you're in the right place. Degens assumes you know what a private key is, what gas is, and what "WL" means. We don't pretend otherwise.

If you're brand new to all of this, start with [Getting Started](getting-started/installation.md) and read it front-to-back before touching wallets. Mistakes with private keys are forever.

## How to use this guide

* **Brand new?** Read [Getting Started](getting-started/installation.md) → [Interface Tour](getting-started/interface-tour.md), then dip into the [Core Concepts](core-concepts/wallets.md) for wallets, RPCs, and proxies.
* **Just want to mint something?** Skim [Wallets](core-concepts/wallets.md), [RPCs](core-concepts/rpcs.md), then jump to [Tasks](features/tasks.md).
* **Building a custom platform?** Go straight to [Laboratory](features/laboratory.md).
* **Something broke?** [Troubleshooting](help/troubleshooting.md) and [FAQ](help/faq.md).

## A note on safety

This app handles private keys. A few things to internalize before you go further:

* **Your keys live on your computer, encrypted, in a local database.** They aren't sent anywhere.
* **There is no cloud backup.** If you wipe the app or your machine, your imported keys are gone unless you backed up the seed phrases yourself, somewhere safe.
* **Test with throwaway wallets first.** Especially on Tasks and Laboratory. There's no "undo" on a mainnet transaction.
* **Don't share screenshots that include private keys, mnemonics, or unmasked addresses.** The app has a mask toggle on every sensitive screen — use it.

Now go mint something.
