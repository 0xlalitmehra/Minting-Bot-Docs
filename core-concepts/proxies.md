# Proxies

Proxies route your outbound HTTP requests through a different IP. Useful when a platform rate-limits per-IP, when you're running 50 wallets on one drop, or when the platform geofences your real IP. The Proxies page is where you keep them.

## Mental model

* Proxies live inside **groups**.
* Groups aren't chain-specific — a proxy is a proxy.
* Tasks reference proxy groups, not individual proxies. The runner rotates through the group as it works.

## Supported formats

When you click **Add Proxies**, paste one proxy per line in any of these formats:

```
123.45.67.89:8080
123.45.67.89:8080:user:password
My Residential 1,123.45.67.89:8080
My Residential 2,123.45.67.89:8080:user:password
```

* `ip:port` → unauthenticated proxy.
* `ip:port:user:pass` → authenticated proxy (most paid services).
* Optional `name,` prefix to label the proxy. Otherwise, names auto-increment ("Proxy 1", "Proxy 2", ...).

> **HTTP/HTTPS proxies, not full URLs.** Don't paste `https://` URLs — just the host and port. SOCKS5 is supported by underlying clients but not officially formatted in the input UI.

You can also bulk-import from a `.txt` file with the file picker on the Add modal.

## Pinging proxies

The proxy table shows latency and status (online / offline / unknown), just like RPCs.

* **Single proxy** — ping icon on the row.
* **Selected proxies** — **Ping (N)** in the toolbar.

By default, the ping hits the app's built-in test endpoint. If you want to test against a specific URL — say, the platform you're about to mint on — click **Target** in the toolbar to set a custom test URL. From then on, **Ping** will go through each selected proxy to that URL and report success + latency.

This is genuinely useful for proxy services that work fine on a generic test but fail on, say, OpenSea. Test against the real target.

## Group management

Same drill as wallets and RPCs:

* **Create group** at the top of the page.
* Group strip lets you switch between groups; rename / delete from the tab.
* Drag tabs to reorder.

## Bulk operations

* **Ping (N)** — re-test.
* **Delete (N)** — remove with confirmation.
* The 👁️ mask toggle redacts the IP / port columns for screen-share safety.

## Editing a proxy

Click the edit icon to rename, replace the address, or update credentials.

## Tips that save mints

* **Residential > datacenter** for most NFT platforms. Datacenter ranges are widely blacklisted (Cloudflare, Akamai). Residential and ISP proxies are slower but get through.
* **Don't share a proxy across 50 wallets on the same task.** Spread them out — the rotation is per-task, so a group with 10 proxies serving 50 wallets is doing 5 wallets per proxy and that often gets fingerprinted as a botnet.
* **Test against the actual mint URL** before drop time, not just a default ping target. Cloudflare may bounce you on `nfts2me.com` while letting `httpbin.org` through.
* **Latency outliers in your group** mean either the proxy is dying or it's geographically far from the platform's edge. Replace them.
* You don't *need* a proxy for everything. For a 1–5 wallet mint on a small platform, your real IP is fine. Proxies are for scale.

---

Next: [Captcha Keys](captcha.md).
