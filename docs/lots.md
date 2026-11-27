# Lots — WoW AI Coaching

> Découpage du backlog (`docs/stories.md`, s01 à s21) en lots. Chaque lot se livre en production
> et fonctionne seul. À l'intérieur d'un lot, les stories suivent l'ordre de leurs dépendances.
> Le backlog n'est pas figé : une fois le produit en ligne, un changement passe par `/ztp-intent`.

La Fondation (architecture, setup, CI, design system) ne fait partie d'aucun lot : elle se pose
au jalon 04.

## Lot initial — Première boucle de coaching

### Pourquoi
*(écrit par le porteur du projet)*

Je veux tester une première boucle : j'envoie mes logs, mes dégâts s'affichent, et j'ai quelques
conseils pour m'améliorer.

### La boucle
Je crée mon compte → j'envoie mon combat log → je choisis mon personnage → je vois mes dégâts et
mes soins, le détail de mes sorts, mes buffs et mon graphique → mon assistant IA lit le résumé de
mon combat et me conseille → le conseil reste sur le site → je refais le boss, je renvoie mon log
et je vois ma progression en %.

Autour de cette boucle, le lot livre aussi l'abonnement (3 coachings gratuits par semaine, ou
illimité à 5,99 €/mois), la comparaison avec les autres joueurs et avec les meilleurs, et la
suppression de compte.

### Stories
| Ordre | Story | Complexité | Dépend de | Jalon |
|---|---|---|---|---|
| 1 | s01-sign-up-sign-in | 2 | — | 05 |
| 2 | s02-upload-log-list-fights | 4 | s01 | 05 |
| 3 | s03-choose-my-character | 2 | s02 | 05 |
| 4 | s04-fight-damage-by-player | 4 | s02 | 05 |
| 5 | s17-patch-compatibility | 2 | s04 | 05 |
| 6 | s05-fight-healing-by-player | 4 | s04 | 06 |
| 7 | s06-player-spell-breakdown | 3 | s04, s05 | 06 |
| 8 | s07-fight-buffs-debuffs | 3 | s06 | 06 |
| 9 | s08-fight-timeline-chart | 3 | s04, s05 | 06 |
| 10 | s09-boss-progress-history | 2 | s03, s04, s05 | 06 |
| 11 | s10-connect-ai-assistant | 4 | s03, s06, s07, s08 | 06 |
| 12 | s11-ai-advice-on-fight | 3 | s09, s10 | 06 |
| 13 | s12-compare-with-opt-in | 3 | s09 | 06 |
| 14 | s15-delete-my-account | 2 | s01, s02, s10, s11, s12 | 06 |
| 15 | s16-compare-with-top-players | 4 | s09, s10 | 06 |
| 16 | s18-ai-coaching-weekly-limit | 3 | s10 | 06 |
| 17 | s19-subscribe | 4 | s01, s18 | 06 |
| 18 | s20-manage-subscription | 4 | s15, s19 | 06 |

L'abonnement n'est ouvert au public qu'une fois s20 livrée.

### Sortie du lot
*(choisie par le porteur du projet)*

Le lot est terminé quand, en production, j'ai envoyé un vrai log, que mes dégâts correspondent à
ceux de Warcraft Logs, et que mon IA m'a donné un conseil enregistré sur le site. Je refais ensuite
le même boss : si mon chiffre clé monte d'au moins 5 %, je continue. Sinon, j'améliore le résumé
lu par l'IA avant de coder le lot suivant.

## Lots suivants (optionnels)

| Lot | Objectif | Stories (dans l'ordre) |
|---|---|---|
| Lot 2 — Administration | Gérer les utilisateurs et retirer un log abusif | s13, s14 |
| Lot 3 — Partage Discord | Publier son combat dans un salon Discord | s21 |

## Vérification du lot initial

| Critère | Résultat |
|---|---|
| Une boucle qu'un utilisateur fait de bout en bout | ✅ Du compte au conseil de l'assistant IA, puis nouvel essai et progression en % |
| Toutes ses dépendances sont dans le lot | ✅ Chaque story listée en « Dépend de » est dans le lot, avant celle qui en a besoin |
| Le plus petit lot qui teste l'hypothèse du PRD | ⚠️ Agrandi volontairement. La boucle seule tient en 10 stories (s01 à s08, s17, s10). Les 8 autres (s09, s11, s12, s15, s16, s18, s19, s20) sont ajoutées pour avoir 5 stories de complexité 4 au jalon 06 : s16, s19 et s20, avec leurs dépendances. Décision du porteur |
| Utilisable seul, en production | ✅ Aucune story du lot ne demande une story hors du lot ; l'abonnement s'ouvre avec s20, dans le lot |
| Le « Pourquoi » est écrit par le porteur | ✅ |
| Garde des stories pour les jalons 05 et 06 | ✅ Jalon 05 : s01, puis s02, s03, s04 et s17 enchaînées. Jalon 06 : 5 stories de complexité 4 (s05, s10, s16, s19, s20), avec leurs dépendances |
| La Fondation n'en fait pas partie | ✅ Jalon 04 |
| Chaque story du backlog est dans un lot | ✅ 21 stories : 18 + 2 + 1 |
