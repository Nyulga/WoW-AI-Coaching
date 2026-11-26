# Stories Review — WoW AI Coaching

> Relecture à froid de `docs/stories.md` par rapport à `docs/prd.md` (aucune intent liée). Chaque problème est classé : critical / major / minor.

Sources reviewed : `docs/prd.md` et `docs/stories.md`, branche `dev`, HEAD `17456e3` (révision indiquée par l'appelant ; le relecteur, sans shell, a lu les fichiers sur disque). Intents : aucune — les 16 stories ont `Source: docs/prd.md` et `docs/intents/` n'existe pas. La critique du PRD (`docs/reviews/prd.md`) montre ses points corrigés ou refusés de façon argumentée.

## Perimeter coverage
| PRD feature / accepted intent outcome | Covered by | OK? |
|---|---|---|
| Comptes utilisateur : inscription, connexion | s01-sign-up-sign-in | ✅ |
| Suppression de mon compte et de toutes mes données | s15-delete-my-account | ✅ (voir le finding sur ses dépendances) |
| Comptes administrateurs : gérer les utilisateurs et les logs | s13-admin-manage-users, s14-admin-delete-log | ✅ |
| Envoi et lecture du fichier combat log Retail (5 dans le PRD) | s02-upload-log-list-fights (envoi + liste des combats), la lecture des événements étant répartie dans s04 à s08 | ✅ (le 5 est bien découpé, s02 est noté 4 avec son risque) |
| Vue détaillée : dégâts, soins, buffs/debuffs, graphiques | s04, s05, s06, s07, s08 | ✅ |
| Historique de mes logs et progression sur un même boss | s09-boss-progress-history (+ s03 pour « mon » personnage) | ✅ |
| Accès MCP : lecture du résumé, conseils renvoyés, affichés et gardés dans l'historique | s10-connect-ai-assistant, s11-ai-advice-on-fight | ✅ |
| Comparaison opt-in avec d'autres utilisateurs du site | s12-compare-with-opt-in | ✅ |
| Comparaison aux meilleurs joueurs de ma spé via l'API Warcraft Logs (sur le site et lisible par l'assistant) | s16-compare-with-top-players | ✅ |

- [x] Every feature of the PRD "In scope (core loop)" table is delivered by at least one story
- [x] Every linked intent is accepted and its outcome covered; canonical tracker sources verified (sans objet : aucune intent liée)

## Scope
- [x] Any change to a PRD exclusion has an explicit approved intent decision (aucune exclusion modifiée)
- [x] No story goes beyond the perimeter

Exclusions vérifiées une à une :
- **Rankings et percentiles** : absents. s04 trie seulement les joueurs d'un même combat, ce n'est pas un classement. s16 rappelle cette exclusion dans ses notes.
- **Replay, live logging, mobile, Discord, guilde, paiement** : absents.
- **Versions autres que Retail** : refusées explicitement (s02 AC4).
- **IA payée par le site** : exclue, rappelée dans s10.
- **Usage de l'API Warcraft Logs** : limité à la comparaison aux meilleurs joueurs (s16).
- **Battle.net et réinitialisation du mot de passe** : marqués hors PRD dans s01.

## Story quality
- [x] Each story is an end-to-end shippable slice, not a technical layer
- [ ] Every acceptance criterion can become a test (s02 AC7 et s16 AC1 sont imprécis, voir les findings)
- [x] Agentic notes present and useful (files, constraints, traps)
- [x] Complexity scored; no unsplit 5; every 4 states its risk (s02, s04, s05, s10, s13 et s16 ont chacune leur « Risque (4) »)

## The list as a whole
- [ ] Dependency order executable: no cycle, no forward reference. La liste s01→s16 se suit sans cycle ni référence en avant. En revanche, le « Lot initial » contredit les dépendances déclarées, et s14/s15 dépendent de stories qu'elles ne déclarent pas (voir les findings).
- [x] Ids well-formed (`s<number>-<slug>`), unique and stable (s01 à s16, tous uniques)
- [ ] No overlap or duplication between stories (petit chevauchement sur « Mes logs » entre s02 et s09)

