# Stories Review — WoW AI Coaching

> Revue à contexte neuf de `docs/stories.md` par rapport à `docs/prd.md` et aux intents acceptées qui y sont liées. Chaque problème est classé critical / major / minor.

Sources relues :
- `docs/prd.md`, révision `9a95e5f` (celle qui m'a été indiquée).
- `docs/stories.md`, révision `8efad11`, stories s01 à s21.

J'ai lu les deux fichiers sur le disque. Dans `.git`, `HEAD` pointe sur `refs/heads/dev`, et `.git\refs\heads\dev` contient `8efad112df702fcfaeb12fc297f7818a7adaf321`. Comme l'arbre était propre au départ, le disque correspond à `8efad11`. Je n'ai pas de terminal, donc je n'ai pas pu comparer le contenu des commits entre eux.

Aucune intent n'est liée : les 21 stories indiquent `Source: docs/prd.md`, et `docs/intents/` n'existe pas. `docs/lots.md` existe, ce qui confirme l'en-tête qui y renvoie. Je ne l'ai pas relu au-delà de ce lien.

J'ai fait toute la revue avant de relire la revue précédente (`docs/reviews/stories.md`, sur `da7bbe9`). Le suivi de ses findings est en fin de rapport.

## Perimeter coverage
| PRD feature / accepted intent outcome | Covered by | OK? |
|---|---|---|
| Comptes utilisateur : inscription, connexion | s01-sign-up-sign-in | ✅ |
| Suppression de mon compte et de toutes mes données (RGPD) | s15-delete-my-account, complétée par s16 (note), s20 AC5 et s21 (note) | ✅ (voir finding 3) |
| Comptes administrateurs : gérer les utilisateurs et les logs | s13-admin-manage-users, s14-admin-delete-log | ✅ |
| Envoi et lecture du combat log Retail (noté 5 dans le PRD) | s02 (envoi, liste des combats, refus), puis s04 à s08 (chiffres) | ✅ (le 5 est découpé) |
| Compatibilité avec le patch : patch affiché, format non pris en charge refusé | s17-patch-compatibility, avec s02 AC4 pour les versions autres que Retail | ✅ |
| Vue détaillée d'un combat : dégâts, soins, buffs/debuffs, graphiques | s04, s05, s06, s07, s08 | ✅ |
| Historique de mes logs et progression sur un même boss | s09, avec s03 pour le choix du personnage | ✅ |
| Accès MCP : l'assistant lit le résumé, renvoie des conseils que le site affiche et garde | s10 (branchement, outils, coupure d'accès), s11 (conseils affichés et gardés, signalés dans la progression) | ✅ |
| Comparaison avec d'autres utilisateurs, avec leur accord (opt-in) | s12 | ✅ |
| Comparaison aux meilleurs de ma spé via l'API Warcraft Logs, sur le site et pour l'assistant | s16 (AC1 sur le site, AC3 pour l'assistant) | ✅ |
| Abonnement 5 €/mois illimité, 3 coachings/semaine sans abonnement, lister ses combats gratuit et illimité, abonnement à l'assistant non compris, résiliation, paiement refusé | s18 (limite, AC5 : liste illimitée), s19 (AC1-AC2 abonnement illimité, AC7 assistant non compris), s20 (résiliation, renouvellement, paiement refusé) | ✅ |
| Partage Discord : chiffre clé, progression, dernier conseil, salon choisi, sans bot, message clair si Discord ne répond pas | s21-share-fight-discord | ✅ |

- [x] Chaque fonction du tableau « In scope (core loop) » du PRD est livrée par au moins une story.
- [x] Chaque intent liée est acceptée et son résultat couvert : sans objet, aucune intent n'est liée.

La mesure de l'hypothèse (10 joueurs, 70 %, 30 jours) se fait hors produit, comme l'annonce l'en-tête. Elle s'appuie sur les données de s09 et s11 : le conseil enregistré par essai et le chiffre clé.

## Scope
- [x] Aucune exclusion du PRD n'est levée : l'abonnement et Discord font partie du PRD lui-même, ils n'arrivent pas par une exception.
- [x] Aucune story ne sort du périmètre, avec deux réserves mineures (findings 4 et 5). Ce que j'ai vérifié :
  - Classements et rankings : s16 ne sert qu'à la comparaison aux meilleurs de sa spé, l'usage autorisé. Sa note exclut l'import de rapports et les classements.
  - Discord : s21 passe par un webhook (une adresse fournie par Discord pour publier dans un salon). Il n'y a ni bot, ni liaison de compte Discord, ni autre plateforme.
  - Abonnement : un seul niveau, pas de paiement à l'unité, aucune donnée de carte sur le site. s20 exclut le remboursement et les niveaux multiples.
  - IA : le site n'en paie aucune (note de s10).
  - s01 exclut Battle.net/Google et la réinitialisation du mot de passe, ce qui reste conforme au PRD.

## Story quality
- [x] Chaque story est une tranche livrable de bout en bout, pas une couche technique. Réserve : s18 mise en production avant s19 (finding 1).
- [x] Chaque critère d'acceptation peut devenir un test. Restent quelques valeurs attendues ou cas limites non fixés : findings 2, 6, 7, 8 et 9.
- [x] Les notes pour l'agent sont présentes et utiles. Il manque une note de suppression des données dans s18 et s19 (finding 3).
- [x] Complexité notée, aucun 5 laissé entier, chaque 4 indique son risque. Les 4 sont s02, s04, s05, s10, s16, s19 et s20 : chacun a une ligne « Risque (4) ». s13 est notée 3 (4 dans le PRD) et garde une note de risque sur les droits.

## The list as a whole
- [x] Ordre des dépendances exécutable, sans cycle ni référence en avant. J'ai vérifié l'ordre du fichier : s01, s02, s03, s04, s17, s05, s06, s07, s08, s09, s10, s11, s12, s13, s14, s15, s16, s18, s19, s20, s21. Chaque dépendance déclarée arrive avant la story qui en a besoin. Les renvois écrits dans le texte sont eux aussi en arrière :
  - s09 AC1 renvoie à s02 ;
  - la note de s18 renvoie à s11 et s16 ;
  - s19 AC8 renvoie à s18 ;
  - s20 AC5 renvoie à s15.
  Les notes de s16 et s21 étendent après coup les suppressions de s14 et s15 : c'est une extension par une story plus tardive, pas une référence en avant.
- [x] Ids bien formés (`s<number>-<slug>`), uniques et stables : s01 à s21, sans doublon. s17 est placée hors de l'ordre des numéros, et la raison est écrite (en-tête et note de s17).
- [x] Pas de chevauchement entre stories :
  - s02 refuse ce qui n'est pas Retail ; s17 refuse une version Retail non prise en charge. La frontière est écrite.
  - s09 AC1 complète la page « Mes logs » de s02 sans la refaire.
  - s18 compte la limite, s19 la lève, s20 gère la suite de l'abonnement.
  - s10 AC7 (le joueur coupe l'accès) et s13 AC5 (compte désactivé) sont deux déclencheurs distincts.

## Findings
1. **minor — s18-ai-coaching-weekly-limit / s19-subscribe.** Le texte « comment s'abonner » a bien été déplacé dans s19 AC8. Mais rien ne dit ce qui se passe entre la mise en production de s18 et celle de s19. Pendant ce temps, la limite de 3 coachings s'applique sans aucun moyen de la lever, et le « so that » de s18 (« j'essaie avant de m'abonner ») n'est pas encore possible. C'était la seconde moitié de l'ancien finding 2. Correction : une phrase qui accepte ce délai, ou qui dit que s18 et s19 passent en production ensemble.
2. **minor — s19 (AC1 / AC9).** AC1 place « S'abonner » « depuis mon compte », une page réservée aux comptes connectés (s01 AC5). AC9 suppose qu'un visiteur non connecté voit ce bouton. On ne sait pas où ce visiteur le trouve : page d'abonnement publique ? lien du message de refus (AC8) ? Il faut le préciser pour qu'AC9 soit testable sans ambiguïté.
3. **minor — s18 et s19 (notes) — ancien finding 4, toujours ouvert.** Aucune note ne prévoit d'effacer avec le compte (s15) les données que ces stories créent : le compteur et les combats lus pour s18, la référence client Stripe pour s19. s16 et s21 ont une telle note. La note de s15 ne couvre que les stories déjà livrées au moment de son exécution, donc pas celles-ci.
4. **minor — s19 (AC1) / exclusion « autres moyens de paiement que la carte » — ancien finding 6, toujours ouvert.** AC1 parle de « paiement par carte », mais aucun critère n'empêche la page Stripe de proposer d'autres moyens de paiement. Selon la configuration du compte Stripe, elle peut le faire. Un critère ou une note suffit.
5. **minor — s20 (AC1) — ancien finding 7, toujours ouvert.** « Voir mes factures » n'est pas dans le PRD. L'ajout est léger et souvent fourni par le portail Stripe, mais c'est au porteur de le confirmer.
6. **minor — s21 (AC2) — ancien finding 8, en partie corrigé.** L'unité est maintenant fixée (écart en %). Restent deux cas limites non définis : un combat sans conseil, et un premier essai sans essai précédent.
7. **minor — s18 (AC6) — ancien finding 3, en partie corrigé.** La note dit maintenant clairement quels outils sont décomptés. En revanche, ni s18 ni s19 ne disent ce qu'affiche le compteur « coachings restants » pour un abonné (par exemple « illimité »).
8. **minor — s12 (AC3) — ancien finding 11, toujours ouvert.** Quand un autre joueur a plusieurs combats comparables, on ne sait pas quel chiffre est retenu : le meilleur, le dernier ou une moyenne.
9. **minor — « difficulté » en Mythique+ (s02, s09, s12, s16, s21) — ancien finding 12, toujours ouvert.** Rien ne dit si le niveau de clé fait partie de la difficulté, alors que s09, s12 et s16 comparent « même boss, même difficulté ».
10. **minor — s19 (note « pas ouvert au public tant que s20 n'est pas livrée »).** Cette note corrige l'ancien finding 1, mais c'est une règle de mise en production, pas un critère. Si s19 est en production, rien n'y décrit ce qui empêche un visiteur de s'abonner avant s20. Le moyen relève de `/ztp-plan`, mais la règle doit y être reprise explicitement.

## Suivi de la revue précédente (`da7bbe9`)
| Ancien finding | État à `8efad11` |
|---|---|
| 1 major — s19 sans résiliation avant s20 | Corrigé : la note de s19 et `docs/lots.md` disent que l'abonnement n'est ouvert au public qu'après s20. Reste un point mineur : finding 10 |
| 2 major — s18 AC2 renvoyait à l'abonnement de s19 (référence en avant) | Corrigé : le texte est passé dans s19 AC8. Reste un point mineur : finding 1 |
| 3 minor — outils décomptés par s18, affichage pour un abonné | En partie corrigé : la note de s18 liste les outils. Reste l'affichage pour un abonné (finding 7) |
| 4 minor — suppression des données de s18 et s19 | Ouvert (finding 3) |
| 5 minor — compte déjà abonné qui relance « S'abonner » | Corrigé (s19 AC10) |
| 6 minor — carte uniquement | Ouvert (finding 4) |
| 7 minor — factures dans s20 | Ouvert (finding 5) |
| 8 minor — cas limites et unité dans s21 AC2 | En partie corrigé : l'unité est en %. Les cas limites restent ouverts (finding 6) |
| 9 minor — s17 après s04, en-tête contradictoire | Corrigé : l'en-tête reconnaît maintenant le délai entre s04 et s17 |
| 10 minor — unité de l'écart dans s09 AC2 | Corrigé (« écart en % ») |
| 11 minor — chiffre retenu dans s12 AC3 | Ouvert (finding 8) |
| 12 minor — difficulté en Mythique+ | Ouvert (finding 9) |

Les deux majors sont corrigés. 2 minors sont entièrement corrigés et 2 en partie. Il reste 10 minors, dont 2 nouveaux (findings 2 et 10) et 2 nés de corrections (findings 1 et 10).

## Verdict
Toutes les fonctions « In scope » du PRD à `9a95e5f` sont couvertes, et aucune story ne reprend une exclusion. Il n'y a ni cycle, ni référence en avant, ni 5 laissé entier, et chaque 4 indique son risque. Les ids sont uniques. Il ne reste que des minors : des cas limites et des valeurs à préciser. Ils peuvent se corriger dans `docs/stories.md` au fil de l'eau, ou en recherche pour la story concernée.

Max severity: minor
Stories ready: yes
