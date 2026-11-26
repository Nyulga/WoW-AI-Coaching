# PRD — WoW AI Coaching

## Idea validation
### Problem
Les joueurs de World of Warcraft (Retail) qui veulent progresser utilisent aujourd'hui Warcraft Logs
(les chiffres détaillés d'un combat) et WoWAnalyzer (une analyse automatique). Ces outils montrent
des chiffres, mais n'expliquent pas concrètement quoi changer : il n'y a pas de vrai coaching.
Le joueur qui ne sait pas lire un log seul reste devant des tableaux sans savoir comment s'améliorer.

### Target users
Joueurs qui progressent et utilisent déjà un assistant IA (Claude, ChatGPT…) : ils font du raid ou
du donjon Mythique+ sur WoW Retail, veulent s'améliorer, mais ne savent pas interpréter un combat
log seuls. Le site les guide pour brancher leur assistant IA.

### Hypothesis to verify
Un joueur qui suit les conseils de son assistant IA, branché sur le site, améliore son chiffre clé (DPS ou HPS) entre deux logs
du même boss.
Signal mesurable : parmi au moins 10 joueurs qui envoient 2 logs du même boss après avoir lu les
conseils, au moins 70 % améliorent leur chiffre clé.
Non vérifiée avant de construire.

## Starting point
- Situation: new project
- Product type: web application

## Reference
- Warcraft Logs — site où l'on envoie son fichier de combat log pour voir en détail dégâts, soins,
  buffs et debuffs. On n'en reprend pas : les classements mondiaux (rankings / parses), le replay
  visuel du combat, l'envoi en direct (live logging).
- WoWAnalyzer — analyse automatique d'un log avec suggestions. On en reprend l'idée d'analyse ;
  le coaching passe ici par l'assistant IA du joueur, branché sur le site.

## Perimeter
### In scope (core loop)
| Feature | Complexity (1-5) | Why this score |
|---|---|---|
| Comptes utilisateur : inscription, connexion | 2 | Formulaire et enregistrement classiques |
| Comptes administrateurs : gérer les utilisateurs et les logs | 4 | Rôles et droits distincts ; gardé pour protéger un site public |
| Envoi et lecture du fichier combat log officiel de WoW Retail | 5 | Format externe, fichiers très volumineux ; gardé car c'est la base de toute la valeur |
| Vue détaillée d'un combat : dégâts, soins, buffs/debuffs, graphiques | 4 | Nombreux types d'événements, rattachement des familiers à leur joueur, absorptions et surplus de soin, graphiques dans le temps |
| Historique de mes logs et progression sur un même boss | 2 | Liste et comparaison simples ; nécessaire pour mesurer l'hypothèse |
| Accès MCP pour l'assistant IA du joueur : il lit le résumé de ses combats, renvoie des conseils concrets, que le site affiche et garde dans l'historique | 4 | Intégration externe (serveur MCP) avec connexion par compte ; c'est le cœur de la valeur |
| Comparaison avec d'autres utilisateurs du site sur le même boss | 3 | Logique de comparaison entre joueurs |

Scale: 1 trivial CRUD · 2 form + persistence + list · 3 business logic / several states · 4 integrations, payments, roles · 5 real-time, migrations, external systems. A 5 is an out-of-scope candidate — keep it only if it IS the core value.

### Out of scope
- Classements mondiaux (rankings / parses en percentiles)
- Replay visuel du combat (carte animée des positions)
- Envoi des logs en direct pendant le raid (live logging)
- API Warcraft Logs (comparaison avec des logs externes) — repoussée à une version 2
- Versions du jeu autres que Retail (Classic, etc.)
- Paiement, abonnement, offre premium — dont l'abonnement qui réservera plus tard l'accès MCP aux abonnés
- IA payée ou hébergée par le site : l'analyse est faite par l'assistant IA du joueur
- Application mobile (iOS / Android)
- Bot Discord et partage automatique vers d'autres plateformes
- Gestion de guilde (roster, inscriptions aux raids, gestion d'équipe)

### Differentiation
Au lieu de tableaux de chiffres, l'assistant IA du joueur, branché sur le site, dit en langage simple quoi changer au prochain essai,
et l'historique montre si ça a marché.

## Constraints
- Budget très serré : hébergement et stockage gratuits ou presque. Aucun coût d'IA supporté par le site.
- Construit seul, par un non-développeur avec un agent (formation Ultra) : la technique doit rester
  simple à maintenir.
- RGPD : comptes avec e-mail d'utilisateurs européens — données personnelles protégées, droit de
  suppression du compte.

## Success criteria
- Tout le périmètre « In scope » est livré et fonctionne en production.
- Signal de l'hypothèse : au moins 10 joueurs envoient 2 logs du même boss après avoir lu les
  conseils de leur assistant IA, et au moins 70 % d'entre eux améliorent leur chiffre clé (DPS ou HPS).
