# App Store Connect — checklist champ par champ

Trois pages distinctes dans appstoreconnect.apple.com → My Apps → ton app.

## A. App Information (section *General*)
| Champ | Valeur type | Piège |
|---|---|---|
| Name (30) | marque + mots-clés ASO forts | le champ qui pèse le plus dans le ranking |
| Subtitle (30) | punchline de positionnement | pas des keywords (déjà couverts) |
| Category | Primary seule suffit | — |
| Content Rights | « No third-party content » si contenu généré | cohérence avec la réalité |
| Age Rating | questionnaire honnête | IA → souvent 9+, normal |
| License Agreement | Apple Standard EULA suffit | Terms liés in-app + en ligne |
| App Store Server Notifications | URL du provider d'abos (V2) | à coller si webhook serveur |
| ⚠️ Privacy Policy URL | **PAS ici** → page App Privacy | on la cherche toujours au mauvais endroit |

## B. Page Version (« 1.0 Prepare for Submission »)
Sélecteur de langue en haut à droite — remplir CHAQUE langue.
- **Promotional Text** (170) : modifiable sans re-review.
- **Description** (4000) : honnête, structurée, freemium explicite (« X gratuits puis abo »).
- **Keywords** (100) : sans espaces après les virgules, pas les mots du Name.
- **Support URL / Marketing URL** : HTTPS 200 vérifiés.
- **Screenshots** : 6,9" (1290×2796) et/ou 6,5" (1242×2688), vraies captures device.
- **Build** : attaché (après processing TestFlight).
- **In-App Purchases and Subscriptions** : **sélectionner les abos** — « Optional » affiché,
  obligatoire en vrai à la 1re soumission.
- **App Review Information** : Sign-in required ✅ · login = compte **premium** · Notes = le modèle
  ci-dessous · contact complet.
- **Version Release** : « Manually release » (contrôler le moment).

## C. App Privacy (section *General*)
« Do you collect data? » → Yes. Pour chaque type : **Linked = Yes**, **Tracking = No**.

| Donnée | Catégorie | Usage |
|---|---|---|
| Email Address | Contact Info | App Functionality |
| User Content | User Content | App Functionality + « shared with third parties » si IA |
| Coarse Location | Location | App Functionality (jamais Precise sans besoin réel) |
| Purchases | Purchases | App Functionality |

❌ Rien dans **Tracking** ni **Advertising** (sauf SDK de pub réel). ✅ **Privacy Policy URL ici**.

## Modèle de notes reviewer (EN)
```
<App> does <one line>.

Two demo accounts:
- PREMIUM (login fields above): <email> / <pw> — unlimited access, review every feature.
- FREE (to test the subscription): <email> / <pw> — after <N> actions the paywall appears; test the
  auto-renewable subscription with your sandbox Apple ID. "Restore Purchases" is on every purchase surface.

How to test: 1) sign in (premium) 2) <core action> 3) <where the result appears>.

Privacy/AI: <provider> API key is server-only (never in the app bundle). The app never embeds
copyrighted/third-party content in generated output.
Account deletion & data export: in Profile.
Privacy: https://<domain>/privacy — Terms: /terms — Support: /support
```

## Les 10 dernières minutes avant « Submit for Review »
1. Les 3 URLs légales répondent 200 (curl ou navigation privée).
2. Compte démo premium : login testé à l'instant.
3. Compte gratuit : atteint bien le paywall.
4. Abonnements « Ready to Submit » ET sélectionnés dans la version.
5. Build attaché = le build testé sur TestFlight (pas un plus vieux).
6. Screenshots à jour avec l'UI réelle.
7. App Privacy label cohérent avec la privacy policy.
8. Export compliance réglé (`ITSAppUsesNonExemptEncryption=false`).
9. Notes reviewer collées, contact joignable.
10. Release « Manual » si tu veux choisir ton jour de sortie.
