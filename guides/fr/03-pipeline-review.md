# Le Pipeline de Review

## Pourquoi 6 dimensions ?

La revue de code va au-dela de "est-ce que ca a l'air correct ?". Differents aspects de la qualite du code necessitent differentes expertises. Le pipeline de review decoupe la revue en 6 dimensions specialisees, chacune traitee par un agent dedie :

| Dimension | Agent | Ce qu'il verifie |
|-----------|-------|-------------------|
| Architecture | `review-architecture` | Violations de couches, direction des dependances, limites des modules |
| Quality | `review-quality` | Lisibilite, DRY, gestion des erreurs, code mort |
| Tests | `review-tests` | Couverture, qualite des tests, cas limites, conventions |
| Reuse | `review-reuse` | Code duplique, utilitaires manques, heritage |
| Efficiency | `review-efficiency` | Requetes N+1, parallelisation manquante, operations redondantes |
| Security | `review-security` | OWASP Top 10, injection, authentification, secrets |

## Comment ca fonctionne

### Review independante (`/skill-review`)

1. Detecte les modifications non committees via `git diff`
2. Lance les 6 review agents **en parallele**
3. Chaque agent analyse UNIQUEMENT les lignes modifiees
4. Les resultats sont consolides en un seul rapport
5. Les observations sont triees par severite : BLOCKER > HIGH > MEDIUM > LOW

### Dans le cadre d'une implementation (`/skill-implement`)

1. Le `coding-agent` implemente la fonctionnalite
2. Les 6 review agents s'executent en parallele
3. S'il y a des observations BLOCKER/HIGH (sauf security) :
   - Le `coding-agent` les corrige
   - Les reviewers concernes se re-executent
   - Maximum 3 iterations
4. Les observations security sont presentees a l'utilisateur (jamais corrigees automatiquement)
5. Le rapport du pipeline est genere

## Niveaux de severite

| Niveau | Signification | Action |
|--------|---------------|--------|
| **BLOCKER** | Doit etre corrige avant le merge | Bloque le pipeline |
| **HIGH** | Devrait etre corrige | Declenche la boucle de correction |
| **MEDIUM** | A envisager de corriger | Signale, non bloquant |
| **LOW** | Suggestion | Signale, informatif |

## La boucle de correction

```
Review Round 1 -> Observations (3 HIGH, 2 MEDIUM)
  |
coding-agent corrige les 3 observations HIGH
  |
Review Round 2 -> Observations (1 HIGH, 2 MEDIUM)
  |
coding-agent corrige 1 observation HIGH
  |
Review Round 3 -> PASS (2 MEDIUM restantes, non bloquantes)
  |
Pipeline termine
```

### Detection de deadlock

Si plus de 80% des observations sont identiques apres 2 rounds de correction, le pipeline s'arrete :
- Le coding-agent est probablement incapable de resoudre le probleme
- Le pipeline escalade vers l'utilisateur avec les observations recurrentes
- L'utilisateur decide : corriger manuellement, ignorer, ou ajuster l'approche

## Security : rapport uniquement

Les observations de security ne sont **JAMAIS corrigees automatiquement**. Pourquoi ?

1. **Les faux positifs** sont frequents dans l'analyse de securite
2. **Le contexte compte** -- ce qui ressemble a une vulnerabilite peut etre intentionnel
3. **Les corrections peuvent introduire de nouveaux problemes** -- les changements de securite necessitent un jugement humain
4. **Piste d'audit** -- les decisions de securite doivent etre deliberees et documentees

Les observations de security sont presentees a l'utilisateur avec :
- La description de la vulnerabilite potentielle
- La reference a la categorie OWASP
- La remediation suggeree
- L'utilisateur decide si et comment corriger

## Utiliser des reviews individuelles

Si vous n'avez pas besoin des 6 dimensions, utilisez des flags :

```
/skill-review --arch             # Architecture uniquement
/skill-review --security         # Security uniquement
/skill-review --tests --quality  # Plusieurs dimensions specifiques
```

## Personnaliser les reviews

Chaque review agent lit `.claude/rules/` pour les conventions specifiques au projet. Pour personnaliser ce qui est signale :

1. Ajoutez des regles dans `.claude/rules/architecture.md`, `testing.md`, `security.md`
2. Les review agents verifieront en fonction de vos regles
3. Pas besoin de modifier les agents eux-memes
