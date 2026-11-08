---
name: saumon-finish
description: Termine le travail en cours sur la branche courante - commite tous les fichiers modifiés (après confirmation), propose un message de commit en anglais au format Conventional Commits, puis pousse le commit (après confirmation). À utiliser pour "finish", "commit et push", "termine mon travail".
---

# saumon-finish

Commit + push de la branche courante, avec confirmation de l'utilisateur avant chaque action qui modifie l'historique ou le dépôt distant.

## Étapes

### 1. État du dépôt

```bash
git branch --show-current
git status --short
git diff HEAD --stat
```

- Si l'arbre de travail est propre (aucun fichier modifié ni non suivi), le dire et s'arrêter.
- Si la branche courante est `master` ou `main`, prévenir l'utilisateur (le dépôt suit git flow : travail sur `feature/*` puis fusion dans `dev`) et lui demander s'il veut continuer quand même.
- Si HEAD est détaché, s'arrêter et le signaler.

### 2. Relire les changements

Lire `git diff HEAD` (et le contenu des fichiers non suivis si nécessaire) pour comprendre la nature des changements. Signaler à l'utilisateur tout fichier qui semble sensible (`.env`, clés, secrets, gros binaires) : ne pas l'inclure sans son accord explicite.

### 3. Proposer le message de commit

Message **en anglais**, au format Conventional Commits :

```
<type>(<scope optionnel>): <description courte à l'impératif, minuscule, sans point final>

<corps optionnel : le pourquoi, en anglais>
```

- Types : `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- Ligne de titre ≤ 72 caractères.
- Un seul commit pour l'ensemble des changements. Si les changements sont clairement sans rapport entre eux, le signaler et proposer de les séparer.
- Pas de ligne d'attribution (Co-Authored-By, etc.).

### 4. Confirmation du commit (AskUserQuestion)

Afficher la liste des fichiers qui seront commités (`git status --short`) et le message proposé, puis demander :
- **Commiter** avec ce message,
- **Modifier le message** (via « Other », puis reproposer),
- **Annuler**.

Ne rien commiter sans réponse positive.

### 5. Commiter

```bash
git add -A
git commit -m "<titre>" -m "<corps éventuel>"
```

Si un hook de pré-commit échoue, corriger le problème et créer un **nouveau** commit ; ne jamais utiliser `--no-verify` ni `--amend` sans demande.

### 6. Confirmation du push (AskUserQuestion)

Indiquer la branche, le remote (`origin`) et le hash du commit créé, puis demander de confirmer le push :
- **Pousser**,
- **Ne pas pousser**.

### 7. Pousser

```bash
git push origin <branche>
```

Si la branche n'a pas encore de upstream : `git push -u origin <branche>`. Ne jamais utiliser `--force`. Si le push est rejeté (non fast-forward), l'expliquer et demander quoi faire, sans rien forcer.

### 8. Récap

Afficher le hash et le titre du commit, la branche, et le résultat du push. Ne pas fusionner ni supprimer de branche : seulement sur demande explicite.
