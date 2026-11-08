---
name: saumon-update-projects
description: Scanne les dépôts publics de https://github.com/saumon/, compare avec la section "Whitewater Projects" du README, propose d'ajouter les projets manquants puis modifie le README. À utiliser pour "mets à jour la liste des projets", "ajoute mes nouveaux projets au README", "sync projects".
---

# saumon-update-projects

Met à jour la section `## 📂 Whitewater Projects` de `README.md` à partir des dépôts publics de l'utilisateur GitHub `saumon`.

## Étapes

### 1. Lister les dépôts publics

```bash
gh api 'users/saumon/repos?type=owner&per_page=100' --paginate \
  --jq '.[] | select(.private==false) | [.name,.fork,.archived,.has_pages,.homepage,.description] | @tsv'
```

Si `gh` n'est pas disponible ou pas authentifié, utiliser
`curl -s 'https://api.github.com/users/saumon/repos?type=owner&per_page=100&page=N'` (paginer jusqu'à page vide).

### 2. Lire les projets déjà listés

Lire `README.md` et extraire les entrées de la section `## 📂 Whitewater Projects` (lignes `- [**nom**](url) - description`). Le nom du dépôt est dans le texte en gras ou à la fin de l'URL.

### 3. Calculer les projets manquants

Un dépôt est « manquant » s'il n'est pas déjà dans la section. Exclure d'office :
- le dépôt `saumon` (le profil lui-même) et `saumon.github.io` (le site),
- les forks et dépôts archivés (les mentionner à l'utilisateur comme exclus, ne pas les proposer sauf demande).

### 4. Préparer une entrée par projet manquant

**Règle absolue : tout texte écrit dans le README doit être en anglais**, quelle que soit la langue du dépôt source (description GitHub ou README en français, etc.). Le skill est rédigé en français, mais jamais le README. Format identique aux entrées existantes :

```
- [**nom**](URL) - Description courte.
```

- **URL** : `homepage` si renseignée ; sinon `https://saumon.github.io/<nom>/` si `has_pages` est vrai ; sinon `https://github.com/saumon/<nom>`.
- **Description** : celle du dépôt si elle existe, **toujours traduite/reformulée en anglais** ; sinon, la déduire du README du dépôt (`gh api repos/saumon/<nom>/readme --jq .content | base64 -d | head -40`) en une phrase **en anglais**. Si rien n'est exploitable, proposer une description minimale et le signaler.

### 5. Proposer à l'utilisateur (AskUserQuestion)

Présenter la liste des projets manquants avec l'entrée proposée pour chacun, et demander lesquels ajouter (multiSelect). S'il n'y en a aucun, le dire et s'arrêter. Permettre à l'utilisateur de corriger une description via « Other ».

### 6. Modifier le README

Vérifier que chaque ligne à insérer est entièrement en anglais (relire avant d'écrire, y compris les descriptions corrigées par l'utilisateur via « Other » : les traduire). Avec l'outil Edit, ajouter les entrées retenues à la fin de la liste de la section `Whitewater Projects` (après la dernière ligne `- [**...`, avant le `---`). Ne toucher à rien d'autre. Ne pas committer sauf demande explicite.

### 7. Récap

Afficher les lignes ajoutées et les projets ignorés.
