# Configuration du Projet

Comment configurer Claude Code pour un nouveau projet.

## Etape 1 : Copier les fichiers du starter

```bash
# Copier les templates du projet dans votre repertoire .claude/
cp -r claude-starter/project/* <your-project>/.claude/

# Votre projet devrait maintenant contenir :
# <your-project>/.claude/
#   agents/         (10 fichiers d'agents)
#   skills/         (7 repertoires de skills)
#   rules/          (3 fichiers templates)
#   CLAUDE.md.template
```

## Etape 2 : Executer le skill d'adaptation

```bash
cd <your-project>
claude
# Puis tapez : /skill-adapt-starter
```

Cela va :
1. Analyser votre codebase (langage, framework, configuration des tests)
2. Poser quelques questions sur votre architecture
3. Convertir tous les fichiers `.template` en configuration reelle
4. Creer un `CLAUDE.md` a la racine de votre projet

## Etape 3 : Examiner les fichiers generes

Apres l'adaptation, examinez ces fichiers :

| Fichier | Ce qu'il faut verifier |
|---------|------------------------|
| `CLAUDE.md` (racine du projet) | Commandes de build, description de l'architecture |
| `.claude/rules/architecture.md` | Definitions des couches, structure des modules |
| `.claude/rules/testing.md` | Commandes de test, conventions |
| `.claude/rules/security.md` | Patterns d'authentification, regles de validation |

## Etape 4 : Ajouter des regles specifiques au projet

Les templates couvrent les patterns courants. Ajoutez les regles specifiques a votre projet :

### Regles d'architecture personnalisees

```markdown
# Dans .claude/rules/architecture.md, ajoutez :

## Project-Specific Rules
- All API responses must include a `requestId` field
- Database migrations must be reversible
- Feature flags must be used for incomplete features
```

### Regles de test personnalisees

```markdown
# Dans .claude/rules/testing.md, ajoutez :

## Project-Specific Testing
- Integration tests must clean up test data (afterEach)
- Mock external APIs, never call them in tests
- Use factories for test data, not fixtures
```

## Etape 5 : Verifier la configuration

Testez votre configuration en executant :

```bash
# Dans le repertoire de votre projet, demarrez Claude :
claude

# Essayez ces commandes :
/skill-review          # Devrait analyser vos modifications non committees
/skill-run-tests       # Devrait executer votre suite de tests
```

## Optionnel : Configuration globale

Si vous n'avez pas encore configure les parametres globaux :

```bash
# Copier les parametres globaux
cp claude-starter/global/CLAUDE.md ~/.claude/CLAUDE.md
cp claude-starter/global/settings.json ~/.claude/settings.json
```

## Optionnel : .claudeignore

Creez un fichier `.claudeignore` a la racine de votre projet pour exclure des fichiers du contexte de Claude :

```
# Build artifacts
dist/
build/
node_modules/

# Large generated files
*.min.js
*.bundle.js
package-lock.json

# Binary files
*.png
*.jpg
*.pdf
```

## Structure des repertoires apres configuration

```
<your-project>/
  CLAUDE.md                     # Instructions du projet (genere)
  .claudeignore                 # (optionnel) Fichiers a exclure
  .claude/
    agents/                     # 10 definitions d'agents
      coding-agent.md
      implement-pipeline.md
      explorer.md
      debugger.md
      review-architecture.md
      review-quality.md
      review-tests.md
      review-reuse.md
      review-efficiency.md
      review-security.md
    skills/                     # 7 skills
      skill-implement/SKILL.md
      skill-coding/SKILL.md
      skill-review/SKILL.md
      skill-debug/SKILL.md
      skill-commit/SKILL.md
      skill-run-tests/SKILL.md
    rules/                      # Regles du projet
      architecture.md
      testing.md
      security.md
```

Note : `skill-adapt-starter` se supprime automatiquement apres une execution reussie.
