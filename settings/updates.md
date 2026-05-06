# Updates

Degens auto-updates itself. You don't need to redownload installers — the app pulls signed update bundles from the cloud and installs them in place.

## How auto-update works

1. About **8 seconds after you log in**, the app does a background check.
2. The check is **throttled to once every 6 hours** so it's not constant.
3. **Dev builds skip the check entirely.**
4. If a newer version is available — and you haven't explicitly skipped it — a banner appears in the bottom-right corner.

The check is silent on failure. If the update server is down or your network blinks, the app just continues — you'll never see a "couldn't check for updates" error.

## The update banner

A small, non-blocking corner toast. It shows:

* The new version number.
* The first couple of lines of release notes.
* Three buttons:

| Button | What it does |
|---|---|
| **Update now** | Opens the full Update modal in install mode. |
| **Later** | Dismisses the banner. You'll see it again on the next 6-hour check (or next login, whichever comes first). |
| **Skip this** | Persistently skips this specific version. You won't be re-prompted unless an even newer version ships. |

The banner is non-blocking — you can keep working. It's not a modal.

## The update modal

The bigger modal opens from two places:

* **From the banner** — clicking "Update now" jumps straight to install mode.
* **From Settings → About & Updates → Check for Updates** — manual check.

### Manual check flow

1. Click **Check for Updates** in Settings.
2. Modal opens with "Checking for updates..." spinner.
3. After up to 10 seconds:
   * **Up to date** — "You're up to date" message. Auto-closes after 2 seconds.
   * **Update available** — version, full release notes, **Update Now** and **Later** buttons.
   * **Timeout** — modal closes silently. Try again.

### Install flow

When you click **Update Now**:

1. The app downloads the update bundle. A progress bar shows 0–100%.
2. After download, a brief "Update Complete" message.
3. **The app auto-relaunches after 1.5 seconds.** No prompt, no choice.

So **save your work and stop any running tasks before clicking Update Now.** There's no "I'll restart later" path once the download finishes — that's intentional, to make sure you're never running a half-updated app.

### If the install fails

If the download fails or the install errors out:

* You'll see "Update Failed" in red with the error message.
* **Skip** dismisses; the app continues on the old version.
* **Retry** starts the whole download over.

## Frequency, throttling, what to expect

* **Background check:** every 6 hours, silent.
* **Manual check:** any time, from Settings.
* **Skip is per-version.** If you skip v2.1.0 and v2.2.0 ships, you get the banner for v2.2.0.
* **Updates are signed.** Tauri's updater verifies the signature against the bundled public key before installing. A tampered update will be rejected.

## Where updates come from

Updates are hosted on a CDN bucket (`pub-...r2.dev/latest.json`) controlled by the Degens team. The app fetches that JSON, compares your version to the latest, and downloads the appropriate platform bundle (Windows `.exe`/`.msi` or macOS `.dmg`).

You can see the current bundled endpoint by reading `src-tauri/tauri.conf.json` if you're poking around — but you don't need to configure anything.

## Tips

* **Don't update right before a drop.** Auto-relaunch will kill any running tasks. Update during downtime.
* **Skip rarely.** Updates often include critical fixes for platform changes (e.g., "OpenSea changed their phase API"). Staying behind a major version means broken minting.
* **If the update modal hangs**, close it and try again from Settings. Network blips are usually the cause.

---

Next: [Troubleshooting](../help/troubleshooting.md).
