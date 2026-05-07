# Installation

Degens ships as a native desktop app for **Windows** and **macOS**. The installer is distributed through the Discord server — there's no public download link, by design. If you don't have access yet, that's a subscription / role thing, not an installation thing.

## System requirements

| Platform | Minimum |
|---|---|
| **Windows** | Windows 10 or 11, x64 |
| **macOS** | macOS 10.15 (Catalina) or later |
| **RAM** | 4 GB free (8 GB+ recommended if you're running 100+ tasks) |
| **Disk** | ~250 MB for the app, plus whatever your local data grows to |
| **Network** | A stable connection. Mints fail loudly when the network blinks. |

The app runs in a 1920×1080 window by default and is resizable.

## Where to get the installer

1. Join the Degens Discord server.
2. Run `/pay` to activate your subscription if you haven't yet.
3. The installer link lives in the gated downloads channel that unlocks once your role is active.

If you ever lose the installer, you don't need to redownload it before the next launch — auto-update handles that. See [Updates](../settings/updates.md).

## Installing on Windows

1. Download the Windows installer from the Discord channel.
2. Double-click to run. Windows SmartScreen may yell at you the first time — click **More info → Run anyway**. The app is signed but unfamiliar to SmartScreen until enough installs warm up its reputation.
3. Pick an install location, finish the wizard.
4. The app launches automatically the first time. You can also pin it to the taskbar from there.

## Installing on macOS

1. Download the macOS installer from the Discord channel.
2. Open the installer and drag **Degens.app** into the **Applications** folder.
3. The first time you launch it, macOS will probably block it: **"Degens cannot be opened because it is from an unidentified developer."**
4. Open **System Settings → Privacy & Security**, scroll to the bottom, and click **Open Anyway** next to the Degens warning.
5. Re-launch the app.

## What gets installed where

The app stores everything locally in your user profile:

* **Windows:** `%APPDATA%\com.degens\`
* **macOS:** `~/Library/Application Support/com.degens/`

Don't poke around in there unless you know what you're doing — that folder is where your encrypted wallets live. **Back it up if you care about not re-importing wallets later.**

## Updating

Once installed, the app checks for updates automatically a few seconds after every login (throttled to once every 6 hours). Updates ship as silent corner banners — you'll never get interrupted mid-mint. See [Updates](../settings/updates.md) for the full flow and how to trigger a manual check.

## Uninstalling

Standard uninstall through Windows "Add or Remove Programs" or by dragging the app to Trash on macOS. **The local data folder above is not removed by default** — delete it manually if you want a fully clean wipe (and you've backed up your wallets first).

---

Next up: [First Launch & Login](first-launch.md).
