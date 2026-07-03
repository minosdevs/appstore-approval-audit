# 🍎 appstore-approval-audit

**🇬🇧 [English version](README.md)**

**Passe la review Apple du premier coup.**

Un skill [Claude Code](https://claude.com/claude-code) qui audite ton app iOS comme le ferait le reviewer Apple le plus tatillon — et te rend un verdict **GO / NO-GO** avec les fixes exacts, avant que tu cliques « Submit for Review ».

**Marche avec n'importe quel stack iOS** — Swift/SwiftUI natif, Expo/React Native, ou Flutter : il détecte ton stack d'abord, puis sait où regarder dans chacun.

Forgé en shippant une vraie app iOS (IAP + IA) : chaque check vient d'un piège rencontré en vrai, pas de la doc.

## Ce qu'il vérifie

- 💸 **Achats (3.1.1)** — Restore sur *chaque* paywall, prix depuis StoreKit, essai gratuit conforme
- 🔒 **Privacy (5.1.1 / 5.1.2)** — suppression de compte in-app, App Privacy label, divulgation IA, tiers nommés dans la privacy policy
- 🕳️ **Pièges de config** — secrets dans le bundle, export compliance, `UIBackgroundModes` parasites, permissions injustifiées
- 🧭 **Flux que le reviewer teste** — funnels sans issue, reset de mot de passe en build prod, timeouts, comptes démo
- 🏪 **App Store Connect** — abos « Missing Metadata », pages légales en 404, métadonnées malhonnêtes, les 10 dernières minutes avant submit

## Installation

```bash
# global (toutes tes sessions)
git clone https://github.com/minosdevs/appstore-approval-audit ~/.claude/skills/appstore-approval-audit

# ou par projet
git clone https://github.com/minosdevs/appstore-approval-audit .claude/skills/appstore-approval-audit
```

## Usage

Dans Claude Code, à la racine de ton app iOS :

```
/appstore-approval-audit
```

ou dis-lui simplement : *« audite mon app avant la soumission App Store »*.

Tu obtiens un rapport structuré : verdict, findings classés **BLOCKER / HIGH / MEDIUM / LOW** (avec `fichier:ligne` et fix estimé), chemin critique dans l'ordre de déblocage, et la checklist des 10 dernières minutes.

## Structure

```
SKILL.md                       ← le skill (méthode d'audit en 4 passes)
references/
  guidelines-rejets.md         ← les guidelines qui rejettent vraiment, piège par piège
  asc-checklist.md             ← App Store Connect champ par champ + notes reviewer
  rapport-audit.md             ← le format du rapport rendu
```

## Pourquoi

Le premier rejet Apple coûte 24-48 h de review + le moral. La plupart des rejets ne viennent pas du code : pages légales en 404, Restore oublié sur un paywall secondaire, abonnements pas sélectionnés dans la version, label privacy incohérent. Ce skill attrape tout ça **avant** Apple.

---

MIT · fait par [@minosdevs](https://x.com/minosdevs) — je build in public, viens voir.
