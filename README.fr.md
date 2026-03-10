# Claude Code Starter Kit

## Pourquoi ce kit ?

Claude Code est un assistant IA en ligne de commande qui ecrit, revoit et debogue du code directement dans votre terminal. Le principe fondamental pour etre productif est de **penser en agents, pas en conversations** -- deleguez le travail specialise a des agents dedies au lieu de tout concentrer dans une seule conversation, ce qui preserve votre contexte principal pour les decisions. Ce kit transforme des workflows eprouves en production en skills et agents reutilisables que vous pouvez adopter immediatement.

## Installation rapide

### 1. Copier la configuration globale

Copiez le contenu de `global/` dans votre repertoire personnel :

```bash
cp global/CLAUDE.md ~/.claude/CLAUDE.md
cp global/settings.json ~/.claude/settings.json
```

### 2. Copier la configuration projet

Copiez le contenu de `project/` dans votre depot :

```bash
cp -r project/.claude <votre-projet>/.claude
```

### 3. Installer les plugins

```bash
claude mcp add github -- npx -y @anthropic-ai/claude-code-github-mcp
claude mcp add context7 -- npx -y @anthropic-ai/claude-code-context7-mcp
claude mcp add claude-mem -- npx -y @anthropic-ai/claude-code-memory-mcp
claude mcp add playwright -- npx -y @anthropic-ai/claude-code-playwright-mcp
```

### 4. Personnaliser pour votre projet

Lancez Claude Code dans le repertoire de votre projet et executez :

```
/skill-adapt-starter
```

Ce skill analyse votre codebase (langage, framework, test runner, architecture) et reecrit les templates de rules, agents et skills pour correspondre a votre projet.

## Plugins recommandes

| Plugin | Fonction | Priorite |
|--------|----------|----------|
| **github** | Issues, PRs, recherche de code, commentaires de review directement depuis Claude | INDISPENSABLE |
| **context7** | Documentation a jour des bibliotheques et frameworks | INDISPENSABLE |
| **claude-mem** | Memoire persistante entre les sessions | RECOMMANDE |
| **playwright** | Automatisation du navigateur pour les tests et verifications | OPTIONNEL |

## Les 3 couches

La configuration de Claude Code fonctionne en trois couches, de la plus large a la plus etroite :

| Couche | Emplacement | Portee | Exemple |
|--------|-------------|--------|---------|
| **Global** | `~/.claude/CLAUDE.md` | Tous les projets, toutes les sessions | "Ne jamais tuer un processus sans demander" |
| **Projet** | `<projet>/.claude/` | Toute personne travaillant sur ce depot | Rules d'architecture, conventions de test, skills |
| **Session** | Contexte de conversation | Cette conversation uniquement | Tache en cours, etat temporaire |

Les rules globales definissent comment vous voulez que Claude se comporte partout. Les rules projet definissent comment votre codebase fonctionne. Le contexte de session correspond a ce sur quoi vous travaillez en ce moment.

## Penser en agents, pas en conversations

C'est le concept le plus important pour un developpement assiste par IA productif.

Votre conversation principale avec Claude dispose d'une **fenetre de contexte** -- un budget fixe de texte qu'il peut traiter. Chaque fichier lu, chaque sortie de test, chaque long bloc de code consomme ce budget. Quand il est epuise, Claude commence a oublier le contexte anterieur et la qualite se degrade.

**La solution : traitez votre contexte principal comme un dispatcher.** Gardez-le leger. Deleguez le travail lourd a des agents qui s'executent dans leurs propres fenetres de contexte separees.

### Arbre de decision

| Action | Ou | Pourquoi |
|--------|-----|----------|
| Lire un plan ou un ticket | Contexte principal | Court, necessaire pour la coordination |
| Une seule commande git | Contexte principal | Trivial, sortie minimale |
| Petite modification (<10 lignes, 1-2 fichiers) | Contexte principal | Plus rapide que de lancer un agent |
| Recherche grep/glob ciblee | Contexte principal | Peu de sortie |
| Lire des fichiers (>50 lignes ou >2 fichiers) | **Agent** | Tueur de contexte #1 |
| Modifications de code (>3 fichiers ou >20 lignes) | **Agent** | Preserve le contexte principal |
| Executer des tests | **Agent** | L'agent execute et rapporte un resume |
| Revue de code | **Agent** | Lit de nombreux fichiers, produit un rapport cible |
| Investigation ou debogage | **Agent** | Peut necessiter la lecture de dizaines de fichiers |

### Mauvais vs. bon

