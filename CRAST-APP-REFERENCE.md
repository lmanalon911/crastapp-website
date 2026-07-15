# Crast App — Full Reference

A single source-of-truth document pulling together everything that exists about Crast App
today, for use as raw material when writing the actual website copy. Not itself meant to be
published verbatim — some of this is internal/technical detail, not marketing language.

---

## 1. What it is

Crast App is a Windows desktop utility that runs quietly in the background and simulates
ongoing computer activity — mouse movement, scrolling, keyboard taps, browser tab switching,
and app switching — tuned to read as genuine activity to a per-second, Hubstaff-style
productivity tracker (the kind that measures `active_seconds / 600` over rolling 10-minute
windows, separately for mouse and keyboard).

It's aimed at people whose work is monitored by this style of activity tracker and who want
their measured activity level to sit at a specific, controllable target rather than reflect
literal second-by-second input.

- Runs hidden, no visible window most of the time — controlled entirely by global hotkeys.
- Shows a system tray icon for status and a right-click fallback menu.
- Auto-launches at Windows login once installed.

## 2. Key features

- **Three auto intensity levels** (Low / Mid / High) — one tap of a hotkey and it runs at a
  fixed target activity band, drifting naturally within that band so it never looks
  perfectly flat.
- **Manual mixer mode** — a dedicated control-panel window with sliders for exact Mouse %,
  Keyboard %, and Overall % control, a Constant/Range toggle, an adjustable variation band,
  and its own adjustable browser tab-switch and app-switch intervals. For users who want to
  dial in an exact number rather than use a preset.
- **Self-healing browser tab-switching** — advances the active tab in real, multi-tab browser
  windows (Chrome, Edge, Brave, Opera, Vivaldi, Firefox) on an interval, with a fallback
  detection method so a future browser UI update can't silently break it.
- **App switching** — independently rotates which open application is in the foreground on
  its own interval, restoring it first if it was minimized. Unlike tab-switching's brief
  flash, this stays on each app for the full interval.
- **Persistent settings** — Manual mode's slider values are saved and restored automatically,
  including across a reboot.
- **Licensed / commercial product** — key-based activation, device-limit enforcement,
  in-app "Check for Update" and "Report Issue" features.

## 3. The three auto levels + Manual mode

All three auto levels and Manual mode share one proven underlying engine (a per-second,
run-based mouse-activity model with independently-firing keyboard activity), just aimed at
different targets:

| Mode | Hotkey | Overall activity target | Mouse center | Keyboard center |
|---|---|---|---|---|
| **Low** | `Ctrl+Alt+1` | ~45% | 37% (±10) | 13% (±10) |
| **Mid** | `Ctrl+Alt+2` | ~65% | 51% (±10) | 29% (±10) |
| **High** | `Ctrl+Alt+3` | ~85% | 72% (±10) | 46% (±10) |
| **Manual** | `Ctrl+Alt+H` | whatever you set | your slider | your slider (independent) |

- Only one mode runs at a time. Pressing the *active* mode's hotkey again turns it off;
  pressing a *different* mode's hotkey switches directly.
- Each level redraws its exact numbers once every aligned 10-minute block, nudged
  ±10 percentage points around its center, so consecutive windows are never identical or a
  flat line — but the center never drifts, so long-run averages stay predictable.
- `Ctrl+Alt+G` hides/shows the Manual mixer window: hiding arms a **10-second grace period**
  before movement actually starts (time to step away from the mouse), showing it pauses
  everything instantly for adjusting.
- Right-clicking the tray icon offers the same mode choices as a mouse-driven fallback.

## 4. Browser tab-switching (all modes)

Every mode advances the active tab in the user's real, multi-tab browser windows — Chrome,
Edge, Brave, Opera/Opera GX, Vivaldi, and Firefox — on an interval. In the auto levels this is
a fixed interval that scales with intensity (Low 40s, Mid 25s, High 15s); in Manual mode it's
fully user-adjustable, including fully off. It only targets genuine browser windows with a
tab strip — not browser-based app/PWA windows, and not other Chromium-based non-browser apps
(e.g. Electron apps like Claude desktop). The browser briefly becomes the foreground window
when a tab switches (an unavoidable side effect of how each browser exposes tab selection),
then focus returns to whatever the user was doing.

## 4.1 App-switching (all modes)

A second, independent feature: on its own interval (auto levels scale the same way as browser
tab-switching — Low 75s, Mid 45s, High 25s; user-adjustable, including fully off, in Manual
mode), Crast brings the next real open application window to the foreground — restoring it
first if it was minimized — and leaves it focused until the next interval. This rotates
through everything the user has open, not just browsers, so it reads as genuinely switching
between applications rather than a brief flash.

## 4.2 Constant/Range applies to both switch intervals too (Manual mode)

Manual mode's existing Constant/Range toggle and Variation slider (see §2) now also govern the
browser tab-switch and app-switch intervals, not just Mouse/Keyboard. Constant holds each
interval at its exact slider value; Range redraws a fresh value, every time it fires, within
+/- Variation% of that value (a relative percentage of the seconds value). Auto mode's
per-level intervals above are fixed constants and are never affected by this toggle.

## 5. System requirements & installation

- Windows 10/11. Everything it needs (Windows PowerShell 5.1, .NET WinForms/UI Automation)
  is already built into Windows — no separate runtime install.
- No admin rights required.
- Ships as a small installer (`Install-CrastApp.bat` / `.ps1` + a compiled executable).
  Internally the compiled binary uses a generic filename ("DesktopUtility.exe") rather than
  a "Crast"-branded one, for discretion on a monitored work machine — an implementation
  detail, not something that needs to appear on the website.
