# Gestion de la Fenetre de Contexte

La fenetre de contexte est votre ressource la plus precieuse lorsque vous travaillez avec Claude Code. Chaque fichier lu, chaque sortie de test, chaque echange — tout consomme un budget fixe. Quand il se remplit, Claude compresse votre conversation et des details sont perdus.

Ce guide vous apprend a garder le controle.

## Le Probleme : la Compaction

Quand votre conversation approche la limite de contexte, Claude compresse automatiquement les messages anciens. C'est la **compaction automatique**. Voici ce qui se passe :

- Les extraits de code detailles sont resumes en une ligne
- Les plans multi-etapes perdent des etapes intermediaires
- Le contenu des fichiers lus precedemment disparait
- Les resultats des agents sont compresses en resumes de resumes

Le resultat : Claude "oublie" des parties du plan, relit des fichiers deja vus, prend des decisions contradictoires, ou pose des questions deja repondues. Vous finissez par passer plus de temps a recuperer le contexte qu'a travailler reellement.

## Developpement Guide par un Plan

La technique la plus importante : **toujours avoir un fichier de plan**.

Un fichier de plan est votre memoire externe. Quand la compaction survient, le plan survit sur le disque. Claude peut le relire. Sans plan, tout n'existe que dans la fenetre de contexte — et une fois compresse, c'est perdu.

```
# Mauvais : Tout dans votre tete
Vous : "Ajoute l'authentification a l'API"
Claude : lit 8 fichiers, conçoit l'approche, commence a implementer...
[compaction]
Claude : "Qu'est-ce qu'on faisait deja ? Je vais relire ces fichiers..."

# Bon : Guide par un plan
Vous : "Ajoute l'authentification a l'API"
Claude : ecrit le plan dans .claude/plans/auth.md
Claude : implemente etape par etape, consultant le plan comme reference
[compaction]
Claude : lit le fichier de plan, continue exactement ou il s'etait arrete
```

Utilisez `/plan` ou demandez a Claude d'entrer en mode plan avant toute tache non triviale. Le fichier de plan ne coute presque rien — mais sauve tout.

## Le Budget de Contexte

Toutes les operations ne coutent pas la meme chose. Voici une estimation :

| Operation | Cout en contexte | Alternative |
|-----------|-----------------|-------------|
| Lire un fichier de 200 lignes | Eleve | Un agent le lit et retourne un resume |
| Sortie de test complete (100+ lignes) | Tres eleve | Un agent execute les tests et retourne pass/fail |
| Lire une fonction de 20 lignes | Faible | OK dans le contexte principal |
| Resultat d'agent (resume) | Faible | C'est la bonne methode |
| Une seule modification | Faible | OK dans le contexte principal |
| Echanges de conversation | Moyen | Soyez precis, reduisez les iterations |

**Arbre de decision : Contexte principal ou agent ?**

```
C'est une operation petite et ciblee ? (<50 lignes, 1-2 fichiers)
  → Contexte principal

C'est de la lecture, recherche ou analyse de code ?
  → Agent (Explore ou general-purpose)

C'est une implementation sur plusieurs fichiers ?
  → Agent (coding)

C'est l'execution de tests ?
  → Agent (retourner seulement le resume)

Vous n'etes pas sur ?
  → Agent. Le surcout est faible, les economies sont importantes.
```

## Rester Sous la Limite

### 1. Les plans comme memoire externe

Ecrivez les plans sur le disque. Referencez-les au lieu de tout garder dans la conversation :

```
Vous : "Lis le plan a .claude/plans/feature.md et continue avec l'etape 3"
```

Claude lit le fichier, obtient le contexte complet et continue. Bien moins couteux que d'avoir tout l'historique du plan dans la conversation.

### 2. Deleguer aux agents

Les agents s'executent dans leur propre fenetre de contexte. Leurs resultats reviennent sous forme de resumes compacts. C'est le levier le plus puissant :

```
# Mauvais : 500 lignes de sortie de test dans votre contexte
Vous : "Execute les tests"

# Bon : 2 lignes de resume
Vous : "Execute les tests avec un agent, retourne seulement le resume pass/fail"
```

