# Captcha Keys

Some platforms (and many WL endpoints) gate their flows behind a captcha — Cloudflare Turnstile, hCaptcha, reCAPTCHA. Solving them in-app at scale isn't realistic, so Degens uses third-party solver APIs. The Captcha Keys page is where you store the API keys those services hand you.

## Supported providers

| Provider | Notes |
|---|---|
| **CapMonster** | Default. Cheapest for hCaptcha + Turnstile in most cases. |
| **2Captcha** | Old reliable. Solid for reCAPTCHA. |
| **CapSolver** | Fast, decent prices, modern API. |

You can store multiple keys for the same provider — useful if you want to split balance across accounts, or load-balance.

## Adding a key

1. Sign up with your chosen provider and grab your API key from their dashboard.
2. In Degens, click **Add Key** in the bottom toolbar.
3. Fill in:
   * **Name** — your label (e.g., "CapMonster main", "2Captcha backup").
   * **API Key** — paste it.
   * **Provider** — pick from the dropdown.
4. Save.

The key is stored locally — never sent anywhere except to the provider it belongs to.

## Testing a key

Each row has a test icon, and you can bulk-test selected keys with **Test Selected** in the toolbar. Test results surface in a toast — success means the provider accepted the key.

{% hint style="info" %}
Some providers also expose balance via their API. If shown, the table includes a balance column updated when you test. If you don't see balance, your provider doesn't return it — check the provider dashboard directly.
{% endhint %}

## Editing a key

Click the edit icon to rename or rotate the key. The key is hidden by default; click the eye icon to reveal, or the copy icon to copy without revealing.

## Bulk operations

* **Test Selected (N)** — re-test selected keys.
* **Delete (N)** — bulk delete with confirmation.

## How tasks pick a key

When a task or WL check needs a captcha solved:

1. The task's configured provider is checked.
2. The app picks an available key for that provider — usually round-robin.
3. The captcha challenge is submitted to the solver.
4. The solver returns a token, which the app injects into the platform request.

You can store multiple keys per provider; the rotation spreads load and helps if one key is over its rate limit.

## Tips

* **Top up before big drops.** Solver balances drain fast on high-volume mints. CapMonster typically charges per solve; pre-fund.
* **Different platforms use different captchas.** Cloudflare-protected sites (Mintify, Scatter) lean on Turnstile. Older sites might use reCAPTCHA. Make sure your provider supports the captcha type — most modern ones support all three.
* **Keep at least two providers configured** as a fallback. If CapMonster has an outage during your drop, swapping to 2Captcha is one click.
* **Don't share screenshots that include API keys.** The mask toggle helps, but the safest path is to never reveal them on screen at all.

---

That covers the building blocks. Now: features. Start with the [Dashboard](../features/dashboard.md).
