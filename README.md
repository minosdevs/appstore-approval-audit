# 🍎 appstore-approval-audit

**🇫🇷 [Version française](README.fr.md)**

**Pass Apple review on the first try.**

A [Claude Code](https://claude.com/claude-code) skill that audits your iOS app like the pickiest Apple reviewer would — and gives you a **GO / NO-GO** verdict with exact fixes, before you hit "Submit for Review".

**Works with any iOS stack** — native Swift/SwiftUI, Expo/React Native, or Flutter: it detects your stack first, then knows where to look in each one.

Forged while shipping a real iOS app (IAP + AI): every check comes from a trap hit in production, not from the docs.

## What it checks

- 💸 **Purchases (3.1.1)** — Restore on *every* paywall, prices from StoreKit, compliant free trial
- 🔒 **Privacy (5.1.1 / 5.1.2)** — in-app account deletion, App Privacy label, AI disclosure, third parties named in the privacy policy
- 🕳️ **Config traps** — secrets in the bundle, export compliance, stray `UIBackgroundModes`, unjustified permissions
- 🧭 **Flows the reviewer actually tests** — dead-end funnels, password reset in a production build, timeouts, demo accounts
- 🏪 **App Store Connect** — "Missing Metadata" subscriptions, 404 legal pages, dishonest metadata, the last 10 minutes before submit

## Install

```bash
# global (all your sessions)
git clone https://github.com/minosdevs/appstore-approval-audit ~/.claude/skills/appstore-approval-audit

# or per project
git clone https://github.com/minosdevs/appstore-approval-audit .claude/skills/appstore-approval-audit
```

## Usage

In Claude Code, at the root of your iOS app:

```
/appstore-approval-audit
```

or just tell it: *"audit my app before the App Store submission"*.

You get a structured report: verdict, findings ranked **BLOCKER / HIGH / MEDIUM / LOW** (with `file:line` and estimated fix), the critical path in unblocking order, and the last-10-minutes checklist.

## Structure

```
SKILL.md                       ← the skill (4-pass audit method)
references/
  guidelines-rejets.md         ← the guidelines that actually reject, trap by trap
  asc-checklist.md             ← App Store Connect field by field + reviewer notes template
  rapport-audit.md             ← the report output format
```

## Why

A first Apple rejection costs 24-48h of review time — plus your momentum. Most rejections don't come from code: legal pages returning 404, Restore missing on a secondary paywall, subscriptions not selected with the version, inconsistent privacy label. This skill catches all of it **before** Apple does.

---

MIT · built by [@minosdevs](https://x.com/minosdevs) — building in public, come say hi.
