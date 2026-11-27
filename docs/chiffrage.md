# Chiffrage · WoW AI Coaching

## Grille

| Complexité | Jours-homme sans IA | Jours-homme avec IA | Pourquoi |
|---|---|---|---|
| 1 | 0,5 | 0,25 | CRUD trivial : un écran, une donnée, presque pas de règle |
| 2 | 2 | 1 | Formulaire, enregistrement et liste : chemin classique, peu d'états |
| 3 | 3 | 1,25 | Logique métier ou plusieurs états : calculs à justifier et à tester |
| 4 | 6,5 | 2,5 | Au-dessus de la référence (4 / 1,5) : format de combat log mal documenté, chiffres qui doivent rester justes face à Warcraft Logs, intégrations externes (MCP avec connexion par compte, API Warcraft Logs, paiement Stripe) |
| 5 | Interdite : à découper avant de chiffrer (règle de Zero to Prod) | — | — |

Forfaits : la Fondation (hors grille, ce n'est pas une story) est chiffrée au forfait — 5 j sans IA, 2 j avec IA.
Écart toléré au recalcul : 10 % · assez strict pour repérer une story mal notée, assez souple pour un arrondi.

## Stories

| Story | Complexité | Jours-homme sans IA | Jours-homme avec IA |
|---|---|---|---|
| Fondation | hors grille | 5 (choix de la stack et ADR, mise en place du projet, CI, déploiement, design system) | 2 (idem) |
| s01-sign-up-sign-in | 2 | 2 | 1 |
| s02-upload-log-list-fights | 4 | 6,5 | 2,5 |
| s03-choose-my-character | 2 | 2 | 1 |
| s04-fight-damage-by-player | 4 | 6,5 | 2,5 |
| s17-patch-compatibility | 2 | 2 | 1 |
| s05-fight-healing-by-player | 4 | 6,5 | 2,5 |
| s06-player-spell-breakdown | 3 | 3 | 1,25 |
| s07-fight-buffs-debuffs | 3 | 3 | 1,25 |
| s08-fight-timeline-chart | 3 | 3 | 1,25 |
| s09-boss-progress-history | 2 | 2 | 1 |
| s10-connect-ai-assistant | 4 | 6,5 | 2,5 |
| s11-ai-advice-on-fight | 3 | 3 | 1,25 |
| s12-compare-with-opt-in | 3 | 3 | 1,25 |
| s13-admin-manage-users | 3 | 3 | 1,25 |
| s14-admin-delete-log | 2 | 2 | 1 |
| s15-delete-my-account | 2 | 2 | 1 |
| s16-compare-with-top-players | 4 | 6,5 | 2,5 |
| s18-ai-coaching-weekly-limit | 3 | 3 | 1,25 |
| s19-subscribe | 4 | 6,5 | 2,5 |
| s20-manage-subscription | 4 | 6,5 | 2,5 |
| s21-share-fight-discord | 3 | 3 | 1,25 |
| Total | 21 stories (+ Fondation) | 86,5 (81,5 stories + 5 Fondation) | 35,5 (33,5 stories + 2 Fondation) |

## Par lot (`docs/lots.md`)

| Lot | Stories | Jours-homme sans IA | Jours-homme avec IA |
|---|---|---|---|
| Fondation (jalon 04, hors lot) | — | 5 | 2 |
| Lot initial | 18 stories (liste dans `docs/lots.md`) | 73,5 | 30 |
| Lot 2 — Administration | s13, s14 | 5 | 2,25 |
| Lot 3 — Partage Discord | s21 | 3 | 1,25 |
| Total | 21 stories (+ Fondation) | 86,5 | 35,5 |

Complexité revue : s13-admin-manage-users passée de 4 à 3 (un seul rôle admin, une liste et une désactivation de compte) · –3,5 j sans IA, –1,25 j avec IA (grille actuelle).

Ajout au périmètre (PRD `9a95e5f`) : abonnement (s18, s19, s20) et partage Discord (s21) · +19 j sans IA, +7,5 j avec IA (grille actuelle).

Prix client : 10 000 € (forfait de 40 j × TJM 250 €, entre 8 875 € et 21 625 €) · sous la valeur sans IA pour rester attractif, au-dessus du coût réel pour couvrir les risques connus : justesse des chiffres, patchs du jeu, conditions d'utilisation de l'API Warcraft Logs.
Recalcul à l'aveugle (jours-homme sans IA) : 81 j pour les 21 stories, Fondation exclue (contexte neuf, 2026-11-26), contre 57 j avec l'ancienne grille (2 → 1 j, 3 → 2 j, 4 → 5 j) : écart +42 %, hors tolérance · grille relevée à 2 / 3 / 6,5 j : 81,5 j · écart : +0,6 %, dans la tolérance (10 %)
