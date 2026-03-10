# Skills et Agents

## Quelle est la difference ?

| | Skills | Agents |
|---|--------|--------|
| **Invoque par** | L'utilisateur (commande slash) | Claude (programmatiquement) |
| **Objectif** | Orchestrer des workflows | Executer des taches specifiques |
| **S'execute dans** | Le contexte principal | Un contexte separe (subprocess) |
| **Format** | `SKILL.md` avec frontmatter | Fichier Markdown dans `.claude/agents/` |
| **Exemple** | `/skill-implement` | `coding-agent` |

Les **skills** sont des workflows destines a l'utilisateur. Ils orchestrent un ou plusieurs agents.
Les **agents** sont des unites de travail. Ils effectuent le travail reel dans des contextes isoles.

## Format des Skills (SKILL.md)

Les skills se trouvent dans `.claude/skills/<skill-name>/SKILL.md` :

```yaml
---
description: "Short description shown in skill list"
user_invocable: true
---

# Skill Name

Instructions for what Claude should do when this skill is invoked.

## Steps
1. First step
2. Second step
```

Regles essentielles :
- `description` est affichee quand les utilisateurs tapent `/` pour voir les skills disponibles
- `user_invocable: true` le fait apparaitre dans la liste des commandes slash
- Les instructions peuvent referencer des agents par leur nom (ils seront lances comme subagents)
- Les skills s'executent dans le contexte principal, donc gardez les instructions concises

## Format des Agents

Les agents se trouvent dans `.claude/agents/<agent-name>.md` :

```markdown
# Agent Name

You are a [role description]. You [what you do].

## Workflow
1. Step one
2. Step two

## Rules
- Rule one
- Rule two

## Output Format
Report: [what to return]
```

Regles essentielles :
- Les agents s'executent dans des contextes separes (ils ne voient pas votre conversation principale)
- Fournissez-leur TOUT le contexte dont ils ont besoin dans le prompt de lancement
- Definissez des formats de sortie clairs pour que les resultats soient faciles a analyser
- Gardez les agents concentres sur UNE seule responsabilite

## Ecrire des skills personnalises

### Exemple : un skill de deploiement

```yaml
---
description: "Deploy to staging environment"
user_invocable: true
---

# Deploy to Staging

## Steps
1. Run `git status` to check for uncommitted changes. If dirty, ask user to commit first.
2. Run tests: spawn coding-agent to run the test suite.
3. If tests pass: run `./deploy.sh staging`
4. Verify deployment: check health endpoint
5. Report: deployed version, health status, URL
```

### Exemple : un skill de documentation

```yaml
---
description: "Generate API documentation from code"
user_invocable: true
---

# Generate API Docs

## Steps
1. Spawn explorer agent: "Find all route definitions and their handlers"
2. For each route: extract method, path, parameters, response format
3. Generate markdown documentation
4. Write to docs/api.md
```

## Ecrire des agents personnalises

### Exemple : un agent de migration

```markdown
# Database Migration Agent

You create and verify database migrations.

## Workflow
1. Read the requested schema change
2. Check current schema (read migration files or query DB)
3. Generate migration file following project conventions
4. Generate rollback migration
5. Verify: dry-run the migration if possible

## Rules
- Always create rollback migrations
- Never drop columns without asking
- Use the project's migration framework
```

## Bonnes pratiques

1. **Les skills orchestrent, les agents executent** -- ne mettez pas de details d'implementation dans les skills
2. **Un agent, une tache** -- gardez les agents concentres
3. **Contrats de sortie clairs** -- definissez ce que les agents renvoient
4. **Echec gracieux** -- les agents doivent signaler les erreurs, pas echouer silencieusement
5. **Sensibles au contexte** -- les agents doivent consulter `.claude/rules/` pour les conventions du projet
