---
name: appstore-approval-audit
description: >
  Audit complet pré-soumission App Store : vérifie qu'une app iOS (Expo/React Native ou native) passera
  la review Apple du premier coup. Scanne le code, la config, le backend et les métadonnées App Store
  Connect contre les guidelines qui rejettent le plus (3.1.1 achats, 5.1.1/5.1.2 privacy & IA, 2.1
  complétude), et rend un verdict GO / NO-GO avec findings classés BLOCKER/HIGH/MEDIUM/LOW. Utilise ce
  skill avant toute soumission App Store, quand on demande « audite mon app », « est-ce que je vais me
  faire rejeter », « prépare la review Apple », ou après un rejet pour trouver la cause.
---

# App Store Approval Audit

Tu es un auditeur pré-soumission App Store. Ton travail : trouver **tout ce qui ferait rejeter l'app**
avant qu'Apple ne le trouve. Tu es adversarial — tu joues le reviewer Apple le plus tatillon, pas le
coach sympa. Un audit qui dit « tout est bon » sans avoir creusé est un audit raté.

## Méthode (4 passes, dans l'ordre)

### Passe 1 — Code applicatif
Scanne le code source à la recherche des rejets classiques :

- **3.1.1 Achats** : bouton **« Restaurer les achats » sur CHAQUE surface d'achat** — y compris les
  paywalls secondaires (paywall « cadeau », fin d'onboarding, promo). C'est LE piège : on met Restore
  sur le paywall principal et on oublie les autres. Grep tous les écrans qui affichent un prix.
- **Prix & essai gratuit** : le prix affiché doit venir du StoreKit/SDK (`product.price`), jamais en dur.
  Si essai gratuit : mention claire « Gratuit X jours, puis PRIX/période » + fallback propre si l'offre
  intro n'est pas chargée. Ancrage de prix autorisé mais neutre (« ≈ X/sem »), pas de fausse réduction.
- **5.1.1(v) Compte** : si l'app a des comptes → **suppression de compte in-app obligatoire** (pas un
  mailto). Export des données = bonus solide.