### 3. Ne jamais lire de gros fichiers dans le contexte principal

Si vous devez comprendre un fichier, envoyez un agent. Si vous devez modifier une fonction specifique, lisez seulement cette fonction (utilisez les plages de lignes). La pire chose a faire est de `Read` un fichier de 500 lignes "juste pour voir ce qu'il contient."

### 4. Garder les conversations focalisees

Une tache par session. Si vous terminez une fonctionnalite et voulez en commencer une nouvelle, envisagez de demarrer une session fraiche. L'ancien contexte est du poids mort pour la nouvelle tache.

### 5. Surveiller l'indicateur de contexte

Claude Code affiche votre utilisation du contexte. Quand vous atteignez ~70%, il est temps de :
- Terminer la tache en cours
- Demarrer une nouvelle session avec un transfert
- Sauvegarder votre plan et continuer avec une session fraiche

## Recuperation : Quand la Compaction Survient

Parfois cela arrive malgre vos efforts. Voici quoi faire :

### Option 1 : Relire le plan

Si vous avez un fichier de plan, demandez simplement a Claude de le relire :

```
Vous : "Lis .claude/plans/feature.md et continue ou on s'est arrete"
```

### Option 2 : Demarrer une nouvelle session

Pour une compaction importante, une nouvelle session est souvent plus propre :

```
Vous : "Resume ce qu'on a fait et ce qui reste. Ecris-le dans .claude/plans/handoff.md"
# Nouvelle session
Vous : "Lis .claude/plans/handoff.md et continue l'implementation"
```

### Option 3 : Utiliser la memoire

Si vous utilisez `claude-mem` ou un outil similaire, les decisions cles et les patterns survivent automatiquement entre les sessions. Sauvegardez les decisions architecturales importantes en memoire au fur et a mesure.

## Exemples Concrets

### Exemple 1 : L'explosion de contexte

> Vous demandez a Claude d'ajouter un systeme de paiement. Claude lit 12 fichiers pour comprendre la codebase, ecrit 200 lignes d'implementation, execute les tests (80 lignes de sortie), rencontre une erreur, lit 3 fichiers supplementaires pour debugger...
>
> Le contexte est a 90%. La compaction se declenche. Claude oublie la moitie du plan d'implementation et commence a relire des fichiers deja vus. Vous passez 20 minutes a revenir au point ou vous en etiez.

### Exemple 2 : L'approche disciplinee

> Vous demandez a Claude de planifier le systeme de paiement. Claude ecrit un plan en 6 etapes. Vous l'approuvez.
>
> Claude dispatche un agent coding pour l'etape 1. L'agent retourne : "PaymentService cree avec 3 methodes, tous les tests passent." Vous passez a l'etape 2. Chaque etape est un appel d'agent propre.
>
> Le contexte reste a 30% tout au long. Pas de compaction. Pas de travail perdu.

### Exemple 3 : Recuperation en milieu de session

> Vous etes a l'etape 4 sur 6 quand vous remarquez que le contexte devient eleve. Vous demandez a Claude de mettre a jour le fichier de plan avec la progression actuelle. Puis vous demarrez une nouvelle session :
>
> "Lis .claude/plans/payment.md et continue avec l'etape 5."
>
> Contexte propre, plan complet, aucun travail perdu. La coupure de session a pris 30 secondes.

## Reference Rapide

| A faire | A eviter |
|---------|----------|
| Ecrire un plan avant d'implementer | Se lancer directement dans le code pour des fonctionnalites complexes |
| Deleguer la lecture de fichiers aux agents | Lire de gros fichiers dans le contexte principal |
| Obtenir des resumes de tests des agents | Afficher la sortie complete des tests dans le contexte |
| Une tache par session | Enchainer des taches non liees dans une session |
| Sauvegarder la progression dans des fichiers de plan | Tout garder dans la memoire de conversation |
| Repartir a neuf a 70% de contexte | Continuer jusqu'a ce que la compaction frappe |
| Utiliser les outils de memoire pour les decisions cles | Compter uniquement sur l'historique de conversation |
| Etre precis dans vos demandes | Avoir de longs echanges de clarification |
