# First Launch & Login

When you launch Degens for the first time, you'll see a brief loading spinner while the app provisions a unique device ID, followed by the **Login screen**.

## Logging in

There's exactly one way in: **Login with Discord.**

1. Click **Login with Discord** on the login card.
2. Your default browser opens an OAuth consent page hosted at the Degens auth worker.
3. Approve the consent.
4. The browser redirects back into the app via a `degensapp://` deep link.
5. The app exchanges the code for a session token, validates your subscription role server-side, and drops you on the **Dashboard**.

If your browser doesn't bounce back into the app within ~60 seconds, you'll be returned to the login screen with a "Login window closed or timed out" message. Just click **Login with Discord** again.

> **No password, no seed phrase, no private key needed to log in.** Your access is tied to your Discord account and your active subscription role in the server.

## Device binding

The first successful login binds your account to **this device** (machine-level device ID, stored locally). You can only be logged in on one device at a time — that's intentional, to prevent account sharing.

### "This account is registered on another device"

If you see this on login, it means your account is currently bound to a different machine. You have two options:

1. **Log out on the original machine** and try again.
2. Click the **Reset & login on this device** button (amber, appears only when this error is shown). This unbinds the previous device and binds the new one.

> **Heads up:** the reset is throttled to **once per 24 hours** server-side. If you reset and then change your mind, you have to wait it out. Don't hop between devices casually.

## Subscription expired ("Your subscription is no longer active")

If your role is revoked while you're logged in (subscription expired, refunded, manually removed), the next session validation will sign you out and show the renewal screen.

* If a renewal URL is configured (Whop, Lemon Squeezy, etc.), you'll see a **Renew subscription** button that opens the checkout in your browser.
* Otherwise — and this is the default for Suby billing — you'll see instructions to run `/pay` from any channel in the Discord server.

Once your role is restored, log in again normally.

## Logging out

To log out manually, go to **Settings → Account → Log Out**. This clears the device binding too, so the next login on this machine is a clean bind.

## What's stored where

* Your **JWT session token** lives in the app's local secure store (managed by Tauri).
* Your **device ID** is in `localStorage` under `degens.deviceId`.
* Your **Discord username + avatar** is cached for the Settings → Account card.
* **No wallet keys, no API keys, no manifests** are sent during login. That all stays local.

---

Now that you're in: [Interface Tour](interface-tour.md).