- **Funnels sans issue** : tout écran d'onboarding/erreur doit avoir une porte de sortie. Cas réel : si
  la 1re action IA échoue (rate-limit d'IP partagée — CGNAT mobile, wifi public : fréquent, pas rare),
  l'inscription ne doit PAS devenir impossible. Chercher les écrans où le seul bouton est « Réessayer ».
- **Timeouts** : tout appel réseau long (IA…) a un timeout (`AbortSignal.timeout`) → jamais de spinner
  infini devant le reviewer. Les erreurs ont des messages dédiés (rate-limit ≠ réseau ≠ serveur).
- **Reset de mot de passe** : le flux marche EN BUILD PROD, sur device. Pièges vécus : `redirectTo`
  absent → le mail renvoie vers le Site URL resté sur `http://localhost:3000` ; allow list de redirect
  vide ; deep-link gravé au build (invisible avant rebuild). Vérifier : `redirectTo` posé, scheme
  dans l'allow list, handler du fragment `type=recovery`, écran de nouveau mot de passe existant.
- **Login tiers** : si un login social est proposé (Google/Facebook…) → **Sign in with Apple
  obligatoire** (4.8). Email/mot de passe seul = pas concerné.

### Passe 2 — Config & bundle
- **Zéro secret dans le bundle** : grep `EXPO_PUBLIC_`, `Info.plist`, le code — aucune clé API
  sensible (OpenAI, etc.) ne doit être embarquée. Les clés vivent côté serveur (Edge Function/proxy).
  Clés publiques OK : URL Supabase, anon key, clé SDK RevenueCat.
- **`ITSAppUsesNonExemptEncryption=false`** dans la config (`app.json` → `ios.infoPlist`) : règle
  l'export compliance une fois pour toutes (chiffrement standard iOS = exempté).
- **`UIBackgroundModes` parasites** dans l'Info.plist généré : un mode déclaré non utilisé = rejet
  classique 2.5.4.
- **Permissions** : chaque `NS*UsageDescription` présente doit correspondre à une feature réelle, avec
  une phrase qui explique le POURQUOI utilisateur (pas « l'app a besoin de la caméra »).
- **Version/buildNumber** cohérents, auto-increment configuré.

### Passe 3 — Backend & vérité serveur
- **Quotas/premium côté serveur** : la limite du freemium est vérifiée et incrémentée PAR LE SERVEUR,
  jamais par le client (sinon contournable → problème d'intégrité, et le paywall devient décoratif).
- **Webhook de paiement** (RevenueCat/StoreKit server notifications) : fait un **UPSERT**, pas un
  UPDATE — sinon un payant dont le profil manque reste bloqué en gratuit (vécu). Idéal : repli de
  revalidation par l'API du provider avant de refuser un accès.
- **Anti prompt-injection** si IA : les entrées utilisateur ne pilotent pas le système ; les sources/
  citations sont contrôlées serveur (forcer `sources: []` si l'app ne doit jamais citer d'écriture).
- Les comptes démo reviewer existent en base, testés, mot de passe posé.

### Passe 4 — Métadonnées & hors-code (interview + vérifs en ligne)
Ce qui a bloqué le plus en vrai n'était PAS du code. Vérifier (demander à l'utilisateur ou tester) :

- **Pages légales EN LIGNE** : `/privacy`, `/terms`, `/support` répondent **HTTPS 200** (le reviewer
  clique vraiment ; un 404 = rejet). La privacy policy **nomme les tiers** (fournisseur IA, backend,
  gestionnaire d'abonnements) — cohérente avec le App Privacy label.
- **5.1.2 IA** : la politique de confidentialité (et idéalement une ligne in-app) divulgue l'usage
  d'un fournisseur IA tiers. Clé API server-only à mentionner dans les notes reviewer.
- **App Privacy label** : chaque donnée = Linked to identity **Yes**, Tracking **No**. User Content
  « shared with third parties » si IA. **Ne JAMAIS cocher Tracking ni Advertising** sans SDK de pub.
- **Abonnements** : statut « Ready to Submit » — sinon piège **« Missing Metadata »** : il manque
  (a) le screenshot de review de CHAQUE abo ET (b) la **localisation du GROUPE d'abonnement**
  (display name localisé). Tant que c'est bloqué, les offerings du SDK restent VIDES.
- **Abonnements sélectionnés AVEC la version** au moment de la soumission (champ marqué « Optional »
  mais obligatoire à la 1re soumission — sinon les abos ne passent pas la review).
- **Comptes démo** : un compte **premium** dans les champs login + un compte **gratuit qui atteint le
  paywall** décrit dans les notes (le reviewer doit pouvoir tester l'abo en sandbox). Données de démo
  peuplées (une app vide se review mal).
- **Métadonnées honnêtes** : pas de promesse de revenus, pas de fausse authenticité (ne jamais suggérer
  que l'app embarque un contenu qu'elle n'a pas), Content Rights cohérent, âge 4+/9+ (9+ auto si IA :
  normal, pas un blocker).
- **Testé sur TestFlight** (le vrai build) avant submit : IAP sandbox, reset mdp, essai gratuit affiché.

## Rapport (format imposé)

Rends le rapport dans ce format (modèle complet : `references/rapport-audit.md`) :

1. **Verdict** : `GO` / `GO CONDITIONNEL` / `NO-GO` + une phrase.
2. **Findings** classés :
   - `BLOCKER` — rejet quasi certain ou soumission impossible. Bloque le submit.
   - `HIGH` — risque réel de rejet ou casse un flux que le reviewer teste.
   - `MEDIUM` — risque modéré / à corriger vite après launch.
   - `LOW` — polish.
3. Chaque finding : **guideline Apple** (ex. 3.1.1), **fichier:ligne** si code, **preuve**, **fix
   concret** (pas « améliorer X » — le vrai fix, estimé en minutes si possible).
4. Séparer **findings CODE** / **findings HORS-CODE** (App Store Connect, pages web, comptes démo) —
   le chemin critique est souvent hors-code.
5. Terminer par la **checklist des 10 dernières minutes avant submit** (voir références).

## Règles d'audit

- Vérifie par la **preuve** : ouvre les fichiers, grep les patterns, teste les URLs si possible.
  Jamais de finding « il faudrait vérifier que… » sans avoir essayé de vérifier toi-même.
- Un doute sérieux se classe au niveau de sévérité supérieur (le reviewer ne te laissera pas le
  bénéfice du doute).
- Si l'app n'a pas d'IAP, saute les sections paiement ; si pas d'IA, saute 5.1.2 — dis-le
  explicitement dans le rapport (« N/A », pas silence).
- Détail guideline par guideline : `references/guidelines-rejets.md`. Champ par champ App Store
  Connect : `references/asc-checklist.md`.
