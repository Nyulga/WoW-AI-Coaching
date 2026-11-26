# User Stories — WoW AI Coaching

> One story = one shippable slice, written to be executed by an agent.
> Id format: `s<number>-<short-slug>` — reused in every pipeline file and in the branch name.

**Lot initial livrable seul** (première boucle de valeur, jusqu'au coaching) : s01, s02, s03, s04,
s05, s10, s11. s10 dépend aussi de s06, s07, s08 : soit ces stories rejoignent le lot, soit le
résumé lu par l'assistant se limite d'abord aux dégâts et soins — à trancher en revue des stories.

Pièges communs à toutes les stories qui lisent un log :
- **Dépôt GitHub public** : un vrai combat log contient les noms de vrais personnages. Ne jamais
  committer un log réel ; les tests utilisent de courts extraits avec des noms fictifs.
- Le format du combat log change avec les patchs du jeu (ligne `COMBAT_LOG_VERSION` en tête de
  fichier) : la version supportée est à fixer en recherche, à partir des logs d'exemple du porteur.
- Les fichiers réels pèsent de quelques centaines de Mo à plus d'1 Go ; où se fait la lecture
  (navigateur ou serveur) est une décision d'architecture, sous contrainte de budget très serré.
- Référence : Warcraft Logs sert de comparaison pour valider les chiffres sur un même log.

## Story s01-sign-up-sign-in — Créer un compte et se connecter
Source: docs/prd.md

**As a** visiteur **I want** créer un compte et me connecter **so that** mes logs et mes conseils me soient rattachés.

### Complexity
2

### Acceptance criteria
- [ ] AC1 — Une inscription avec un e-mail et un mot de passe valides crée le compte et connecte l'utilisateur.
- [ ] AC2 — Une inscription avec un e-mail déjà utilisé est refusée avec un message, et aucun compte n'est créé.
- [ ] AC3 — Une connexion avec les bons identifiants ouvre la session.
- [ ] AC4 — Une connexion avec un mauvais mot de passe est refusée avec un message qui ne révèle pas si l'e-mail existe.
- [ ] AC5 — Une page réservée visitée sans être connecté redirige vers la connexion.
- [ ] AC6 — La déconnexion ferme la session : une page réservée redirige ensuite vers la connexion.

### Dependencies
Aucune.

### Agentic notes
- RGPD : ne collecter que l'e-mail ; mot de passe jamais stocké en clair.
- Aucun secret dans le dépôt : les clés vont dans `.env.local` (ignoré par Git).
- Hors PRD : connexion via Battle.net/Google, réinitialisation du mot de passe.

## Story s02-upload-log-list-fights — Envoyer un combat log et voir ses combats de boss
Source: docs/prd.md

**As a** joueur **I want** envoyer mon fichier de combat log officiel **so that** je voie les combats de boss qu'il contient et puisse en choisir un.

### Complexity
4

### Acceptance criteria
- [ ] AC1 — Un combat log Retail valide envoyé apparaît dans « Mes logs » avec la liste de ses combats de boss : boss, difficulté, résultat (victoire ou échec), durée, date.
- [ ] AC2 — Plusieurs essais du même boss dans un log sont listés séparément, dans l'ordre chronologique.
- [ ] AC3 — Un fichier qui n'est pas un combat log WoW est refusé avec un message clair, et rien n'est enregistré.
- [ ] AC4 — Un combat log d'une autre version du jeu que Retail est refusé avec un message clair, et rien n'est enregistré.
- [ ] AC5 — Un combat log sans aucun combat de boss est refusé avec le message « aucun combat de boss trouvé », et rien n'est enregistré.
- [ ] AC6 — Un log n'est visible que par son propriétaire : un autre utilisateur connecté qui ouvre son adresse obtient « introuvable ».
- [ ] AC7 — Un log réel de plusieurs centaines de Mo est traité jusqu'au bout et ses combats sont listés.

### Dependencies
s01-sign-up-sign-in.

### Agentic notes
- **Risque (4)** : taille des fichiers et budget. AC7 se vérifie avec un log réel fourni par le porteur, jamais committé.
- Combats de boss repérés par `ENCOUNTER_START` / `ENCOUNTER_END` (boss, difficulté, succès) ; un donjon Mythique+ est encadré par `CHALLENGE_MODE_START` / `CHALLENGE_MODE_END`.
- Conserver ou non le fichier brut après lecture : décision d'architecture (coût de stockage).
- Équivalent dans la référence : la liste des combats d'un rapport Warcraft Logs.

## Story s03-choose-my-character — Indiquer quel personnage est le mien
Source: docs/prd.md

**As a** joueur **I want** indiquer quel personnage du log est le mien **so that** mon historique, mes conseils et mes comparaisons portent sur moi.

### Complexity
2

### Acceptance criteria
- [ ] AC1 — Après l'envoi d'un log, la liste des joueurs du log est proposée et je choisis mon personnage.
- [ ] AC2 — Sur un log suivant contenant un personnage de même nom et même royaume, il est choisi automatiquement.
- [ ] AC3 — Je peux changer le personnage choisi pour un log.

### Dependencies
s02-upload-log-list-fights.

### Agentic notes
- Un personnage s'identifie par nom + royaume (format `Nom-Royaume` dans le log).
- Les autres joueurs du log restent visibles dans les vues du combat, mais seul « mon » personnage alimente l'historique, l'assistant IA et la comparaison.

## Story s04-fight-damage-by-player — Voir les dégâts de chaque joueur sur un combat
Source: docs/prd.md

**As a** joueur **I want** voir les dégâts et le DPS de chaque joueur sur un combat **so that** je sache où je me situe.

### Complexity
4

### Acceptance criteria
- [ ] AC1 — La page d'un combat affiche chaque joueur avec sa classe, sa spécialisation, ses dégâts totaux et son DPS (dégâts / durée du combat), du plus haut au plus bas.
- [ ] AC2 — Les dégâts des familiers et invocations d'un joueur sont comptés dans son total, et n'apparaissent pas comme un joueur à part.
- [ ] AC3 — Sur un extrait de log aux totaux connus, les dégâts affichés sont exactement ceux attendus.
- [ ] AC4 — Les dégâts survenus hors de la fenêtre du combat (avant son début ou après sa fin) ne sont pas comptés.

### Dependencies
s02-upload-log-list-fights.

### Agentic notes
- **Risque (4)** : justesse des chiffres. Rattachement des familiers via `SPELL_SUMMON` et le propriétaire indiqué dans les champs du « combat log avancé ».
- Types de dégâts : `SWING_DAMAGE`, `SPELL_DAMAGE`, `SPELL_PERIODIC_DAMAGE`, `RANGE_DAMAGE`, ainsi que les cas particuliers (`DAMAGE_SPLIT`, `SPELL_DAMAGE_SUPPORT` de l'Évocateur Augmentation) : règle à fixer en recherche.
- Classe et spécialisation : `COMBATANT_INFO` en début de combat.
- Référence : onglet « Damage Done » de Warcraft Logs sur le même log.

## Story s05-fight-healing-by-player — Voir les soins de chaque joueur sur un combat
Source: docs/prd.md

**As a** joueur **I want** voir les soins, le HPS et le surplus de soin de chaque joueur **so that** je juge l'efficacité des soigneurs.

### Complexity
4

### Acceptance criteria
- [ ] AC1 — La page d'un combat affiche, pour chaque joueur, ses soins effectifs, son HPS et son surplus de soin séparé.
- [ ] AC2 — Le surplus de soin n'est pas compté dans les soins effectifs.
- [ ] AC3 — Les dégâts absorbés par un bouclier sont comptés dans les soins du joueur qui a posé le bouclier.
- [ ] AC4 — Les soins des familiers et invocations sont comptés dans le total de leur maître.

### Dependencies
s04-fight-damage-by-player.

### Agentic notes
- **Risque (4)** : absorptions (`SPELL_ABSORBED`) attribuées au poseur du bouclier ; surplus présent dans `SPELL_HEAL` / `SPELL_PERIODIC_HEAL`.
- Réutiliser le rattachement des familiers construit en s04.
- Référence : onglet « Healing » de Warcraft Logs.

## Story s06-player-spell-breakdown — Voir le détail des sorts d'un joueur
Source: docs/prd.md

**As a** joueur **I want** ouvrir un joueur sur un combat et voir ses sorts un par un **so that** je comprenne d'où viennent ses dégâts ou ses soins.

### Complexity
3

### Acceptance criteria
- [ ] AC1 — Ouvrir un joueur affiche ses sorts avec, pour chacun : montant, part du total en %, nombre de coups et taux de critiques.
- [ ] AC2 — La somme des sorts d'un joueur est égale à son total affiché en s04 (dégâts) et en s05 (soins).

### Dependencies
s04-fight-damage-by-player, s05-fight-healing-by-player.

### Agentic notes
- Les sorts des familiers apparaissent dans le détail du maître, repérables comme tels.
- Référence : vue d'un joueur dans Warcraft Logs.

## Story s07-fight-buffs-debuffs — Voir les buffs et debuffs d'un joueur
Source: docs/prd.md

**As a** joueur **I want** voir les buffs reçus et les debuffs posés par un joueur avec leur temps de présence **so that** je repère un buff mal entretenu.

### Complexity
3

### Acceptance criteria
- [ ] AC1 — Pour un joueur, la liste de ses buffs reçus s'affiche avec leur temps de présence en % de la durée du combat.
- [ ] AC2 — La liste des debuffs que le joueur a posés sur les ennemis s'affiche avec leur temps de présence en %.
- [ ] AC3 — Un buff déjà actif au début du combat est compté depuis le début du combat.
- [ ] AC4 — Un buff retiré puis réappliqué a pour temps de présence la somme de ses périodes actives.

### Dependencies
s02-upload-log-list-fights.

### Agentic notes
- Événements `SPELL_AURA_APPLIED`, `SPELL_AURA_REMOVED`, `SPELL_AURA_REFRESH`, et cumuls (`…_DOSE`).
- Piège : un buff actif avant le combat peut n'apparaître que par son `SPELL_AURA_REMOVED`.
- Référence : onglets « Buffs » / « Debuffs » de Warcraft Logs.

## Story s08-fight-timeline-chart — Voir le DPS et le HPS au fil du combat
Source: docs/prd.md

**As a** joueur **I want** un graphique de DPS et de HPS au fil du combat **so that** je voie les moments où je baisse.

### Complexity
3

### Acceptance criteria
- [ ] AC1 — La page d'un combat affiche un graphique du DPS de chaque joueur par tranches de temps régulières.
- [ ] AC2 — Le même graphique est disponible pour le HPS.
- [ ] AC3 — Je peux masquer ou afficher chaque joueur dans le graphique.
- [ ] AC4 — Pour un joueur, la somme des tranches du graphique est égale à son total affiché en s04 / s05.

### Dependencies
s04-fight-damage-by-player, s05-fight-healing-by-player.

### Agentic notes
- La taille des tranches (par ex. 1 s ou 5 s) se décide en conception.

## Story s09-boss-progress-history — Suivre ma progression sur un boss
Source: docs/prd.md

**As a** joueur **I want** voir mes logs et mes essais successifs sur un même boss **so that** je sache si je progresse.

### Complexity
2

### Acceptance criteria
- [ ] AC1 — « Mes logs » liste mes logs du plus récent au plus ancien, avec leur date et leur nombre de combats.
- [ ] AC2 — Pour un boss et une difficulté, mes essais successifs s'affichent avec mon chiffre clé et l'écart avec l'essai précédent.
- [ ] AC3 — Le chiffre clé est le HPS si mon personnage avait une spécialisation de soigneur sur ce combat, sinon le DPS.
- [ ] AC4 — Seuls les essais de mon personnage (choisi en s03) apparaissent.

### Dependencies
s03-choose-my-character, s04-fight-damage-by-player, s05-fight-healing-by-player.

### Agentic notes
- C'est ce qui mesure l'hypothèse du PRD (progression entre 2 logs du même boss).

## Story s10-connect-ai-assistant — Brancher mon assistant IA sur le site
Source: docs/prd.md

**As a** joueur qui utilise un assistant IA **I want** brancher mon assistant sur le site **so that** il lise le résumé de mes combats.

### Complexity
4

### Acceptance criteria
- [ ] AC1 — Une page « Brancher mon assistant IA » donne l'adresse du serveur MCP et les étapes pour l'assistant retenu à l'architecture.
- [ ] AC2 — Un assistant qui se branche doit passer par la connexion au compte du joueur ; sans elle, aucun outil ne renvoie de données.
- [ ] AC3 — L'outil « lister mes combats » ne renvoie que les combats du joueur connecté (boss, difficulté, résultat, date).
- [ ] AC4 — L'outil « lire le résumé d'un combat » renvoie le résumé chiffré de mon personnage : dégâts ou soins, détail des sorts, buffs/debuffs, DPS/HPS dans le temps.
- [ ] AC5 — Demander le résumé d'un combat d'un autre joueur renvoie « introuvable ».
- [ ] AC6 — Le résumé ne contient jamais les événements bruts du log.
- [ ] AC7 — Depuis le site, je peux couper l'accès de mon assistant ; ensuite, ses appels sont refusés.

### Dependencies
s03-choose-my-character, s06-player-spell-breakdown, s07-fight-buffs-debuffs, s08-fight-timeline-chart.

### Agentic notes
- **Risque (4)** : sécurité et compatibilité. Connexion par compte d'un serveur MCP distant, prise en charge réelle par les assistants visés (Claude, ChatGPT…) à vérifier en recherche, cloisonnement strict des données entre joueurs.
- Le site ne paie aucune IA : il ne fait qu'exposer des outils.
- Le résumé doit rester court : il est lu par une IA dont la mémoire de travail est limitée.

## Story s11-ai-advice-on-fight — Recevoir et garder les conseils de mon assistant
Source: docs/prd.md

**As a** joueur **I want** que mon assistant enregistre ses conseils sur le site **so that** je les retrouve sur le combat et dans ma progression.

### Complexity
3

### Acceptance criteria
- [ ] AC1 — L'outil « enregistrer un conseil » attache un texte au combat indiqué, au nom du joueur connecté.
- [ ] AC2 — La page du combat affiche les conseils reçus, avec leur date.
- [ ] AC3 — Enregistrer un conseil sur le combat d'un autre joueur est refusé.
- [ ] AC4 — Un conseil contenant du code ou du balisage s'affiche comme du texte, sans être exécuté.
- [ ] AC5 — Dans ma progression (s09), les essais qui ont reçu un conseil sont signalés.

### Dependencies
s09-boss-progress-history, s10-connect-ai-assistant.

### Agentic notes
- Le texte vient d'une IA externe : le traiter comme une donnée non fiable.

## Story s12-compare-with-opt-in — Me comparer aux autres joueurs qui l'acceptent
Source: docs/prd.md

**As a** joueur **I want** comparer mon chiffre clé à celui d'autres joueurs du site sur le même boss **so that** je sache où j'en suis.

### Complexity
3

### Acceptance criteria
- [ ] AC1 — Le réglage « autoriser la comparaison avec mes combats » est désactivé par défaut.
- [ ] AC2 — Sur un de mes combats, je vois mon chiffre clé comparé à celui des joueurs ayant donné leur accord, sur le même boss, la même difficulté et la même spécialisation.
- [ ] AC3 — Les combats d'un joueur qui n'a pas donné son accord n'apparaissent jamais dans une comparaison.
- [ ] AC4 — Quand un joueur retire son accord, ses combats disparaissent aussitôt des comparaisons.

### Dependencies
s09-boss-progress-history.

### Agentic notes
- RGPD : accord explicite, retirable à tout moment (PRD, contraintes).
- Ce qui est montré des autres joueurs (nom du personnage ou anonyme) se décide en conception.

## Story s13-admin-manage-users — Gérer les utilisateurs (admin)
Source: docs/prd.md

**As a** administrateur **I want** voir les utilisateurs et désactiver un compte **so that** je protège le site.

### Complexity
4

### Acceptance criteria
- [ ] AC1 — Un utilisateur qui n'est pas admin ne peut pas accéder à l'espace admin.
- [ ] AC2 — L'admin voit la liste des utilisateurs : e-mail, date d'inscription, nombre de logs.
- [ ] AC3 — Un compte désactivé par l'admin ne peut plus se connecter ; le réactiver rétablit l'accès.
- [ ] AC4 — Aucun utilisateur ne peut se donner lui-même le rôle admin depuis le site.

### Dependencies
s01-sign-up-sign-in, s02-upload-log-list-fights.

### Agentic notes
- **Risque (4)** : droits. Chaque action admin est vérifiée côté serveur, pas seulement masquée à l'écran.
- Le premier admin est désigné hors de l'interface (configuration ou commande).

## Story s14-admin-delete-log — Supprimer un log (admin)
Source: docs/prd.md

**As a** administrateur **I want** supprimer un log **so that** je retire un contenu abusif.

### Complexity
2

### Acceptance criteria
- [ ] AC1 — L'admin voit la liste des logs (propriétaire, date, taille) et peut en supprimer un.
- [ ] AC2 — Un log supprimé disparaît pour son propriétaire, des comparaisons et des outils de l'assistant IA.
- [ ] AC3 — Un utilisateur qui n'est pas admin ne peut pas supprimer le log d'un autre.

### Dependencies
s13-admin-manage-users.

### Agentic notes
- Supprimer aussi les combats et conseils rattachés au log.

## Story s15-delete-my-account — Supprimer mon compte
Source: docs/prd.md

**As a** joueur **I want** supprimer mon compte **so that** mes données personnelles soient effacées (RGPD).

### Complexity
2

### Acceptance criteria
- [ ] AC1 — Après confirmation, mon compte est supprimé et je ne peux plus me connecter.
- [ ] AC2 — Mes logs, combats, conseils et accès d'assistant IA sont supprimés, et n'apparaissent plus dans aucune comparaison.
- [ ] AC3 — L'e-mail du compte supprimé peut servir à créer un nouveau compte.

### Dependencies
s01-sign-up-sign-in, s02-upload-log-list-fights.

### Agentic notes
- Vérifier, au moment de son exécution, que toutes les données créées par les stories déjà livrées sont couvertes.

## Story s16-compare-with-top-players — Me comparer aux meilleurs joueurs de ma spé
Source: docs/prd.md

**As a** joueur **I want** comparer mon combat aux meilleurs joueurs de ma spécialisation sur le même boss **so that** mon assistant IA et moi voyions ce qu'ils font de mieux.

### Complexity
4

### Acceptance criteria
- [ ] AC1 — Sur un de mes combats, mon chiffre clé s'affiche à côté de celui des meilleurs joueurs de ma spécialisation sur le même boss et la même difficulté, d'après Warcraft Logs.
- [ ] AC2 — Si Warcraft Logs ne répond pas ou refuse l'appel, un message clair s'affiche et le reste de la page du combat fonctionne normalement.
- [ ] AC3 — L'outil de l'assistant IA « comparer aux meilleurs » renvoie cette comparaison pour un de mes combats, et « introuvable » pour le combat d'un autre joueur.
- [ ] AC4 — La clé d'accès à l'API Warcraft Logs n'apparaît jamais dans le dépôt, dans les pages du site ni dans les réponses à l'assistant.

### Dependencies
s09-boss-progress-history, s10-connect-ai-assistant.

### Agentic notes
- **Risque (4)** : dépendance externe. Conditions d'utilisation, limites d'appels et accès (clé gratuite) de l'API Warcraft Logs à vérifier en recherche ; ne pas rappeler l'API à chaque affichage si ses limites l'interdisent.
- Brique « intégration externe » exigée par la formation.
- Hors périmètre : tout autre usage de l'API (import de rapports, classements).
