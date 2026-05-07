# Laboratory

The Laboratory is where power users add **custom platforms** to the app. A new chain is launching, a new mint pad pops up, an obscure protocol you want to bot — instead of waiting for built-in support, you write a **manifest** (a JSON document describing how to call the platform) and the app treats it like any other platform.

**On this page:**

* [What's a manifest?](#whats-a-manifest)
* [The Laboratory page](#the-laboratory-page)
* [The editor — Edit mode](#the-editor--edit-mode)
* [The editor — Test mode](#the-editor--test-mode)
* [Phase 2 features](#phase-2-features)
* [How custom platforms surface elsewhere](#how-custom-platforms-surface-elsewhere)
* [What's missing today (be honest)](#whats-missing-today-be-honest)
* [Authoring workflow, end-to-end](#authoring-workflow-end-to-end)

---

Lab platforms show up in the [Whitelist Checker](whitelist-checker.md) (if eligibility is defined) and the [Tasks](tasks.md) page (if a transaction step is defined), alongside built-ins.

{% hint style="warning" %}
**This is the deepest feature in the app.** If you're brand new, get comfortable with [Wallets](../core-concepts/wallets.md), [Tasks](tasks.md), and [WL Checker](whitelist-checker.md) first. Manifest authoring assumes you can read API docs, JSON, and basic ABI fragments.
{% endhint %}

## What's a manifest?

A manifest is a JSON document that describes:

1. **Metadata** — platform name, icon, which chains it applies to, how to detect URLs.
2. **Steps** — an ordered list of operations: HTTP calls, data extraction, message signing, contract reads, transaction sends.
3. **Eligibility** — how to compute whether a wallet qualifies for a phase, given the data fetched in the steps.

The manifest engine runs the steps top-to-bottom, threading outputs of one step into the templates of the next. At the end, it has enough data to either render a WL Checker result or build a mint transaction.

## The Laboratory page

Two-pane layout:

* **Left sidebar** — list of saved manifests. Click to open.
* **Right panel** — the editor for the selected manifest, or an empty state when nothing's selected.

Top right: **+ New platform** button.

If you have no manifests and haven't drafted one, the sidebar collapses out and the page shows "Click + New platform to create your first manifest."

### Creating a manifest

1. Click **+ New platform**.
2. A draft row appears in the sidebar with a dashed border and a "DRAFT" badge.
3. The editor opens to the **Metadata tab** with empty defaults.
4. Drafts are unsaved until you click **Save** (or `Ctrl+S`).

### Selecting / deleting / duplicating

* **Select** — click a row in the sidebar.
* **Duplicate** — duplicate icon on the row. Clones with `(copy)` appended; new manifest gets a fresh UUID.
* **Delete** — context menu / delete icon → confirmation modal. Cannot be undone.

## The editor — Edit mode

The editor has two modes: **Edit** (default) and **Test**. Toggle at the top.

In Edit mode, you have four tabs:

### 1. Metadata

* **Name** — required. Shows in the platform pickers across the app.
* **Icon** — pick from emoji, paste a data URL, or upload an image.
* **Chain IDs** — multi-select for which EVM chains this platform applies to. You can scope a manifest to Ethereum-only, or to several chains.
* **URL Detection** — hostnames and regex patterns. Lets the app auto-route a pasted URL like `https://mypad.xyz/drops/cool-drop` to this manifest, capturing `cool-drop` as the slug.
* **Custom User Inputs** — declare extra form fields the user supplies at check/mint time (e.g., a referral code).
* **Network Hosts** — the list of hosts your manifest will hit. Used internally for rate-limit budgets.

### 2. Steps

A reorderable list. Click **Add Step** to insert one of:

| Step | What it does |
|---|---|
| `http_request` | Makes an HTTP call. Templated URL, method, headers, body. Optionally extracts JSON paths into context. |
| `extract` | Pulls data from a previous step's output (or from inputs) into a named context variable using JSONPath. |
| `sign_message` | Signs an EIP-191 or EIP-712 message with the active wallet. **Phase 2 feature** — see below. |
| `contract_call` | Read-only contract call (no tx, no gas). **Phase 2.** |
| `send_transaction` | Sends a transaction. The mint, basically. **Phase 2.** |

Steps are collapsible cards with inline validation. If you reference a template variable that doesn't exist (e.g., `{{response.foo}}` when no step set `response`), you'll see an amber warning right on the card.

![Laboratory editor on the Steps tab — an `http_request` step expanded with URL, headers, JSON body, timeout, expected status, and retry config.](../images/laboratory.png)

#### Templates

Throughout the manifest, fields can use `{{...}}` template syntax to reference:

* User inputs — `{{slug}}`, `{{wallet}}`
* Step outputs — `{{response.phases[0].id}}`
* Captures from URL detection — `{{captures.collectionSlug}}`

The renderer resolves these at runtime per wallet.

### 3. Eligibility

Two strategies for what makes a wallet eligible:

* **`singlePhase`** — one hardcoded phase. You define `isEligible` (a template that evaluates true/false), `maxMintableByWallet`, `pricePerToken`, etc.
* **`phaseList`** — multiple phases coming from a step output (e.g., the response of an HTTP call). For each phase, you define eligibility and limits.

Manifests with a populated eligibility section automatically show up as "checkable" in the [WL Checker](whitelist-checker.md).

### 4. Raw JSON

The full manifest as JSON, in a code editor with syntax highlighting and validation. Two-way sync with the form tabs:

* Edits in the form update the JSON.
* Edits in the JSON, once valid, sync back to the form when you switch tabs.

If the JSON is invalid, you'll see errors inline (red squigglies) and a banner above the editor.

This is also how you **export and import** manifests today: copy the JSON out, paste it elsewhere, or paste a manifest someone shared with you. There's no built-in import/export button (yet).

## Editor header

Across the top of the editor:

* **Unsaved badge** — shows when the current state differs from the last saved version.
* **Validation chip** — shows a count of validation errors. Click to expand a per-tab breakdown.
* **Enable Phase 2 features** — upgrades `schemaVersion` from 1 to 2, unlocking `sign_message`, `contract_call`, and `send_transaction`. Once upgraded, you can't downgrade. Read the [Phase 2 caveats](#phase-2-features) below before clicking.
* **Save** — disabled until the manifest is dirty, valid, and has no local errors. `Ctrl+S` works too.

## The editor — Test mode

Test mode is the dry-run sandbox. Two columns.

### Left: parameters

* **Slug or URL** — what you'd paste in the WL Checker. The URL detection regex from your metadata extracts the captures.
* **Custom inputs** — fields you declared in metadata.
* **Wallet** — pick a saved wallet from your wallet groups. Shows balance.
* **Chain** — which EVM chain to run on.
* **Run** / **Stop** buttons.

### Right: execution log

As the dry-run executes, every step appears as a card:

* Step name and type
* Rendered template (the URL/body after `{{...}}` substitution)
* Raw response (truncated)
* Extracted output (JSON preview)
* Duration and pass/fail status

If the eligibility section is populated, you also get an **Eligibility Preview** at the bottom showing the wallet's computed result (eligible: yes/no, max mintable, phase details).

### Create Task button

In Test mode, after a successful dry-run, you'll see a **Create Task** button if:

* The manifest has at least one `send_transaction` step (so it's mint-capable, not just a checker).
* You picked a wallet group.

Clicking it opens the standard task creation form, pre-populated with the dry-run context (slug, wallet, chain, manifest config). The result is a task in the [Tasks](tasks.md) page that uses your custom manifest as its platform.

## Phase 2 features

Manifests start at `schemaVersion: 1`. That version supports:
* `http_request`, `extract`
* `singlePhase` and `phaseList` eligibility

Click **Enable Phase 2 features** to upgrade to `schemaVersion: 2`. This unlocks:
* `sign_message` — EIP-191 personal_sign or EIP-712 typed-data signing with a saved wallet
* `contract_call` — read-only contract calls with templated args
* `send_transaction` — actual transaction broadcast (the mint)

Once you upgrade, the schema can't be reverted. Phase 1 manifests will run forever as v1; only upgrade when you actually need to mint or sign from a manifest.

## How custom platforms surface elsewhere

* **WL Checker** — manifests with eligibility templates appear as platform tabs. URL detection rules merge with built-in detection, so pasting a known URL routes correctly.
* **Tasks** — manifests with `send_transaction` steps appear as a platform option in the group create modal (under your manifest's name).
* **No registry, no sharing.** Manifests live in your local DB. Use the Raw JSON tab to share them as text.

## What's missing today (be honest)

* **No file import/export buttons.** Use the Raw JSON tab.
* **No public manifest gallery.** Sharing is manual, in Discord.
* **No standalone "WL-only" publish toggle** — the same manifest serves both checker and task; the engine decides based on what steps are populated.
* **EVM-only.** Lab manifests don't yet support Solana, Sui, Aptos, or Bitcoin platforms — Phase 2 architecture work for non-EVM chains is on the roadmap.

## Authoring workflow, end-to-end

A typical first manifest:

1. **+ New platform.** Name it. Pick a chain ID. Add an icon if you want.
2. **Metadata → URL Detection** — add the platform's hostname and a regex with a slug capture group.
3. **Steps → Add http_request** — configure GET to the platform's drop API, like `https://api.platform.xyz/drops/{{slug}}`.
4. **Add an extract step** to pull `phases` from the response into context: `{{response.phases}}`.
5. **Eligibility → phaseList** — point at `{{response.phases}}`. For each phase, define `isEligible` (often `{{phase.allowlist}}` includes `{{wallet}}`).
6. **Save.**
7. **Switch to Test mode.** Paste a real drop URL, pick a wallet, hit **Run**.
8. Read the log. Fix any rendering issues, missing fields, etc.
9. Once green, the manifest shows up in the WL Checker for that platform.
10. (Optional) Enable Phase 2 → add `send_transaction` → test → **Create Task**.

That's it. You've built your own platform.

---

Next: [Settings](../settings/settings.md).
