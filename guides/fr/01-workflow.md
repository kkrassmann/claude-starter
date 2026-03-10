# Workflow : Pensez en agents, pas en conversations

C'est le concept le plus important pour un developpement assiste par IA productif.

## L'idee centrale

Claude Code dispose d'un **context window** -- une quantite limitee d'informations qu'il peut conserver dans une seule conversation. Chaque fichier lu, chaque sortie de test, chaque log d'erreur consomme une partie de ce budget. Lorsqu'il se remplit, le contexte ancien est compresse et les details sont perdus.

**Les agents resolvent ce probleme.** Chaque agent s'execute dans son propre contexte. A la fin de son execution, seul son resultat compact revient dans votre conversation principale. Cela signifie :

- Votre contexte principal reste propre pour les **decisions et la coordination**
- Le travail lourd (lecture de fichiers, implementation, tests) se deroule dans les contextes des agents
- Vous pouvez executer plusieurs agents **en parallele** pour des taches independantes

## Arbre de decision : contexte principal vs agent

| Tache | Ou ? | Pourquoi |
|-------|------|----------|
| Lire un plan ou un court fichier de config | Contexte principal | Petit, necessaire pour la coordination |
| Commandes git (status, checkout) | Contexte principal | Trivial, sortie minimale |
| Lire des fichiers (>50 lignes ou >2 fichiers) | **Agent** | Tueur de contexte numero 1 |
| Implementer du code (>3 fichiers ou >20 lignes) | **coding-agent** | Preserve le contexte principal |
| Executer des tests | **coding-agent** | L'agent execute et ne renvoie que le resume |
| Petites modifications (<10 lignes, 1-2 fichiers) | Contexte principal | Plus rapide que de lancer un agent |
| Recherche ciblee (fichier/fonction specifique) | Contexte principal (Grep/Glob) | Peu de sortie |
| Exploration large de la codebase | **explorer agent** | Pourrait renvoyer beaucoup de donnees |

## Anti-pattern : la session qui tue le contexte

Mauvais workflow :
1. Lire 5 fichiers source dans le contexte principal (~800 lignes consommees)
2. Faire 8 modifications dans le contexte principal
3. Executer les tests 3 fois avec la sortie complete (~30 lignes chacune)
4. Le contexte est rempli a 70%, Claude commence a oublier les decisions precedentes

**Resultat :** Claude perd le fil de ce qu'il a deja corrige, reintroduit des bugs, pose des questions auxquelles vous avez deja repondu.

## Bon pattern : la session dispatcher

Bon workflow :
1. Decrire la tache dans le contexte principal
2. Lancer un `coding-agent` avec la description complete de la tache
3. L'agent lit les fichiers, implemente, execute les tests, fait son rapport
4. Vous recevez un resume de 10 lignes : "3 fichiers modifies, 45 tests passent"
5. Contexte principal utilise : ~5%

## Exemples pratiques de workflow

### Implementation d'une fonctionnalite

```
Vous : "Ajouter la validation d'email a l'endpoint d'inscription"

Actions dans le contexte principal :
1. Lancer coding-agent : "Ajouter la validation d'email a l'inscription.
   Verifier d'abord les patterns de validation existants. Executer les tests apres."
2. Lire le resultat de l'agent (resume de 10 lignes)
3. Si les tests passent : "/skill-commit"
4. Si les tests echouent : lancer un autre coding-agent avec l'erreur
```

### Investigation d'un bug

```
Vous : "Les utilisateurs signalent une erreur 500 sur /api/users/profile"

Actions dans le contexte principal :
1. Lancer explorer + debugger en parallele :
   - Explorer : "Trouver le handler de l'endpoint profile et ses dependances"
   - Debugger : "Verifier error.log pour les erreurs 500 recentes sur /api/users/profile"
2. Lire les deux resultats (~20 lignes au total)
3. Vous comprenez maintenant le probleme sans avoir lu un seul fichier vous-meme
4. Lancer coding-agent avec les instructions de correction
```

### Revue de code

```
Vous : "/skill-review"

Actions dans le contexte principal :
1. Le skill lance 6 review agents en parallele
2. Chacun renvoie ses observations (ou PASS)
3. Vous voyez un rapport consolide
4. Vous decidez quelles observations corriger
```

## Plan Mode

Pour les taches complexes (fonctionnalites multi-fichiers, exigences floues, refactoring important), utilisez le **Plan Mode** :

1. Tapez `/plan` ou demandez a Claude d'entrer en plan mode
2. Claude cree un plan structure sans effectuer de modifications
3. Vous examinez et ajustez le plan
4. Quittez le plan mode et executez (ou deleguez a `/skill-implement`)

**Quand utiliser le plan mode :**
- Nouvelle fonctionnalite couvrant plusieurs fichiers
- Refactoring dont la portee est incertaine
- Quand vous n'etes pas sur de l'approche

**Quand NE PAS utiliser le plan mode :**
- Correction de bug simple (corrigez directement)
- Modification d'un seul fichier (faites-le directement)
- Vous savez deja exactement quoi faire

## Recuperation de contexte

Si votre contexte devient trop plein (Claude commence a oublier des choses) :
1. Sauvegardez votre etat actuel (ce qui est fait, ce qui reste)
2. Demarrez une nouvelle conversation
3. Decrivez ou vous en etiez
4. Continuez avec des agents

La memoire cross-session (plugin claude-mem) est utile ici -- elle persiste les connaissances entre les conversations.

## Resume

| Principe | Ce que cela signifie |
|----------|----------------------|
| Contexte principal = dispatcher | Uniquement coordination, decisions, petites modifications |
| Agents = workers | Lecture de fichiers, implementation, tests |
| Parallele quand possible | Les taches independantes s'executent simultanement |
| Plan mode pour la complexite | Reflechir d'abord, executer ensuite |
| Jamais lire de gros fichiers dans le principal | Toujours deleguer a un agent |
| Sortie de test = resume uniquement | Grep pour pass/fail, pas les logs complets |

L'habitude la plus impactante : **avant de faire quelque chose dans le contexte principal, demandez-vous si un agent devrait le faire a la place.** La reponse est oui plus souvent que vous ne le pensez.
