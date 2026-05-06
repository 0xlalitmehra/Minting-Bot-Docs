# Interface Tour

The app is a single window split into three regions:

1. **Sidebar** (left) — navigation between the 10 main pages.
2. **Top bar** — clock, network status, window controls.
3. **Main panel** — whatever page you've selected.

## The sidebar

The sidebar is the only navigation. There's no router and no URLs — just click an icon to switch pages. The sidebar collapses with the chevron at the top to reclaim screen space; collapsed mode shows icons only.

Order, top to bottom:

| Icon | Page | What it's for |
|---|---|---|
| 🏠 | **Dashboard** | Quick utilities (gas, hex, PnL, unix) |
| ✅ | **Tasks** | Run mint automations |
| 👛 | **Wallets** | Manage wallet groups across chains |
| ⚡ | **RPCs** | Manage RPC endpoint groups |
| 📡 | **Proxies** | Manage proxy lists |
| 🛡️ | **Captcha APIs** | Store API keys for captcha solvers |
| 🖼️ | **NFTs** | Bulk consolidate/disperse NFT holdings |
| 📋 | **WL Checker** | Bulk-check whitelist eligibility |
| 🧪 | **Laboratory** | Author custom platform manifests |
| ⚙️ | **Settings** | Preferences, API keys, account |

The sidebar also shows the live **Ethereum gas tracker** at the bottom — three cards (Normal / Fast / Instant) that update every few seconds. Plug in an Alchemy key in Settings to avoid public-RPC rate limits.

## The top bar

Across the top of the window:

* **Window controls** (left or right depending on platform) — minimize, maximize, close. The window is custom-decorated (no native title bar), so these buttons are part of the app.
* **Drag handle** — the empty space along the top bar is draggable; click and hold to move the window.
* **Global clock** — your local time and a UTC clock side-by-side. Useful when a drop announces in UTC.

## The main panel

Whatever page you're on renders here. A few app-wide behaviors worth knowing:

* **Lazy-loaded pages.** The first time you visit a page (Wallets, Tasks, etc.) it'll show a brief "Loading..." spinner while the page bundle downloads. Once loaded, it stays in memory.
* **No right-click menu.** Browser-style context menus are disabled in production builds — click-driven UI only.
* **Skip-to-content link.** Press `Tab` once after launch and you'll see a "Skip to content" link, mostly for accessibility.

## Keyboard

The app doesn't define many global shortcuts, but a few useful ones exist:

| Shortcut | What it does |
|---|---|
| `Ctrl` + `+` | Zoom in (UI scale) |
| `Ctrl` + `-` | Zoom out |
| `Ctrl` + `0` | Reset zoom to 100% |
| `Ctrl` + `S` (in Laboratory) | Save the current manifest |

Most other interactions are click/select/drag.

## A note on "masked" toggles

Several pages (Wallets, RPCs, Proxies, Tasks) have a **mask** toggle in the table header that hides sensitive columns (private addresses, RPC URLs, gas prices) behind `*****`. Use it before screen-sharing. The data isn't going anywhere — you're just hiding it from the screen.

---

You're set. Next, learn how the building blocks work: [Wallets](../core-concepts/wallets.md).
