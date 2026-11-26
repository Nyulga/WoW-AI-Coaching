# Stories Review — WoW AI Coaching

> Revue à contexte neuf de `docs/stories.md` par rapport à `docs/prd.md` et aux intents acceptées qui y sont liées. Chaque problème est classé critical / major / minor.

Sources relues : `docs/prd.md`, dont la dernière modification est le commit `9a95e5f` (abonnement et partage Discord passés « In scope »), et `docs/stories.md`, dont la dernière modification est le commit `da7bbe9` (s18 à s21, semaine qui repart le mercredi). Les deux fichiers ont été lus sur le disque. J'ai vérifié deux choses dans `.git` : `HEAD` pointe sur `refs/heads/dev`, et `dev` pointe sur `da7bbe9c4eabf2a065dfce44e94f9943d91145b7`. Selon l'état git fourni, le seul fichier non suivi est `docs/lots.md` : le disque correspond donc à `da7bbe9`. Le contenu des commits n'a pas pu être comparé, faute de shell. Aucune intent n'est liée : les 21 stories indiquent toutes `Source: docs/prd.md`, et `docs/intents/` n'existe pas. La revue couvre toute la liste, s01 à s21. Je n'ai lu la revue précédente (`docs/reviews/stories.md`) qu'après mon propre examen, pour savoir si ses findings ont été corrigés : ses 4 minors sont toujours ouverts (findings 9 à 12).

## Perimeter coverage
| PRD feature / accepted intent outcome | Covered by | OK? |
|---|---|---|
| Comptes utilisateur : inscription, connexion | s01-sign-up-sign-in | ✅ |
| Suppression de mon compte et de toutes mes données (RGPD) | s15-delete-my-account (étendue ensuite par s16, s20 AC5, s21) | ✅ (voir findings 1 et 4) |
| Comptes administrateurs : gérer les utilisateurs et les logs | s13-admin-manage-users, s14-admin-delete-log | ✅ |
| Envoi et lecture du combat log Retail (5 dans le PRD) | s02 (envoi, liste, refus), puis s04 à s08 pour les chiffres | ✅ (le 5 est découpé) |
| Compatibilité avec le patch | s17-patch-compatibility (+ s02 AC4) | ✅ |
| Vue détaillée d'un combat : dégâts, soins, buffs/debuffs, graphiques | s04, s05, s06, s07, s08 | ✅ |
| Historique de mes logs et progression sur un même boss | s09 (+ s03) | ✅ |
| Accès MCP : lecture du résumé, conseils affichés et gardés | s10, s11 | ✅ |
| Comparaison avec d'autres utilisateurs (opt-in) | s12 | ✅ |
| Comparaison aux meilleurs de ma spé via l'API Warcraft Logs | s16 | ✅ |
| Abonnement à 5 €/mois pour un coaching illimité ; 3 coachings/semaine sans abonnement ; lister ses combats reste gratuit et illimité ; l'abonnement à l'assistant IA n'est pas compris ; résiliation et paiement refusé | s18 (limite de 3, AC5 : la liste reste libre), s19 (abonnement, AC2 : illimité, AC7 : assistant non compris), s20 (résiliation, renouvellement, paiement refusé) | ✅ |
| Partage sur Discord : chiffre clé, progression et dernier conseil dans un salon choisi, sans bot, message clair si Discord ne répond pas | s21-share-fight-discord | ✅ |

- [x] Chaque fonction du tableau « In scope (core loop) » du PRD est livrée par au moins une story.
- [x] Chaque intent liée est acceptée et son résultat couvert : sans objet, aucune intent n'est liée.

## Scope
- [x] Aucune exclusion du PRD n'est modifiée sans décision d'intent : l'abonnement et Discord sont entrés dans le périmètre par le PRD lui-même (`9a95e5f`), pas en levant une exclusion.
- [x] Aucune story ne sort du périmètre, avec deux réserves mineures (findings 6 et 7). Points vérifiés :
  - **Discord** : s21 passe par un webhook (une adresse fournie par Discord qui permet de publier dans un salon). Il n'y a ni bot ni liaison de compte Discord, ce que sa note rappelle. Pas d'autre plateforme.
  - **Abonnement** : un seul niveau, pas de paiement à l'unité. Le site ne stocke aucune donnée de carte (note de s19). s20 exclut le remboursement, le changement de prix et les niveaux multiples.
  - **Coaching** : aucune IA payée par le site.
  - Le « mercredi à 0 h, heure de Paris » (s18 AC4) précise la « semaine » du PRD : ce n'est pas un ajout de périmètre.

## Story quality
- [x] Chaque story est une tranche livrable de bout en bout, pas une couche technique. Réserve sur s18 et s19 livrées seules : findings 1 et 2.
- [x] Chaque critère d'acceptation peut devenir un test. Il reste des valeurs attendues imprécises : findings 3, 8, 10 et 11.
- [x] Les notes pour l'agent sont présentes et utiles. Il manque une note sur la suppression des données dans s18 et s19 (finding 4).
- [x] Complexité notée, aucun 5 laissé entier, chaque 4 indique son risque. Les 4 sont s02, s04, s05, s10, s16, s19 et s20 ; chacun a une ligne « Risque (4) ». s13 est passée à 3 et garde sa note de risque.

