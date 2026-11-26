# Stories Review — WoW AI Coaching

> Revue à contexte neuf de `docs/stories.md` par rapport à `docs/prd.md` et aux intents acceptées qui y sont liées. Chaque problème est classé critical / major / minor.

Sources reviewed : `docs/prd.md` et `docs/stories.md`, lus sur le disque — branche `dev`, HEAD `7f9b2b6` selon l'appelant (le relecteur, sans shell, ne l'a pas vérifié lui-même). Aucune intent n'est liée : les 17 stories indiquent toutes `Source: docs/prd.md`, et `docs/intents/` n'existe pas. Le relecteur a lu la revue précédente (HEAD `8d031df`) une fois sa propre vérification terminée : ses 6 findings sont corrigés dans le fichier actuel.

## Perimeter coverage
| PRD feature / accepted intent outcome | Covered by | OK? |
|---|---|---|
| Comptes utilisateur : inscription, connexion | s01-sign-up-sign-in | ✅ |
| Suppression de mon compte et de toutes mes données (RGPD) | s15-delete-my-account | ✅ |
| Comptes administrateurs : gérer les utilisateurs et les logs | s13-admin-manage-users (utilisateurs), s14-admin-delete-log (logs) | ✅ |
| Envoi et lecture du combat log officiel Retail (complexité 5 dans le PRD) | s02-upload-log-list-fights (envoi, liste des combats, refus) ; la lecture des chiffres est répartie entre s04 et s08 | ✅ (le 5 est découpé) |
| Compatibilité avec le patch (affichage du patch, refus d'un format non pris en charge) | s17-patch-compatibility (+ s02 AC4 pour ce qui n'est pas Retail) | ✅ |
| Vue détaillée d'un combat : dégâts, soins, buffs/debuffs, graphiques (familiers, absorptions, surplus de soin) | s04, s05, s06, s07, s08 | ✅ |
| Historique de mes logs et progression sur un même boss | s09-boss-progress-history (+ s03 pour « mon » personnage) | ✅ |
| Accès MCP : l'assistant lit le résumé et renvoie des conseils que le site affiche et garde dans l'historique | s10-connect-ai-assistant (connexion par compte, lecture), s11-ai-advice-on-fight (enregistrement, affichage, signalement dans la progression) | ✅ |
| Comparaison avec d'autres utilisateurs, seulement avec leur accord (opt-in) | s12-compare-with-opt-in | ✅ |
| Comparaison aux meilleurs joueurs de ma spé via l'API Warcraft Logs (sur le site et lisible par l'assistant) | s16-compare-with-top-players (AC1 sur le site, AC3 outil de l'assistant) | ✅ |

- [x] Every feature of the PRD "In scope (core loop)" table is delivered by at least one story
- [x] Every linked intent is accepted and its outcome covered; canonical tracker sources verified : sans objet, aucune intent n'est liée

## Scope
- [x] Any change to a PRD exclusion has an explicit approved intent decision : aucune exclusion du PRD n'est modifiée
- [x] No story goes beyond the perimeter. Points vérifiés :
  - Pas de replay, pas d'envoi en direct, pas de version Classic (refusée en s02 AC4), pas de paiement, pas d'IA payée ou hébergée par le site (note de s10), pas d'application mobile, pas de bot Discord, pas de gestion de guilde.
  - s16 : on compare des valeurs aux 10 meilleurs, sans percentiles ; les autres usages de l'API sont exclus dans ses notes.
  - s01 exclut explicitement Battle.net/Google et la réinitialisation du mot de passe.
  - s03 (choisir son personnage) n'a pas de ligne dans le PRD, mais elle est nécessaire pour « mes » combats, l'historique et les comparaisons : pas une sortie du périmètre.

## Story quality
- [x] Each story is an end-to-end shippable slice, not a technical layer : chaque story livre un écran ou un outil MCP utilisable.
- [x] Every acceptance criterion can become a test : oui, avec trois imprécisions mineures sur la valeur attendue (findings 2 à 4)
- [x] Agentic notes present and useful (files, constraints, traps) : présentes partout et utiles
- [x] Complexity scored; no unsplit 5; every 4 states its risk : aucun 5 ; les six stories notées 4 (s02, s04, s05, s10, s13, s16) indiquent toutes leur risque.

## The list as a whole
- [x] Dependency order executable: no cycle, no forward reference. Ordre vérifié : s01, s02, s03, s04, s17, s05, s06, s07, s08, s09, s10, s11, s12, s13, s14, s15, s16. Chaque dépendance arrive avant la story qui en a besoin. Une réserve mineure sur le lot initial : finding 1.
- [x] Ids well-formed (`s<number>-<slug>`), unique and stable : s01 à s17, tous uniques ; s17 garde son id malgré sa place dans la liste, ce qui est voulu.
- [x] No overlap or duplication between stories. Frontières écrites : s02 AC4 / s17 AC3 ; s02 AC1 / s09 AC1 ; s02 AC6, s10 AC5 et s16 AC3 cloisonnent des surfaces différentes ; s10 AC7 et s13 AC5 n'ont pas le même déclencheur.

## Findings
1. **minor — s17-patch-compatibility / en-tête du lot initial.** L'en-tête dit que s17 est « placée juste après s04, pour qu'aucun chiffre ne soit affiché sans contrôle de version ». Pourtant s04 (dégâts et DPS) est livrée en production avant s17 : pendant une story, le site affiche des chiffres pour n'importe quelle version Retail, alors que le PRD dit qu'« un format non pris en charge est refusé plutôt que mal analysé ». Corrections possibles : s17 passe avant s04 (son AC1 porterait alors sur la page du log, créée en s02, puis sur la page du combat), ou l'en-tête reconnaît cette fenêtre.
2. **minor — s09-boss-progress-history (AC2).** « L'écart avec l'essai précédent » n'a pas d'unité (valeur absolue ou %). L'hypothèse du PRD se mesure en % (+5 %).
3. **minor — s12-compare-with-opt-in (AC3).** Pour un joueur ayant plusieurs combats sur le même boss, la même difficulté et la même spé, l'AC ne dit pas quel chiffre est retenu (meilleur, dernier, moyenne) ni s'il apparaît une ou plusieurs fois : la valeur attendue du test n'est pas fixée.
4. **minor — « difficulté » en Mythique+ (s02 AC1, s09 AC2, s12 AC3, s16 AC1).** En M+, tous les boss ont la même difficulté « Mythique+ » quel que soit le niveau de clé, alors qu'un +10 et un +15 ne sont pas comparables. Il faut dire, dans une note ou un AC, si le niveau de clé fait partie de la « difficulté » pour les essais, la progression et les comparaisons — ou le noter explicitement comme à trancher en recherche.

## Verdict
Aucun critical ni major. Toutes les fonctions « In scope » du PRD sont couvertes et rien ne sort du périmètre. Il n'y a ni cycle ni référence en avant, aucun 5 n'est resté non découpé, et tous les 4 indiquent leur risque. Les 6 findings de la revue précédente sont corrigés. Il reste 4 minors, corrigeables dans `docs/stories.md` avant `/ztp-architect`, mais non bloquants.

Max severity: minor
Stories ready: yes
