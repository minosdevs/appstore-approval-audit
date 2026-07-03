# Guidelines Apple — celles qui rejettent vraiment

Classées par fréquence de casse observée en vrai. Chaque entrée : ce qu'Apple veut, le piège, comment l'auditer.

## 2.1 — App Completeness
**Apple veut** : une app finie, testable de bout en bout, sans crash ni placeholder.
- Compte démo **premium** dans les champs login + compte **gratuit → paywall** dans les notes.
- Données de démo peuplées (l'app du reviewer ne doit pas être vide).
- Aucun spinner infini : timeout sur tous les appels longs, message d'erreur dédié par cause.
- Le flux « mot de passe oublié » marche **en build prod sur device** (deep-link gravé au build,
  allow list de redirect remplie, Site URL jamais laissé sur localhost).
- **Audit** : dérouler le parcours reviewer complet sur TestFlight — signup, action cœur, paywall,
  restore, reset mdp, suppression de compte.

## 3.1.1 — In-App Purchase
**Apple veut** : tout achat de contenu digital passe par l'IAP, avec Restore partout.
- **« Restaurer les achats » sur CHAQUE surface d'achat** (paywall principal, paywall cadeau/promo,
  écran d'onboarding qui vend). Piège n°1 en vrai.
- Prix affichés depuis StoreKit (`product.price` + `currencyCode`), jamais en dur.
- Essai gratuit : « Gratuit X jours, puis PRIX/période » + fallback prix simple si l'offre intro ne
  charge pas. Éligibilité : un user ayant consommé son essai ne devrait pas voir « Gratuit » (tolérable
  au launch, à durcir ensuite via `checkTrialOrIntroductoryPriceEligibility`).
- Pas de lien vers un paiement externe, pas de mention d'un prix « ailleurs moins cher ».
- **Audit** : grep tous les composants affichant un prix ; vérifier Restore + source du prix dans chacun.

## 3.1.2 — Subscriptions
**Apple veut** : l'utilisateur comprend ce qu'il paie, et les liens légaux sont accessibles.
- Le paywall dit : nom de l'abo, durée, prix, auto-renouvellement, comment annuler.
- **Privacy Policy + Terms (EULA) accessibles** depuis l'app ET en URL publiques HTTPS 200.
- Métadonnées de la fiche cohérentes avec le paywall (prix, essai, périodes).
- **Audit** : ouvrir les 3 URLs légales (curl → 200), relire le paywall mot à mot.

## 5.1.1 — Data Collection and Storage
**Apple veut** : collecte minimale, transparente, réversible.
- **Suppression de compte in-app** (v) — obligatoire dès qu'il y a création de compte. Un mailto ne
  suffit pas. Export des données = très bon signal.
- Le App Privacy label reflète EXACTEMENT ce que l'app collecte — et la privacy policy raconte la
  même histoire (mêmes tiers, mêmes usages).
- Location : **Coarse** sauf besoin réel de Precise (les horaires de prière n'ont pas besoin de Precise).
- **Audit** : lister les données collectées depuis le code (auth, analytics, contenu) → comparer au
  label prévu → comparer à la privacy policy.

## 5.1.2 — Data Use and Sharing (le piège IA)
**Apple veut** : si des données utilisateur partent chez un tiers (fournisseur IA inclus), c'est divulgué.
- La privacy policy **nomme le fournisseur IA** (et les autres tiers : backend, abonnements).
- Le App Privacy label coche « shared with third parties » sur User Content si l'input part chez l'IA.
- Notes reviewer : préciser « clé API server-only, jamais dans le bundle ».
- In-app : une ligne de transparence près de la feature IA est le choix robuste ; y renoncer est un
  risque assumable mais documenté (si un reviewer le réclame → ajouter une porte de consentement one-time).
- **Audit** : suivre le trajet d'un input utilisateur jusqu'au tiers ; vérifier que chaque étape est déclarée.

## 4.8 — Sign in with Apple
Login social tiers proposé (Google, Facebook…) → **Sign in with Apple obligatoire**. Email/mdp maison
seul = non concerné. **Audit** : lister les boutons de login.

## 2.5.4 — Background modes
`UIBackgroundModes` déclaré mais non utilisé = rejet. **Audit** : lire l'**Info.plist FINAL** et
justifier chaque mode — *Expo : celui du build généré (prebuild/EAS), pas juste `app.json` ;
Swift : l'Info.plist du target + les Capabilities Xcode (Signing & Capabilities)*.

## 2.3 — Accurate Metadata
- Screenshots = vraies captures de l'app (device réel, compte peuplé).
- Description honnête : pas de promesse de revenus, pas de fonctionnalité absente, ne jamais suggérer
  une authenticité de contenu que l'app n'a pas.
- Keywords : pas de marques tierces, pas de répétition du nom.
- **Audit** : relire nom/sous-titre/description/keywords contre la réalité de l'app.

## Export compliance & âge
- `ITSAppUsesNonExemptEncryption=false` dans la config → question réglée à chaque build.
  *Expo : `app.json → ios.infoPlist` · Swift : Info.plist du target.*
- Âge : le système répond seul via le questionnaire ; l'IA/contenu généré donne souvent **9+** → normal,
  pas un blocker. UGC privé non partagé ≠ UGC public (répondre No à « user-generated content » public).

## Pièges de dernière ligne droite (hors guidelines, vécus)
Communs à tous les stacks :
- Abos **« Missing Metadata »** : screenshot de review par abo + **localisation du display name du
  GROUPE** d'abonnement. Tant que pas « Ready to Submit », les produits chargés par le SDK
  (offerings RevenueCat, `Product.products(for:)` StoreKit) reviennent VIDES → le paywall du
  reviewer serait cassé.
- **Sélectionner les abonnements dans la version** à la 1re soumission (champ « Optional » trompeur).
- Testeur TestFlight interne bloqué en « Invited » : l'app n'apparaît qu'après acceptation du mail
  d'invitation (jamais par code).
- « Add for Review » grisé = presque toujours screenshots manquants ou build non attaché.

Spécifique Expo/EAS :
- Env gravées au build (`eas.json`) : changer une clé SDK = **rebuild obligatoire**.
- Le dev build (ad-hoc) s'installe en direct ; le build production signé App Store ne s'installe
  QUE via TestFlight.

Spécifique Swift/Xcode :
- Archive en configuration **Release** (les flags DEBUG/sandbox de test ne doivent pas fuiter).
- Le fichier de config StoreKit local (`.storekit`) ne teste PAS le vrai sandbox — valider les IAP
  sur TestFlight avant submit.
