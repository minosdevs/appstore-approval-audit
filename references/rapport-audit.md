# Modèle de rapport d'audit

Format de sortie du skill. Copier cette structure, remplir, ne rien inventer sans preuve.

---

# Audit pré-soumission — <App> <version>

**Date** : <date> · **Stack** : <Swift/SwiftUI natif · Expo/React Native · Flutter> ·
**Périmètre** : code + config + backend + métadonnées
**Sections N/A** : <ex. « pas d'IAP → 3.1.1 non applicable » — expliciter, jamais passer sous silence>

## Verdict : GO / GO CONDITIONNEL / NO-GO

<Une phrase. Ex. : « NO-GO — 2 blockers hors-code (pages légales 404, abos Missing Metadata) ;
le code est prêt. GO estimé à +1 jour une fois les actions ASC faites. »>

## Chemin critique (dans l'ordre de déblocage)
1. <action bloquante 1>
2. <action bloquante 2>
3. …

## Findings — CODE

### BLOCKER
- **[B-1] <titre>** — guideline <X.Y.Z> · `fichier:ligne`
  Preuve : <ce qui a été observé>
  Fix : <fix concret, estimé ~N min>

### HIGH
- **[H-1] …**

### MEDIUM
- **[M-1] …**

### LOW
- **[L-1] …** (une ligne suffit)

## Findings — HORS-CODE (App Store Connect, web, comptes)

### BLOCKER
- **[B-x] <titre>** — <où : ASC / site / base>
  Preuve : <URL testée, statut constaté, champ vérifié>
  Fix : <action exacte, où cliquer>

### HIGH / MEDIUM / LOW
- …

## Checklist des 10 dernières minutes
(reprendre `asc-checklist.md` § final, cocher ce qui est déjà vérifié)

- [ ] URLs légales 200
- [ ] Compte démo premium testé
- [ ] Compte gratuit → paywall
- [ ] Abos Ready to Submit + sélectionnés dans la version
- [ ] Build attaché = build testé TestFlight
- [ ] Screenshots à jour
- [ ] App Privacy label cohérent
- [ ] Export compliance réglé
- [ ] Notes reviewer collées
- [ ] Release manuel choisi

---

## Barème de sévérité (rappel)

| Niveau | Définition | Exemples vécus |
|---|---|---|
| BLOCKER | rejet quasi certain / submit impossible | Restore absent d'un paywall ; privacy 404 ; abos Missing Metadata |
| HIGH | risque réel de rejet ou flux reviewer cassé | funnel sans issue sur échec ; reset mdp mort en prod ; webhook sans upsert |
| MEDIUM | risque modéré, à corriger vite | prix en dur ; hadith/citation à re-sourcer ; label privacy incomplet |
| LOW | polish | states vides, wording, perf mineure |

Règle : un doute sérieux monte d'un niveau. Le reviewer ne donne pas le bénéfice du doute.
