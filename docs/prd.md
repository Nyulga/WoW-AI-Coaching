# PRD — WoW AI Coaching

## Idea validation
### Problem
Les joueurs de World of Warcraft (Retail) qui veulent progresser utilisent aujourd'hui Warcraft Logs
(les chiffres détaillés d'un combat) et WoWAnalyzer (une analyse automatique). Ces outils montrent
des chiffres, mais n'expliquent pas concrètement quoi changer : il n'y a pas de vrai coaching.
Le joueur qui ne sait pas lire un log seul reste devant des tableaux sans savoir comment s'améliorer.
Ce que ça lui coûte aujourd'hui :
- du temps : à lire ses logs, des guides ou des vidéos sans savoir quoi changer ;
- des essais ratés : il répète des échecs sur un boss sans comprendre son erreur ;
- sa place en groupe : des performances faibles le font refuser en groupe ou en raid.

### Target users
Joueurs qui progressent et utilisent déjà un assistant IA (Claude, ChatGPT…) : ils font du raid ou
du donjon Mythique+ sur WoW Retail, veulent s'améliorer, mais ne savent pas interpréter un combat
log seuls. Le site les guide pour brancher leur assistant IA.

Premier utilisateur réel : le porteur du projet en personne — joueur WoW Retail qui veut
progresser et utilise déjà un assistant IA.

### Hypothesis to verify
Un joueur (hors tank) qui a reçu un conseil de son assistant IA sur un essai améliore son chiffre
clé (DPS ou HPS) d'au moins 5 % sur un essai suivant du même boss et de la même difficulté, dans
les 30 jours.
Signal mesurable : parmi au moins 10 joueurs (hors tanks) ayant un conseil enregistré sur un
premier essai puis un second essai du même boss et de la même difficulté dans les 30 jours, au
moins 70 % améliorent leur chiffre clé d'au moins 5 %.
Non vérifiée avant de construire.

## Starting point
- Situation: new project
- Product type: web application

## Reference
- Warcraft Logs — site où l'on envoie son fichier de combat log pour voir en détail dégâts, soins,
  buffs et debuffs. On n'en reprend pas : les classements mondiaux (rankings / parses), le replay
  visuel du combat, l'envoi en direct (live logging). On utilise son API uniquement pour comparer
  un combat aux meilleurs joueurs de la même spécialisation.
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
| Comparaison avec d'autres utilisateurs du site sur le même boss, uniquement entre joueurs qui ont donné leur accord (opt-in) | 3 | Logique de comparaison entre joueurs et réglage d'accord par joueur |
| Comparaison de mon combat aux meilleurs joueurs de ma spécialisation sur le même boss, via l'API Warcraft Logs (affichée sur le site et lisible par l'assistant IA) | 4 | Intégration d'une API tierce, conditions d'utilisation et disponibilité à vérifier ; apporte au coaching ce que font les meilleurs |

Scale: 1 trivial CRUD · 2 form + persistence + list · 3 business logic / several states · 4 integrations, payments, roles · 5 real-time, migrations, external systems. A 5 is an out-of-scope candidate — keep it only if it IS the core value.

### Out of scope
- Classements mondiaux (rankings / parses en percentiles)
- Replay visuel du combat (carte animée des positions)
- Envoi des logs en direct pendant le raid (live logging)
- Tout autre usage de l'API Warcraft Logs que la comparaison aux meilleurs joueurs de sa
  spécialisation (import de rapports, classements, etc.) — repoussé à une version 2
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
  suppression du compte. Les combats d'un joueur ne sont visibles par d'autres utilisateurs qu'avec
  son accord explicite (opt-in), qu'il peut retirer à tout moment.
- Dépendance : accès à l'API Warcraft Logs (clé d'accès, conditions d'utilisation, limites d'appels).
  Si ses conditions d'utilisation interdisent notre usage, la comparaison aux meilleurs joueurs sort
  du périmètre et une autre intégration externe est choisie.

## Success criteria
- Tout le périmètre « In scope » est livré et fonctionne en production.
- Signal de l'hypothèse : au moins 10 joueurs (hors tanks) ont un conseil enregistré sur un premier
  essai puis un second essai du même boss et de la même difficulté dans les 30 jours, et au moins
  70 % d'entre eux améliorent leur chiffre clé (DPS ou HPS) d'au moins 5 %.
- Testeurs recrutés dans la guilde du porteur du projet et sur des serveurs Discord WoW.