## The list as a whole
- [x] Ordre des dépendances exécutable, sans cycle ni référence en avant. L'ordre vérifié est : s01, s02, s03, s04, s17, s05, s06, s07, s08, s09, s10, s11, s12, s13, s14, s15, s16, s18, s19, s20, s21. Chaque dépendance déclarée arrive avant la story qui en a besoin. Exception : une référence en avant dans un critère de s18 (finding 2).
- [x] Ids bien formés (`s<number>-<slug>`), uniques et stables : s01 à s21, sans doublon.
- [x] Pas de chevauchement entre stories. Les frontières sont nettes :
  - s18 décompte la limite, s19 la lève, s20 gère la suite de l'abonnement.
  - s21 réutilise la progression de s09 et le conseil de s11 sans les refaire.
  - s20 AC5 étend s15 sans la dupliquer.

## Findings
1. **major — s19-subscribe / s20-manage-subscription.** s19 met en production un prélèvement mensuel, alors que la résiliation (s20 AC1-AC2) et l'arrêt des prélèvements à la suppression du compte (s20 AC5) n'arrivent qu'avec s20. Entre les deux mises en production, un abonné ne peut pas arrêter de payer. S'il supprime son compte (s15 déjà en production), les prélèvements continuent alors que son compte n'existe plus. Le PRD promet pourtant « Suppression de mon compte et de toutes mes données ». Corrections possibles : déplacer s20 AC5 dans s19 (avec au moins une résiliation) ; ou écrire noir sur blanc que l'abonnement n'est pas ouvert au public avant que s20 soit livrée, comme la note de s19 le fait déjà pour le statut légal.
2. **major — s18-ai-coaching-weekly-limit (AC2).** Le message de refus doit dire « comment s'abonner ». Or l'abonnement n'existe qu'avec s19, placée après s18 et qui dépend de s18 : c'est une référence en avant. Faire dépendre s18 de s19 créerait un cycle. Livrée seule, s18 limite aussi le coaching sans aucun moyen de lever la limite. Correction : retirer « comment s'abonner » de s18 AC2 et l'ajouter au message de refus dans s19. Ou bien indiquer que s18 et s19 passent en production ensemble.
3. **minor — s18 (AC1, AC5, AC6).** Seul l'outil « lire le résumé d'un combat » est décompté. Les critères ne disent pas si l'outil « comparer aux meilleurs » (s16), qui renvoie aussi des chiffres sur un combat, compte comme un coaching ou contourne la limite. Rien n'est dit non plus de « enregistrer un conseil » (s11). Et AC6 ne dit pas ce que la page affiche pour un abonné (par exemple « illimité »), ni s19.
4. **minor — s18 et s19 (notes).** Contrairement à s16 et s21, aucune note ne prévoit d'effacer avec le compte (s15) les données qu'elles créent : le compteur et la liste des combats lus pour s18, la référence client et abonnement Stripe pour s19. La note de s15 ne vise que les stories déjà livrées au moment de son exécution, donc pas celles-ci.
5. **minor — s19 (AC1).** Rien ne dit ce qui se passe si un compte déjà abonné relance « S'abonner » (deux abonnements payés ?). AC5 ne couvre que la même confirmation Stripe reçue deux fois.
6. **minor — s19 (AC1) / exclusion « autres moyens de paiement que la carte ».** Aucun critère ni note n'impose que la page de paiement de Stripe ne propose que la carte. Or elle peut afficher d'autres moyens selon la configuration du compte Stripe. Un test ou une note suffit à fermer ce risque de sortie du périmètre.
7. **minor — s20 (AC1).** « Voir mes factures » ne figure pas dans le PRD. L'ajout est léger (souvent fourni par le portail Stripe) et sans doute utile légalement, mais c'est au porteur de le confirmer.
8. **minor — s21 (AC2).** Les cas limites ne sont pas fixés : un combat sans conseil, un premier essai sans essai précédent. L'unité de « l'écart avec mon essai précédent » n'est pas donnée non plus (même imprécision que le finding 10).
9. **minor — s17 / en-tête du lot initial** (finding 1 de la revue précédente, non corrigé). s04 est livrée avant s17. Pendant une story, des chiffres sont donc affichés sans contrôle de version, alors que l'en-tête dit le contraire.
10. **minor — s09 (AC2)** (finding 2 précédent, non corrigé). L'écart avec l'essai précédent n'a pas d'unité (valeur ou %), alors que l'hypothèse du PRD se mesure en % (+5 %).
11. **minor — s12 (AC3)** (finding 3 précédent, non corrigé). Quand un joueur a plusieurs combats comparables, on ne sait pas quel chiffre est retenu (meilleur, dernier ou moyenne).
12. **minor — « difficulté » en Mythique+ (s02, s09, s12, s16, et maintenant s21)** (finding 4 précédent, non corrigé). Rien ne dit si le niveau de clé fait partie de la difficulté.

## Verdict
Toutes les fonctions « In scope » du PRD au commit `9a95e5f` sont couvertes, abonnement et partage Discord compris, et rien ne reprend une exclusion. Il n'y a ni cycle ni 5 laissé entier, et chaque 4 indique son risque. Deux majors portent sur l'ordre de mise en production de l'abonnement (findings 1 et 2) : ils se corrigent dans `docs/stories.md` avant d'ouvrir l'abonnement au public, et le mieux est de le faire avant `/ztp-architect`. Il reste 10 minors, dont les 4 de la revue précédente toujours ouverts.

Max severity: major
Stories ready: yes
