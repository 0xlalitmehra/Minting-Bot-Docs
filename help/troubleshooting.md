# Troubleshooting

Things break. Here's what to check before throwing the laptop.

**Jump to a section:**

* [Login & auth](#login--auth)
* [Wallets](#wallets)
* [RPCs](#rpcs)
* [Proxies](#proxies)
* [Captcha](#captcha)
* [Tasks](#tasks)
* [Whitelist Checker](#whitelist-checker)
* [NFTs page](#nfts-page)
* [Updates](#updates)
* [App-wide](#app-wide)

---

## Login & auth

### "Login window closed or timed out"

Your browser didn't bounce back into the app within 60 seconds. Causes:

* You closed the browser tab.
* The app's URL handler got unregistered (a quick uninstall/reinstall fixes this on Windows).
* Antivirus blocked the redirect.

**Fix:** click **Login with Discord** again. If it keeps happening, restart the app and try a different browser.

### "This account is registered on another device"

Your account is bound to a different machine. See [First Launch → Device binding](../getting-started/first-launch.md#device-binding). Click **Reset & login on this device** — but remember it's throttled to 1 reset per 24 hours.

### "Your subscription is no longer active"

Your role got removed. Run `/pay` in Discord to reactivate, then log in again. If a renewal URL is configured, the **Renew subscription** button takes you straight there.

### Login button does nothing

Edge case where the login window doesn't open. Try:

1. Restart the app.
2. Make sure your default browser is set in OS settings.
3. On Windows, re-install the app to refresh its URL handler registration.

## Wallets

### Imported wallet shows balance 0 when it shouldn't

The RPC group for that chain is broken or wrong. Go to [RPCs](../core-concepts/rpcs.md), ping the group, and replace any dead endpoints. For EVM, also confirm you've selected the right chain in the Wallets bottom toolbar.

### "Reset Nonce" button — when do I use it?

When a wallet is stuck broadcasting `pending` transactions and the local nonce has drifted from the chain's nonce. Clicking it cancels pending txs by self-broadcasting at higher gas. EVM only.

### "Duplicate" warning when importing

The address (or private key) is already in the group. The duplicate is skipped, the others import normally. Check the toast count to see how many actually came in.

### My private key was imported wrong

You can't "edit" the key on an existing wallet — delete it and re-import.

## RPCs

### All RPCs show offline after a ping

Likely your network is the problem, not the RPCs. Check:

* You're online (the sidebar shows online status).
* No corporate firewall / VPN is blocking outbound 443.
* Try pinging from a browser by visiting the RPC URL — most respond with a JSON-RPC error message even on GET, which is enough to confirm connectivity.

### One RPC keeps showing "unknown"

The 8-second refine-pass timed out — usually means the RPC is sluggish or returns large responses. It's marginal; consider replacing it with something faster.

### Chain mismatch error after pinging

The RPC URL is serving a different chain than the group's chain ID. Either move the RPC to the right group or fix the URL.

## Proxies

### All proxies offline

Same checks as RPCs — confirm you're online, then test a single proxy from a browser-extension proxy manager to confirm the credentials work outside the app.

### Proxy works in test but tasks fail

Test using the **Target** button against the actual platform URL. Many platforms (Cloudflare-fronted ones especially) reject datacenter IPs even when generic test endpoints work. Switch to residential.

### "Authentication required" errors

Proxy credentials are wrong. Edit the proxy and re-enter `user:pass`.

## Captcha

### Test passes but tasks fail captcha

Different captcha types. Your provider key may be valid but doesn't support the captcha challenge type the platform uses (Turnstile vs. hCaptcha vs. reCAPTCHA). Check your provider's dashboard.

### "Insufficient balance" on solver

You ran out. Top up at the provider, or swap to a different provider with credit.

## Tasks

### Task stuck on "running" forever

Either:

* The RPC is silently dropping the broadcast — switch RPCs.
* The network is congested and the tx is stuck pending — increase priority fee, or use **Reset Nonce** on the wallet to cancel.

### All tasks fail with "InsufficientFunds"

The wallet doesn't have enough native token for `mint price + gas`. Top up.

### All tasks fail with "Phase not active" / "Not in allowlist"

The drop hasn't started yet, the WL period ended, or the wallet isn't actually whitelisted. Cross-check with [WL Checker](../features/whitelist-checker.md) and verify the schedule.

### Task succeeds but no NFT in wallet

The tx was confirmed, but the contract logic transferred the token elsewhere (rare — usually a bug in a custom contract or you're looking at the wrong wallet). Check the tx hash on the explorer to confirm the actual recipient.

### Send mode didn't transfer the NFT

* The destination in **Settings → Send NFTs To** is empty or invalid.
* The transfer failed (look for a `transferring` → `failed` row).
* The contract requires approval before transfer — Send mode handles standard transfers but may not work on every exotic contract.

### Tasks scheduled for a Unix timestamp didn't fire

* The app was closed at the timestamp. Schedules require the app to be running.
* The wallet ran out of funds between staging and firing.

### "Edit Gas" only updated some tasks

Edit Gas updates **all tasks in the current group**, not just the selection. If you have multiple groups for the same drop, repeat per group.

## Whitelist Checker

### Check is slow / 429ing

Throttle is intentional (50ms gap, exponential backoff on 429s). For very large groups, break them into smaller checks and run sequentially.

### Phase shows eligible but task says not eligible

* Eligibility was published in the WL Checker but the on-chain proofs aren't synced yet — wait a minute, re-check.
* Different phase: WL Checker shows "Allowlist", task is configured for "Public". Review your task setup.

### Some platforms aren't in the WL Checker

Element, HyperLaunch, and Manifold aren't supported in the Checker — only in [Tasks](../features/tasks.md). Use the platform's own UI to confirm WL, or write a [Lab manifest](../features/laboratory.md).

## NFTs page

### "Detected ERC-1155" but transfers fail

Some ERC-1155 contracts use non-standard transfer functions. The app uses `safeTransferFrom`. If the contract wraps it, you may need to do those transfers manually or via a custom task.

### Range cap of 2000 IDs is too small

Run multiple batches. Pre-cache addresses; the slow part is sending the txs, not the UI.

### Transfers fail with "operator not approved"

Set `setApprovalForAll` on the source wallet first, manually (via an explorer or a Custom task), then retry.

## Updates

### Modal hangs on "Checking..."

10-second timeout. Close, reopen, retry. Network is usually the cause.

### Update Failed with a download error

Your network dropped mid-download. Click **Retry**. If it keeps failing, restart the app and try again — the auto-check will re-prompt on the next 6-hour cycle.

### App didn't relaunch after update

Manually launch it from the Start menu / Applications folder. Your data is fine.

## App-wide

### App is slow / laggy after running for hours

Restart the app. Long-running task tables can accumulate state. (The app has internal optimizations to mitigate this, but a fresh restart never hurts before a big drop.)

### "Loading..." spinner won't go away on a page

Lazy-loaded bundle failed to fetch. Restart the app. If persistent, your install may be corrupt — reinstall.

### My data is gone after reinstalling

The local DB was deleted with the app data folder. If you had Export Data backups, re-import wallets/RPCs/proxies. If you didn't — your wallets are gone unless you have the seed phrases. **Back up the DB or use Export Data regularly.**

---

Still stuck? See the [FAQ](faq.md), or hit Discord support.
