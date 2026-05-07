# FAQ

Quick answers to the most common questions.

**On this page:**

* [General](#general)
* [Subscription](#subscription)
* [Wallets](#wallets)
* [RPCs](#rpcs)
* [Tasks](#tasks)
* [Whitelist Checker](#whitelist-checker)
* [Laboratory](#laboratory)
* [Updates](#updates)
* [Privacy & safety](#privacy--safety)

---

## General

### Is this a wallet?

No. Degens *manages* wallets — generates, imports, signs with them — but it's not a custodial wallet, an exchange, or a browser extension. Your keys live encrypted on your machine, in the app's local data folder.

### Does Degens see my private keys?

Only the app does, on your machine. Keys are encrypted at rest, decrypted in-memory only when needed to sign a transaction, and never transmitted. The Discord auth flow does not touch your wallets.

### Does it support [chain]?

Built-in:

* **EVM** (every EVM chain, configurable per RPC group)
* **Solana**
* **Sui**
* **Aptos**
* **Bitcoin**

Via [Laboratory](features/laboratory.md) manifests:

* Any EVM chain via custom platforms (today)
* Non-EVM Lab platforms are roadmap, not shipped

### Is my data synced anywhere?

No. Everything — wallets, RPCs, proxies, captcha keys, manifests, settings — is local. There's no cloud account, no sync. The flip side: there's no cloud backup either. **Use [Export Data](settings/settings.md#export-data) regularly.**

### Can I run it on Linux?

Not officially. Windows and macOS are the supported platforms. Linux isn't shipped, supported, or auto-updated.

### Can I run two instances on different machines?

No — device binding pins your account to one machine at a time. To switch, log out on machine A, then log in (or reset) on machine B. See [First Launch → Device binding](getting-started/first-launch.md#device-binding).

## Subscription

### How do I subscribe / renew?

Run `/pay` in any channel of the Degens Discord server. The Suby bot handles billing and grants you the role.

### My subscription expired mid-mint

The session validation runs periodically. Once the role is revoked server-side, the next check signs you out. Renew, log back in, your data is still intact.

### Can I refund?

That's a Discord/Suby billing question — talk to the team in support.

## Wallets

### What chains can I import private keys for?

EVM, Solana, Bitcoin, Aptos, Sui — all five chain families.

### Can I import a seed phrase and derive multiple wallets?

EVM and Sui support seed-phrase import with a passphrase. The app derives the first N accounts from the standard derivation paths. Other chains are private-key import only.

### How are keys encrypted?

The app encrypts keys before storing them locally. Encryption is tied to your operating system's secure store, so the file isn't usable if someone copies it off your machine.

### Can I export a wallet's private key?

Yes — open the wallet's edit modal and click the show/copy buttons. Or bulk-export a group via [Settings → Export Data](settings/settings.md#export-data) to CSV.

### What's "watch-only"?

A wallet imported as just an address, with no key. It can show balances and be referenced in displays, but it can't sign transactions. Useful for tracking a vault address.

## RPCs

### Why do I need to bring my own RPCs?

Public RPCs rate-limit hard. During a hyped drop, public endpoints will throttle you and you'll lose mints. A paid Alchemy / Infura / QuickNode key is dramatically more reliable.

### How many RPCs per group?

Up to **500**. You won't need that many; 5–10 high-quality endpoints is usually enough.

### Why is the RPCs page EVM-only?

Solana, Sui, Aptos, and Bitcoin use the chain SDKs' built-in defaults; you don't manage RPCs the same way. (You may be able to override defaults in future versions, but it's not in the UI today.)

## Tasks

### What platforms are built-in?

OpenSea, Blever, Element, HyperLaunch, Manifold, Mintify, Scatter, and **Custom** (raw payload). Plus anything you add via [Laboratory](features/laboratory.md).

### Does it support free mints?

Yes. Set the **Mint price** to 0 in the task params.

### Can I schedule recurring tasks?

No. Schedules are **one-time absolute timestamps**. For recurring use, the task **Repeater** runs the same task N times in a row from the same wallet.

### What's batch mode for?

Coordinating leader/follower mints when many wallets hit the same contract. The leader broadcasts; followers fire in lockstep behind. Reduces RPC mempool pressure and improves landing rate. See [Tasks → Batch mode](features/tasks.md#batch-mode).

### Can I see logs for a task?

Failed tasks show an error chip with a truncated reason and a link to the tx hash. A full log panel per task is on the roadmap.

### Send mode failed silently

Check [Settings → Wallet Settings → Send NFTs To](settings/settings.md#wallet-settings) — it has to be set, and the task has to have **Send mode** enabled.

## Whitelist Checker

### Why aren't all platforms supported?

Element, HyperLaunch, and Manifold use authentication flows or phase APIs the checker can't safely replicate. They work in [Tasks](features/tasks.md) where you provide context, but not in the bulk checker.

### Can I export the results?

Not directly — but you can spawn a new wallet group from the eligible wallets, which is usually what you wanted anyway. See [WL Checker → Spawning a new wallet group](features/whitelist-checker.md#spawning-a-new-wallet-group-from-eligible-wallets).

### Results disappeared

Results are in-memory only — they don't persist across app restarts (or page reloads). Re-run if needed.

## Laboratory

### How do I share a manifest?

Open the **Raw JSON** tab, copy the JSON, paste it where you want (Discord, gist, etc.). Recipients import by pasting back into their own Raw JSON tab on a new draft.

### Can I publish a manifest publicly?

There's no built-in registry. Distribution is community-led — usually a Discord channel.

### What's the difference between Phase 1 and Phase 2?

Phase 1 is read-only: HTTP requests, data extraction, eligibility computation. Suitable for WL checkers.
Phase 2 unlocks message signing, contract calls, and transaction sends — the parts needed for actual minting.
You upgrade a manifest by clicking **Enable Phase 2 features** in the editor. Once upgraded, you can't downgrade.

### Why doesn't my manifest show up in the WL Checker?

Most likely the eligibility section isn't fully configured, or the chain ID doesn't match the wallet group you've selected. Double-check the Eligibility tab and the metadata's Chain IDs.

## Updates

### How often does the app check for updates?

Every 6 hours after login. You can also trigger a manual check from [Settings → Check for Updates](settings/settings.md#about--updates).

### What if I miss an update?

Skipped updates re-prompt when a newer version ships. Or trigger a manual check any time.

### Will updates kill my running tasks?

Yes — the app auto-relaunches after install. **Don't update mid-drop.**

## Privacy & safety

### Does Degens phone home?

For three things:
1. **Auth validation** — login plus a periodic check that your subscription is still active.
2. **Update check** — to see if a newer version is out.
3. **Network requests you initiate** — RPC calls, platform APIs, captcha solvers.

Wallet keys are not transmitted. Manifests, settings, captcha keys are not transmitted.

### Should I use a separate Discord account for the subscription?

Up to you. The auth scopes are minimal (identity + guild membership), and Degens doesn't post on your behalf. Most users use their main account.

### Should I use a hardware wallet with this?

Hardware wallets aren't currently supported as signers. Mints happen with hot keys (private keys the app holds). Use throwaway wallets for risky drops, and transfer winnings to cold storage afterward (the [NFTs](features/nfts.md) page makes that bulk-easy).

### What happens to my data if Degens shuts down?

The app stops updating, but your local install keeps working as-is. Your wallets, manifests, and settings are local — Degens going away doesn't take them with it. (Until something on the auth side stops responding, at which point you'd be locked out of *new* logins. Export your wallets now, not later.)
