# PRD — Warcraftlog 2

## Idea validation
### Problem
Les joueurs de World of Warcraft (Retail) qui veulent progresser utilisent aujourd'hui Warcraft Logs
(les chiffres détaillés d'un combat) et WoWAnalyzer (une analyse automatique). Ces outils montrent
des chiffres, mais n'expliquent pas concrètement quoi changer : il n'y a pas de vrai coaching.
Le joueur qui ne sait pas lire un log seul reste devant des tableaux sans savoir comment s'améliorer.

### Target users
Joueurs moyens qui progressent : ils font du raid ou du donjon Mythique+ sur WoW Retail, veulent
s'améliorer, mais ne savent pas interpréter un combat log seuls.

### Hypothesis to verify
Un joueur qui suit les conseils du coach IA améliore son chiffre clé (DPS ou HPS) entre deux logs
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
  le coaching passe ici par un agent IA.

## Perimeter
### In scope (core loop)
| Feature | Complexity (1-5) | Why this score |
|---|---|---|
| Comptes utilisateur : inscription, connexion | 2 | Formulaire et enregistrement classiques |
| Comptes administrateurs : gérer les utilisateurs et les logs | 4 | Rôles et droits distincts ; gardé pour protéger un site public |
| Envoi et lecture du fichier combat log officiel de WoW Retail | 5 | Format externe, fichiers très volumineux ; gardé car c'est la base de toute la valeur |
| Vue détaillée d'un combat : dégâts, soins, buffs/debuffs, graphiques | 3 | Nombreux calculs et plusieurs vues |
| Historique de mes logs et progression sur un même boss | 2 | Liste et comparaison simples ; nécessaire pour mesurer l'hypothèse |
| Coach IA : conseils personnalisés et concrets sur un combat | 4 | Intégration d'une IA externe ; c'est le cœur de la valeur |
| Comparaison avec d'autres utilisateurs du site sur le même boss | 3 | Logique de comparaison entre joueurs |

Scale: 1 trivial CRUD · 2 form + persistence + list · 3 business logic / several states · 4 integrations, payments, roles · 5 real-time, migrations, external systems. A 5 is an out-of-scope candidate — keep it only if it IS the core value.

### Out of scope
- Classements mondiaux (rankings / parses en percentiles)
- Replay visuel du combat (carte animée des positions)
- Envoi des logs en direct pendant le raid (live logging)
- API Warcraft Logs (comparaison avec des logs externes) — repoussée à une version 2
- Versions du jeu autres que Retail (Classic, etc.)
- Paiement, abonnement, offre premium
- Application mobile (iOS / Android)
- Bot Discord et partage automatique vers d'autres plateformes
- Gestion de guilde (roster, inscriptions aux raids, gestion d'équipe)

### Differentiation
Au lieu de tableaux de chiffres, un coach IA dit en langage simple quoi changer au prochain essai,
et l'historique montre si ça a marché.

## Constraints
- Budget très serré : hébergement et stockage gratuits ou presque, coût de l'IA par analyse très bas.
- Construit seul, par un non-développeur avec un agent (formation Ultra) : la technique doit rester
  simple à maintenir.
- RGPD : comptes avec e-mail d'utilisateurs européens — données personnelles protégées, droit de
  suppression du compte.

## Success criteria
- Tout le périmètre « In scope » est livré et fonctionne en production.
- Signal de l'hypothèse : au moins 10 joueurs envoient 2 logs du même boss après avoir lu les
  conseils du coach, et au moins 70 % d'entre eux améliorent leur chiffre clé (DPS ou HPS).