## Findings
- **major — coverage / Lot initial (en-tête de stories.md, l.6-8)** — Le lot annoncé « livrable seul » (s01–s05, s10, s11) ne tient pas avec les dépendances déclarées :
  - s11 dépend de s09 (son AC5 signale les essais conseillés dans la progression), alors que s09 n'est pas dans le lot ;
  - s10 dépend de s06, s07 et s08, et son AC4 exige dans le résumé le détail des sorts, les buffs/debuffs et le DPS/HPS dans le temps ;
  - l'option « le résumé se limite d'abord aux dégâts et soins » contredit donc le texte même de s10.

  Le choix est laissé « à trancher en revue », or ce n'est pas au relecteur de corriger. Recommandation : garder s10 tel qu'il est écrit (il colle au PRD, « résumé de ses combats ») et redéfinir le lot comme s01–s11. Sinon, réécrire AC4 et les dépendances de s10 et retirer AC5 de s11. Le porteur tranche.
- **major — s15-delete-my-account (et s14-admin-delete-log)** — Les critères citent des choses que les dépendances déclarées ne livrent pas :
  - s15 AC2 parle des conseils (s11), des accès d'assistant (s10) et des comparaisons (s12), alors que s15 ne déclare que s01 et s02 ;
  - s14 AC2 parle des comparaisons (s12) et des outils de l'assistant (s10), alors que s14 ne déclare que s13.

  Avec les dépendances déclarées, s15 paraît prête dès s02. Lancée à ce moment-là, AC2 ne peut pas être testé, ou bien l'agent doit construire des tables hors de la story. Solution : ajouter les dépendances réelles (s10, s11, s12), ou rendre ces critères explicitement conditionnels aux stories déjà livrées.
- **minor — s02-upload-log-list-fights AC7** — « plusieurs centaines de Mo » ne donne ni seuil précis ni durée maximale, alors que l'en-tête parle de fichiers de plus d'1 Go. Fixer une taille de référence (et éventuellement un temps limite) pour que le test ait une condition d'échec nette.
- **minor — s16-compare-with-top-players AC1** — Le nombre de « meilleurs joueurs » n'est pas fixé, et le « so that » (« voir ce qu'ils font de mieux ») promet plus que l'AC, qui ne compare qu'un chiffre clé. Aligner la phrase de valeur sur les critères, ou préciser ce qui est comparé.
- **minor — s16-compare-with-top-players, notes** — La contrainte du PRD (si les conditions d'utilisation de l'API interdisent cet usage, la feature sort du périmètre et une autre intégration est choisie) n'est pas rappelée comme critère d'arrêt de la recherche.
- **minor — s13-admin-manage-users AC3** — Un compte désactivé ne peut plus se connecter au site, mais rien ne dit que les appels de son assistant IA (s10, livré avant) sont aussi refusés.
- **minor — s07-fight-buffs-debuffs, dépendances** — L'AC parle de la vue « d'un joueur », introduite par s06 sur la page de combat de s04, alors que s07 ne déclare que s02. L'ordre de la liste fonctionne, mais la dépendance réelle devrait être déclarée.
- **minor — s09-boss-progress-history AC1 et s02 AC1** — Les deux stories touchent la page « Mes logs ». Préciser que s09 ne fait qu'enrichir la page créée par s02 (tri, nombre de combats).
- **minor — s12-compare-with-opt-in** — Aucun AC ne dit que le joueur peut activer le réglage d'accord ni où il se trouve ; AC1 et AC4 le supposent seulement.
- **minor — coverage (remarque)** — Le signal de l'hypothèse (au moins 10 joueurs, 70 %, 30 jours) n'est mesurable par aucun écran. Le tableau « In scope » ne le demande pas, donc ce n'est pas un trou de périmètre, mais la mesure se fera hors produit (par exemple par une requête directe dans la base de données).

## Verdict
Aucun problème critique : tout le périmètre du PRD est couvert, rien ne sort du périmètre, il n'y a ni cycle ni « 5 » non découpé, et les identifiants sont valides. Verdict « no » à cause des deux problèmes majeurs, chacun corrigeable par une simple modification de `docs/stories.md` (lot initial et s10/s11 ; dépendances de s14 et s15). Les points mineurs peuvent être corrigés en même temps ou laissés à la recherche de chaque story.

Max severity: major
Stories ready: no
