# Stories Review — WoW AI Coaching

> Revue à contexte neuf de `docs/stories.md` par rapport à `docs/prd.md`. Aucune intent n'est liée. Chaque problème est classé critical / major / minor.

Sources reviewed: `docs/prd.md` et `docs/stories.md`, lus sur le disque (branche `dev`, HEAD `8d031df` selon l'appelant ; le relecteur, sans shell, ne l'a pas vérifié lui-même). Aucune intent n'est liée : les 17 stories ont toutes `Source: docs/prd.md`, et `docs/intents/` n'existe pas. Le relecteur n'a pas lu la revue précédente, pour garder un regard neuf.

Cette relecture remplace la précédente (HEAD `0cbc8aa`), antérieure à l'ajout au PRD de la feature « Compatibilité avec le patch » et de la story s17.

## Perimeter coverage
| PRD feature / accepted intent outcome | Covered by | OK? |
|---|---|---|
| Comptes utilisateur : inscription, connexion | s01-sign-up-sign-in | ✅ |
| Suppression de mon compte et de toutes mes données (RGPD) | s15-delete-my-account | ✅ |
| Comptes administrateurs : gérer les utilisateurs et les logs | s13-admin-manage-users (utilisateurs), s14-admin-delete-log (logs) | ✅ |
| Envoi et lecture du fichier combat log officiel Retail (PRD : 5) | s02-upload-log-list-fights (envoi, liste des combats, refus) ; la lecture des chiffres est répartie entre s04 et s08 | ✅ (le 5 est bien découpé) |
| Compatibilité avec le patch (patch affiché, format non pris en charge refusé) | s17-patch-compatibility (+ s02 AC4 pour ce qui n'est pas Retail) | ✅ (mais voir l'ordre : finding 1) |
| Vue détaillée : dégâts, soins, buffs/debuffs, graphiques (familiers, absorptions, surplus de soin) | s04 (dégâts, familiers), s05 (soins, absorptions, surplus), s06 (sorts), s07 (buffs/debuffs), s08 (graphiques) | ✅ |
| Historique de mes logs et progression sur un même boss | s09-boss-progress-history (+ s03 pour savoir quel personnage est le mien) | ✅ |
| Accès MCP : l'assistant lit le résumé, renvoie des conseils que le site affiche et garde dans l'historique | s10-connect-ai-assistant (connexion par compte, lecture), s11-ai-advice-on-fight (enregistrement, affichage, signalement dans la progression) | ✅ |
| Comparaison avec d'autres utilisateurs, seulement avec leur accord (opt-in) | s12-compare-with-opt-in | ✅ |
| Comparaison aux meilleurs joueurs de ma spé via l'API Warcraft Logs (sur le site et lisible par l'assistant) | s16-compare-with-top-players (AC1 site, AC3 outil de l'assistant) | ✅ |

- [x] Every feature of the PRD "In scope (core loop)" table is delivered by at least one story
- [x] Every linked intent is accepted and its outcome covered; canonical tracker sources verified — sans objet : aucune intent liée

## Scope
- [x] Any change to a PRD exclusion has an explicit approved intent decision — aucune exclusion du PRD n'est modifiée
- [x] No story goes beyond the perimeter. Points vérifiés :
  - s16 compare aux « 10 meilleurs » de la spé, sans classement en percentiles ; l'autre usage de l'API (import de rapports, classements) est explicitement exclu dans ses notes.
  - Aucun replay, aucun envoi en direct, aucun paiement, aucune IA hébergée par le site, aucun bot Discord, aucune gestion de guilde.
  - s01 exclut Battle.net/Google et la réinitialisation du mot de passe.
  - s03 (choisir son personnage) n'est pas une ligne du PRD, mais c'est une condition nécessaire pour « mes » combats, l'historique et les comparaisons : pas une sortie du périmètre.

## Story quality
- [x] Each story is an end-to-end shippable slice, not a technical layer — chaque story produit un écran ou un outil utilisable.
- [x] Every acceptance criterion can become a test — avec trois réserves mineures (findings 3 à 5)
- [x] Agentic notes present and useful (files, constraints, traps) — une omission mineure (finding 6).
- [x] Complexity scored; no unsplit 5; every 4 states its risk — aucun 5 ; tous les 4 (s02, s04, s05, s10, s13, s16) donnent leur risque.

## The list as a whole
- [ ] Dependency order executable: no cycle, no forward reference — pas de cycle ni de référence en avant, mais la place de s17 contredit une contrainte du PRD pour le lot déclaré « livrable seul » (finding 1).
- [x] Ids well-formed (`s<number>-<slug>`), unique and stable — s01 à s17
- [x] No overlap or duplication between stories — frontières explicites : s02 AC4 (pas Retail) / s17 AC3 (Retail, version non prise en charge) ; s02 AC1 / s09 AC1 (« Mes logs » créée en s02).

## Findings
1. **major — s17-patch-compatibility (ordre et lot initial).** Le PRD impose (Contraintes, « Justesse à chaque patch ») qu'« un format non pris en charge est refusé plutôt que mal analysé ». Le « Lot initial livrable seul » (s01 à s11) ne contient pas s17, placée en dernier : s04 à s11, déployées en production, afficheraient et donneraient à l'assistant IA des chiffres pour n'importe quelle version Retail, sans contrôle de version. s17 ne dépend que de s04 : la placer juste après s04 (l'id peut rester le même) et l'ajouter au lot initial. Sinon, le refus d'une version non prise en charge (s17 AC3) doit entrer dans s02.
2. **minor — s14-admin-delete-log (dépendances).** Ses notes demandent de supprimer les conseils rattachés au log, créés par s11 ; s11 n'est pas dans ses dépendances (seulement s10, s12, s13). Ajouter s11.
3. **minor — s02-upload-log-list-fights (AC7).** Le log d'au moins 1 Go est « un log réel fourni par le porteur, jamais committé » : un test d'acceptation figé ne peut pas tourner sans ce fichier. Préciser que le gros fichier est généré par le test (extrait fictif répété jusqu'à 1 Go), ou que cet AC est une vérification manuelle.
4. **minor — s07-fight-buffs-debuffs (AC2).** Le « temps de présence en % » d'un debuff posé sur plusieurs ennemis n'a pas de règle (par cible, ou union des périodes sur toutes les cibles) : la valeur attendue du test n'est pas fixée.
5. **minor — s10-connect-ai-assistant (AC1).** « L'assistant retenu à l'architecture » renvoie à une décision qui n'existe pas encore ; l'assistant ciblé doit être nommé avant le plan pour que le critère soit testable.
6. **minor — s16-compare-with-top-players (suppression des données).** s14 et s15 sont livrées avant s16. Si s16 garde des données liées à un joueur ou à un combat (cache des réponses Warcraft Logs), aucune note ne demande d'étendre à ces données la suppression du compte et du log. Ajouter une note à s16.

## Verdict
Pas de critique : toutes les fonctions « In scope » du PRD sont couvertes, rien ne sort du périmètre, il n'y a ni cycle ni référence en avant, et aucun 5 n'est resté non découpé. Il reste un major (finding 1, la place de s17) à corriger dans `docs/stories.md` avant de continuer, idéalement, et cinq minors.

Max severity: major
Stories ready: yes
