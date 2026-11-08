# Suivi des tickets : GitHub

Les tickets et les specs de ce dépôt sont des issues GitHub (`Amjemhidi/Dispoapp`). Utiliser le CLI `gh` pour toutes les opérations.

## Conventions

- **Créer un ticket** : `gh issue create --title "..." --body "..."`. Utiliser un heredoc pour les corps sur plusieurs lignes.
- **Lire un ticket** : `gh issue view <numéro> --json number,title,body,labels,comments`.
- **Lister les tickets** : `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`, avec les filtres `--label` et `--state` adaptés.
- **Rattacher un ticket à un parent (sous-ticket)** : `gh issue create --parent <parent> ...`, ou ensuite `gh issue edit <parent> --add-sub-issue <enfant>` (`gh` 2.94+). Avec un `gh` plus ancien : `gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<id-base-enfant>` (identifiant de base de données, comme dans **Blocage** ci-dessous). Sans sous-tickets, mettre `Part of #<parent>` en tête du corps de l'enfant.
- **Blocage** : utiliser les **dépendances natives** de GitHub, la représentation officielle et visible dans l'interface. Ajouter un lien avec `gh issue create --blocked-by <n>,<n> ...` ou `gh issue edit <enfant> --add-blocked-by <bloquant>` (`gh` 2.94+). Avec un `gh` plus ancien : `gh api --method POST repos/<owner>/<repo>/issues/<enfant>/dependencies/blocked_by -F issue_id=<id-base-bloquant>`, où `<id-base-bloquant>` est l'**identifiant de base de données** numérique du bloquant (`gh api repos/<owner>/<repo>/issues/<n> --jq .id`, _pas_ le `#numéro` ni le `node_id`). Si les dépendances ne sont pas disponibles, mettre une ligne `Blocked by: #<n>, #<n>` en tête du corps de l'enfant. Un ticket est débloqué quand tous ses bloquants sont fermés.
- **Compter les bloquants ouverts** : `gh api repos/<owner>/<repo>/issues/<n> --jq .issue_dependencies_summary.blocked_by` (bloquants ouverts uniquement ; 0 veut dire qu'on peut commencer). `gh issue view <n> --json blockedBy` liste aussi les bloquants fermés, donc filtrer : `--jq '[.blockedBy.nodes[] | select(.state == "OPEN")] | length'`.
- **Commenter un ticket** : `gh issue comment <numéro> --body "..."`
- **Ajouter / retirer des labels** : `gh issue edit <numéro> --add-label "..."` / `--remove-label "..."`
- **Fermer** : poster l'explication avec `gh issue comment <numéro> --body "..."`, puis `gh issue close <numéro>`. Sur un ticket déjà fermé, `gh issue close --comment` perd le commentaire tout en renvoyant 0.

Déduire le dépôt de `git remote -v` ; `gh` le fait tout seul quand il tourne dans un clone.

Ne créer des tickets que dans le dépôt de l'utilisateur. Ne jamais en créer dans `mattpocock/skills` (le dépôt d'où viennent ces skills). Dans un fork, `gh` peut viser le dépôt parent par défaut : avant le premier `gh issue create` d'une session, vérifier la cible avec `gh repo view --json nameWithOwner` et la faire confirmer à l'utilisateur si ce n'est pas son dépôt.

## Pull requests comme source de demandes

**PRs as a request surface: no.** _(Passer à `yes` si ce dépôt traite les PR externes comme des demandes de fonctionnalité ; `/triage` lit ce drapeau.)_

Avec `yes`, les PR suivent les mêmes labels et états que les tickets, avec les équivalents `gh pr` :

- **Lire une PR** : `gh pr view <numéro>`, puis `gh pr view <numéro> --comments` (hors terminal, `--comments` n'affiche que les commentaires, pas le corps), et `gh pr diff <numéro>` pour le diff.
- **Lister les PR externes à trier** : `gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`.
- **Commenter / labelliser / fermer** : `gh pr comment`, `gh pr edit --add-label`/`--remove-label`, `gh pr close`.

GitHub partage une seule numérotation entre tickets et PR, donc un `#42` seul peut être l'un ou l'autre : essayer `gh pr view 42`, puis `gh issue view 42`.

## Quand une skill dit « publier dans le suivi des tickets »

Créer une issue GitHub.

## Quand une skill dit « récupérer le ticket concerné »

Le lire comme dans **Lire un ticket** ci-dessus.

## Opérations de wayfinding

Utilisées par `/wayfinder`. La **carte** est un seul ticket, dont les tickets **enfants** sont les tâches.

- **Carte** : un seul ticket avec le label `wayfinder:map`, qui contient les sections Notes / Decisions-so-far / Fog. `gh issue create --label wayfinder:map`.
- **Ticket enfant** : un ticket rattaché à la carte comme sous-ticket GitHub (voir **Rattacher un ticket à un parent**). Si les sous-tickets ne sont pas activés, ajouter l'enfant à une liste de tâches dans le corps de la carte et mettre `Part of #<carte>` en tête du corps de l'enfant. Labels : `wayfinder:<type>` (`research`/`prototype`/`grilling`/`task`). Une fois pris, le ticket est assigné au développeur qui pilote.
- **Blocage** : voir **Blocage** dans Conventions.
- **Requête de frontière** : lister les enfants ouverts de la carte (`gh issue list --state open`, limité aux sous-tickets / à la liste de tâches de la carte), écarter ceux qui ont un bloquant ouvert (voir **Compter les bloquants ouverts**, ou un ticket ouvert dans la ligne `Blocked by`) ou un assigné ; le premier dans l'ordre de la carte l'emporte.
- **Prendre un ticket** : `gh issue edit <n> --add-assignee @me`, première écriture de la session.
- **Résoudre** : `gh issue comment <n> --body "<réponse>"`, puis `gh issue close <n>`, puis ajouter un renvoi (résumé + lien) dans la section Decisions-so-far de la carte.