- Auto-adds itself to Windows startup so it's ready (off, not yet activated to a level) at
  every login.

## 6. Licensing & commercial model

- **Key-based activation.** A purchased license key activates the software on a
  fingerprinted device; validity is checked against a live backend at first run and
  periodically after.
- **Device tiers:** Single (1 device), Dual (2 devices), Triple (3 devices) — the license
  key format encodes which tier it belongs to.
- **Free updates for life** — a purchased license entitles the buyer to every future update at
  no extra cost, for as long as they own the license; there's no separate maintenance/renewal
  fee. **In-app "Check for Update"** checks a version endpoint and can self-download/swap to
  the latest build without the customer needing to reinstall from scratch.
- **In-app "Report Issue"** — lets a customer send a support request without leaving the app.
- **No refunds** except where law requires it — purchase implies the buyer has evaluated the
  software already (per EULA §10).
- **Support channel:** in-app Report Issue, or **support@crastapp.com**.
- Full legal terms: see `CrastApp-Licensing\EULA.md` (also summarized in §8 below) — governed
  by Philippine law.

## 7. Version / product history

- The app was originally built and shipped internally under the working name **"Dreamer
  Mode."** It was renamed to **"Crast App"** on 2026-07-13 — branding/text-only change, no
  behavior difference. (Not necessarily something to mention publicly; noted here only for
  historical accuracy.)
- **Version 1** — first fully-working build: Mid + Low auto levels only, no manual control.
- **Version 2** — added the Manual mixer window (sliders, Constant/Range toggle, adjustable
  variation, adjustable tab-switch interval), persisted settings.
- **Version 3 (current)** — unified all three auto levels (reintroducing a third, **High**,
  level) onto one shared, more accurate engine; moved the auto-level hotkeys to
  `Ctrl+Alt+1/2/3`; added a tabbed Manual/Auto interface to the mixer window with one-click
  Start buttons on each tab. Later in the same version: broadened tab-switching from
  Chrome-only to every major browser, and added the independent app-switching feature (see
  §2 and §4.1).
- The commercial layer (licensing, updates, installer, generic-exe packaging) was built as a
  separate track on top of V3 to turn it into a sellable product.

## 8. Legal summary (End User License Agreement)

Full text lives at `CrastApp-Licensing\EULA.md`; key points:

- Software is **licensed, not sold** — no ownership transfers to the buyer.
- License covers a set number of devices per the tier purchased (1 / 2 / 3); keys may not be
  shared, resold, or published; reverse engineering is prohibited.
- Provided **"as is"**, no warranty, liability capped at the amount paid.
- Purchases are **final** (no refunds) except where consumer law requires otherwise.
- Governed by the laws of the **Republic of the Philippines**.
- Contact for legal/support questions: **support@crastapp.com**.

## 9. Honest limitations (already documented internally — useful for setting expectations)

- Manual mode's sliders are raw activity fractions the user calibrates by watching their own
  tracker, not pre-calibrated tracker percentages.
- Randomized "Range" variation is genuinely random, so it can occasionally land close to the
  previous block's value — expected statistical behavior, not a bug.
- Tab-switching necessarily steals foreground focus for a brief moment; no way around that
  short of a browser extension. App-switching goes further and deliberately holds foreground
  focus on each app for its full interval, and will restore a minimized window to do so.
- Firefox tab-switching hasn't been independently verified (it uses a different underlying UI
  toolkit than the Chromium browsers) — it fails safe if unsupported, simply skipping Firefox
  windows rather than erroring.
- Requires a real, unlocked, logged-in Windows desktop session (depends on global hotkeys +
  UI Automation).

## 9.1 Competitive positioning: named trackers + LazyWork comparison (2026-07-15)
User explicitly authorized naming specific activity trackers (Hubstaff, Time Doctor, ActivTrak,
DeskTime) and a direct competitor (LazyWork) in public site copy — a deliberate reversal of the
earlier no-brand-names policy, made after researching LazyWork as a real, validated competitor
in the same niche. Live page: `alternatives.html` ("Crast vs. Mouse Jigglers & LazyWork"),
linked from main nav ("Compare"), the footer, and the FAQ's mouse-jiggler answer.
- **Verified facts used in the comparison** (don't restate stale numbers without re-checking):
  LazyWork is $9.99/month recurring, after a 7-day free trial, compatible with Hubstaff, Time
  Doctor, Upwork and other trackers, simulates mouse/keyboard/app activity. Sourced from
  LazyWork's own public pricing page as of 2026-07-15 — re-verify before reusing if this page is
  ever revisited, pricing pages change.
- Crast's own claims on that page (one-time price, free updates for life, 100% local) were
  already true/established elsewhere on the site, not new claims invented for this comparison.
- Page includes an explicit no-affiliation/trademark disclaimer for every named tracker and
  LazyWork — standard, keep this if the page is ever edited.
- **Why:** the earlier SEO pass found LazyWork ranking for exactly the keyword phrases Crast
  should target ("mouse jiggler alternative," etc.) — proof those are real, searched terms, not
  guesses. Comparison/alternative pages are a standard, effective SaaS content-marketing pattern
  for capturing that search intent.

## 10. Site / infrastructure (for reference, not page content)

- Production domain: **crastapp.com** (also serves `www.crastapp.com`), HTTPS enforced,
  hosted on GitHub Pages.
- Source repo: `github.com/lmanalon911/crastapp-website` (public).
- Current live page: a placeholder reading "Crast is coming soon."
