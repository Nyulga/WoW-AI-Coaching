# Stories Review — WoW AI Coaching

> Relecture à froid de `docs/stories.md` par rapport à `docs/prd.md` et aux intents acceptés qui y sont liés. Chaque problème est classé critical, major ou minor.

**Verdict : le découpage est prêt.** Aucun problème bloquant. Six remarques mineures, à corriger ou non avant `/ztp-architect`. La plus importante est la n°1 (suppression de compte absente du lot initial).

Sources relues : `docs/prd.md` et `docs/stories.md` (s01 à s16), dans leur état sur disque — branche `dev`, HEAD `0cbc8aa` (indiqué par l'appelant ; le relecteur, sans shell, ne l'a pas vérifié lui-même). Aucun intent : chaque story a pour Source `docs/prd.md`, et `docs/intents/` ne contient aucun fichier. Aucune source manquante ni inaccessible.

Cette relecture remplace la précédente (HEAD `17456e3`, `Stories ready: no`), dont les deux problèmes majeurs ont été corrigés dans `0cbc8aa`.

## Perimeter coverage
| PRD feature / accepted intent outcome | Covered by | OK? |
|---|---|---|
| Comptes utilisateur : inscription, connexion | s01-sign-up-sign-in | ✅ |
| Suppression de mon compte et de toutes mes données | s15-delete-my-account | ✅ |
| Comptes administrateurs : gérer les utilisateurs et les logs | s13-admin-manage-users, s14-admin-delete-log | ✅ |
| Envoi et lecture du combat log officiel WoW Retail | s02-upload-log-list-fights (lecture complétée par s03 à s08) | ✅ |
| Vue détaillée d'un combat : dégâts, soins, buffs/debuffs, graphiques | s04, s05, s06, s07, s08 | ✅ |
| Historique de mes logs et progression sur un même boss | s09-boss-progress-history | ✅ |
| Accès MCP : lecture du résumé, conseils renvoyés, affichés et gardés dans l'historique | s10-connect-ai-assistant, s11-ai-advice-on-fight (AC5 : signalement dans la progression) | ✅ |
| Comparaison opt-in avec d'autres utilisateurs du site | s12-compare-with-opt-in | ✅ |
| Comparaison aux meilleurs de ma spé via l'API Warcraft Logs (site + assistant IA) | s16-compare-with-top-players (AC1 pour le site, AC3 pour l'assistant) | ✅ |

- [x] Every feature of the PRD "In scope (core loop)" table is delivered by at least one story
- [x] Every linked intent is accepted and its outcome covered; canonical tracker sources verified — aucun intent lié, donc sans objet

## Scope
- [x] Any change to a PRD exclusion has an explicit approved intent decision — aucune exclusion du PRD n'est modifiée
- [x] No story goes beyond the perimeter — s01 écarte explicitement Battle.net/Google et la réinitialisation du mot de passe. s16 se limite à la comparaison aux meilleurs et écarte l'import de rapports et les classements. Aucune story n'ajoute de paiement, d'IA payée par le site, de live logging, de replay ou de gestion de guilde.

## Story quality
- [x] Each story is an end-to-end shippable slice, not a technical layer — les tables et le parseur naissent dans les stories qui en ont besoin.
- [x] Every acceptance criterion can become a test — une réserve sur s02 AC7 (remarque n°2).
- [x] Agentic notes present and useful (files, constraints, traps) — présentes sur les 16 stories ; pièges communs rappelés en tête du fichier.
- [x] Complexity scored; no unsplit 5; every 4 states its risk — aucune story à 5 ; le « 5 » du PRD est découpé en s02 puis s04 à s08 ; les six stories à 4 (s02, s04, s05, s10, s13, s16) indiquent chacune leur risque.

## The list as a whole
- [x] Dependency order executable: no cycle, no forward reference — l'ordre s01 → s16 s'exécute de haut en bas ; chaque dépendance pointe vers une story placée avant.
- [x] Ids well-formed (`s<number>-<slug>`), unique and stable — s01 à s16, tous uniques et bien formés.
- [x] No overlap or duplication between stories — léger chevauchement s02 / s09 sur « Mes logs » (remarque n°5).

## Findings

1. **minor — s15-delete-my-account / « Lot initial »** — Le lot initial « livrable seul » (s01 à s11) ne contient pas la suppression de compte, que le PRD déclare « obligatoire (RGPD) ». s15 dépend de s12 (AC2), ce qui la repousse en fin de liste : si le lot initial est ouvert à de vrais testeurs, il le sera sans droit de suppression.
   - Recommandé : placer s15 dans le lot initial et limiter son AC2 aux données qui existent déjà ; s12 ajouterait alors la suppression de ses propres données.
   - Autre option : écrire que le lot initial n'est pas ouvert à des tiers.
2. **minor — s02-upload-log-list-fights AC7** — « Un log réel d'au moins 1 Go » repose sur un fichier jamais committé : pas de test reproductible pour l'agent ni en intégration continue. Préciser soit un log de 1 Go généré avec des noms fictifs, soit une vérification manuelle explicite par le porteur.
3. **minor — s02 / s04 / s05 (combat log avancé)** — Le rattachement des familiers (s04 AC2, s05 AC4) et la spécialisation (s04 AC1, utilisée par s09 AC3) supposent le « combat log avancé » activé. Aucun critère ne dit ce qui se passe sinon : refus à l'envoi ou affichage dégradé.
4. **minor — s02 / s09 (Mythique+)** — Aucun critère ne dit comment un boss de clé M+ apparaît (quelle « difficulté », avec ou sans niveau de clé). Le regroupement « même boss, même difficulté » de s09 et s12 est ambigu pour le M+. À préciser en recherche ou par un critère.
5. **minor — s02 / s09 (chevauchement léger)** — s09 AC1 n'apporte qu'un tri et deux colonnes à la page « Mes logs » créée par s02 ; la frontière est indiquée, mais il pourrait être absorbé par s02.
6. **minor — s16-compare-with-top-players** — Rien ne dit qu'une éventuelle copie locale des résultats Warcraft Logs doit être effacée avec le compte ou avec le log (s14, s15). La note de s15 ne couvre pas les données créées après elle.

Point vérifié, sans problème : afficher les « 10 meilleurs » (s16 AC1) ne réintroduit pas les classements mondiaux / percentiles exclus.

## Verdict
Max severity: minor
Stories ready: yes