**Mauvais -- tout dans le contexte principal :**

```
Vous : "Lis ces 5 fichiers, puis implemente la fonctionnalite, puis lance les tests"
  -> Claude lit 5 fichiers (~800 lignes dans le contexte)
  -> Claude fait 8 modifications (plus de contexte consomme)
  -> Claude lance 3 commandes de test (~90 lignes de sortie)
  -> Le contexte est maintenant consomme a 70% pour une seule tache
```

**Bon -- delegation aux agents :**

```
Vous : "Implemente la fonctionnalite de notification utilisateur selon le ticket #42"
  -> Claude lance un coding-agent avec la description complete de la tache
  -> L'agent lit les fichiers, implemente, lance les tests dans SON PROPRE contexte
  -> L'agent rapporte : "Termine. 4 fichiers modifies, 12 tests reussis."
  -> Contexte principal consomme : ~5 lignes
```

Le contexte principal reste propre pour la tache suivante, la decision suivante, la revue suivante.

## Les 7 skills

Les skills sont des workflows predefinis que vous invoquez avec `/skill-<nom>`. Ils orchestrent des agents, imposent des portes de qualite et encodent les bonnes pratiques.

| Skill | Ce qu'il fait |
|-------|--------------|
| `/skill-implement` | Pipeline d'implementation complete : modifications de code, puis revue a 6 dimensions, puis boucle de correction jusqu'a validation |
| `/skill-coding` | Implementer une fonctionnalite ou un bugfix avec protocole d'escalade (3 tentatives, puis debugger) |
| `/skill-review` | Revue de code a 6 dimensions : architecture, qualite, securite, tests, performance, documentation |
| `/skill-debug` | Pipeline de chasse aux bugs : reproduire, isoler, diagnostiquer, corriger, verifier |
| `/skill-commit` | Creer un commit avec un format de message adapte et une reference au ticket |
| `/skill-run-tests` | Lancer les tests et rapporter un resume compact (nombre de reussis/echoues uniquement) |
| `/skill-adapt-starter` | Analyser votre projet et personnaliser tous les templates du starter kit |

### Exemple : `/skill-implement`

```
Vous : /skill-implement "Ajouter un rate limiting sur le endpoint /api/upload, max 10 requetes par minute par utilisateur"
```

Ce qui se passe en coulisses :

1. Un coding agent lit les fichiers pertinents, implemente la fonctionnalite, ecrit les tests, les execute
2. Un review agent verifie l'architecture, la qualite du code, la securite, la couverture de tests, la performance, la documentation
3. Si la revue trouve des problemes, un fix agent les corrige
4. Les etapes 2-3 se repetent jusqu'a une revue propre (3 cycles maximum)
5. Vous recevez un resume : fichiers modifies, tests reussis, statut de la revue

Vous gardez le controle sans gerer chaque etape.

## Budget de contexte

Pensez au contexte comme de la RAM. Il est fini. Chaque interaction en consomme une partie.

| Action | Cout en contexte |
|--------|-----------------|
| Lire un fichier de 200 lignes | Eleve |
| Executer une suite de tests (sortie complete) | Tres eleve |
| Une commande git de 3 lignes | Faible |
| Resume d'un agent ("12 reussis, 0 echoues") | Tres faible |

**Regles pratiques :**

- Ne jamais lire des fichiers entiers (>50 lignes) dans le contexte principal si vous pouvez deleguer a un agent
- Ne jamais charger la sortie complete des tests dans le contexte principal -- filtrez avec `passing|failing` ou laissez l'agent resumer
- Si le contexte depasse 70%, finalisez : sauvegardez l'etat, demarrez une nouvelle session
- Agents en parallele pour les taches independantes (par ex. revoir le module A pendant que le module B est teste)

## Prochaines etapes

Approfondissez des sujets specifiques :

| Guide | Ce que vous apprendrez |
|-------|----------------------|
| [Workflow](guides/01-workflow.md) | Workflow de developpement quotidien avec Claude Code |
| [Skills et Agents](guides/02-skills-and-agents.md) | Comment ecrire vos propres skills et agents |
| [Pipeline de revue](guides/03-review-pipeline.md) | Le systeme de revue a 6 dimensions en detail |
| [Plugins](guides/04-plugins.md) | Installation, configuration et cas d'usage des plugins |
| [Configuration projet](guides/05-project-setup.md) | Adapter le starter kit a votre projet |

---

Construit a partir d'experience reelle sur des codebases en production. Pas de theorie -- uniquement des patterns qui ont survecu au contact des deadlines.
