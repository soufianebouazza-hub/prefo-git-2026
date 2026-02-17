# Bonnes pratiques Git et GitHub

**Formation WebDev — CF2M 2026**

---

## Table des matières

1. [Introduction](#1-introduction)
2. [Rappel : les 3 méthodes pour démarrer un projet](#2-rappel--les-3-méthodes-pour-démarrer-un-projet)
3. [Configuration initiale de Git](#3-configuration-initiale-de-git)
4. [Le fichier .gitignore](#4-le-fichier-gitignore)
5. [Les commits : règles et conventions](#5-les-commits--règles-et-conventions)
6. [Les Conventional Commits](#6-les-conventional-commits)
7. [Les branches : stratégies et nommage](#7-les-branches--stratégies-et-nommage)
8. [Les workflows Git](#8-les-workflows-git)
9. [Le travail en équipe : Fork, Upstream et Pull Requests](#9-le-travail-en-équipe--fork-upstream-et-pull-requests)
10. [Les Pull Requests : anatomie et bonnes pratiques](#10-les-pull-requests--anatomie-et-bonnes-pratiques)
11. [Sécurité : protéger les fichiers sensibles](#11-sécurité--protéger-les-fichiers-sensibles)
12. [Le fichier README.md](#12-le-fichier-readmemd)
13. [Commandes utiles au quotidien](#13-commandes-utiles-au-quotidien)
14. [Récapitulatif : la checklist des bonnes pratiques](#14-récapitulatif--la-checklist-des-bonnes-pratiques)
15. [Ressources](#15-ressources)

---

## 1. Introduction

### Qu'est-ce que Git ?

Git est un logiciel de **gestion de versions décentralisé**, gratuit et open source, créé en 2005 par **Linus Torvalds** (le créateur du noyau Linux). Depuis 2010, c'est de loin le système de versions le plus utilisé dans le monde du développement web, aussi bien pour les projets open source que commerciaux.

Git permet de suivre l'évolution de chaque fichier d'un projet, de revenir à un état antérieur, de travailler en parallèle sur plusieurs fonctionnalités, et de fusionner le travail de plusieurs développeurs.

### Qu'est-ce que GitHub ?

GitHub est une **plateforme en ligne** qui héberge des dépôts Git et facilite la collaboration entre développeurs. Rachetée par Microsoft en 2018 pour 7,5 milliards de dollars, elle compte aujourd'hui plus de 100 millions d'utilisateurs et héberge des millions de projets dans tous les langages.

Parmi ses concurrents, on retrouve **GitLab** (auto-hébergeable, très populaire en CI/CD) et **Bitbucket** (intégré à l'écosystème Atlassian).

Notre groupe de formation : [https://github.com/WebDevCF2m2026](https://github.com/WebDevCF2m2026)

### Pourquoi un cours sur les "bonnes pratiques" ?

Connaître les commandes Git ne suffit pas. La différence entre un projet maintenable et un projet chaotique tient souvent aux **conventions** suivies par l'équipe : nommage des branches, format des commits, organisation du workflow, gestion des secrets... Ce cours couvre toutes ces bonnes pratiques.

### Prérequis

Ce cours suppose que vous connaissez déjà les commandes de base de Git (`init`, `add`, `commit`, `push`, `pull`, `clone`) et que vous disposez d'un compte GitHub configuré avec une clé SSH.

Pour la configuration SSH et les rappels de base, référez-vous au cours de préformation :
[https://github.com/WebDevCF2m2026/prefo-git-2026](https://github.com/WebDevCF2m2026/prefo-git-2026)

---

## 2. Rappel : les 3 méthodes pour démarrer un projet

Avant d'aborder les bonnes pratiques, rappelons les trois scénarios classiques pour lancer un projet avec Git et GitHub (détaillés dans [base-git-classe-1](https://github.com/WebDevCF2m2026/base-git-classe-1)).

### A. La méthode "Local-First" (nouveau projet sur votre machine)

Vous commencez à coder en local avant d'avoir créé le dépôt en ligne.

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <URL_DU_DEPOT_GITHUB>
git push origin main
```

### B. La méthode "Remote-First" (clonage d'un dépôt)

C'est souvent la **méthode la plus simple** pour éviter les erreurs de configuration.

```bash
# Créez d'abord le dépôt sur GitHub, puis :
git clone <URL_DU_DEPOT>
```

Un dossier est créé, déjà lié à GitHub. Vous n'avez plus qu'à coder et "pusher".

### C. Connecter un projet local existant à GitHub

Vous avez déjà commencé à coder et initialisé Git localement.

```bash
git remote add origin <URL_DU_DEPOT_GITHUB>
git branch -M main
git add .
git commit -m "First commit"
git push -u origin main
```

> **Bonne pratique** : Quelle que soit la méthode choisie, créez **toujours** un fichier `.gitignore` et un `README.md` dès le premier commit.

---

## 3. Configuration initiale de Git

Avant de commencer à travailler, configurez correctement votre identité Git. Ces informations apparaîtront dans **chaque commit** que vous ferez.

```bash
git config --global user.name "VotreUsername"
git config --global user.email "votre@email.com"
```

### Configurations recommandées

```bash
# Définir la branche par défaut comme "main" (au lieu de "master")
git config --global init.defaultBranch main

# Activer la colorisation de la sortie Git
git config --global color.ui auto

# Configurer le pull en mode rebase (historique plus propre)
git config --global pull.rebase true

# Définir l'éditeur par défaut (nano, vim, code...)
git config --global core.editor nano

# Configurer un .gitignore global pour votre machine
git config --global core.excludesfile ~/.gitignore_global

# Vérifier toute votre configuration
git config --list
```

### Les alias Git : gagner du temps chaque jour

Les alias permettent de raccourcir les commandes que vous tapez des dizaines de fois par jour :

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage "restore --staged"
git config --global alias.last "log -1 HEAD"
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

Exemples d'utilisation :

```bash
git st          # au lieu de git status
git co main     # au lieu de git checkout main
git lg          # historique en graphe coloré et lisible
git last        # affiche le dernier commit
```

L'alias `lg` est particulièrement utile : il affiche l'historique sous forme de **graphe coloré** avec les branches, les auteurs et les dates relatives.

---

## 4. Le fichier .gitignore

Le fichier `.gitignore` est **indispensable** dans tout projet. Il indique à Git quels fichiers et dossiers ne doivent **jamais** être suivis ni envoyés sur le dépôt distant.

### Pourquoi est-ce si important ?

Sans `.gitignore`, vous risquez d'envoyer sur GitHub des fichiers qui n'ont rien à y faire : des fichiers sensibles contenant des mots de passe ou des clés API (`.env`), des dépendances volumineuses et reconstituables (`vendor/`, `node_modules/`), des fichiers spécifiques à votre IDE (`.idea/`, `.vscode/`), ou des fichiers temporaires du système d'exploitation (`.DS_Store`, `Thumbs.db`).

### Quand le créer ?

**Avant le premier commit !** C'est le tout premier fichier à créer dans un projet. Si vous oubliez d'ignorer un fichier et que vous le commitez, il restera dans l'historique Git même si vous le supprimez ensuite.

### Exemple pour un projet PHP / Symfony

```gitignore
# === Dépendances ===
/vendor/
/node_modules/

# === Variables d'environnement (CONTIENT DES SECRETS !) ===
.env
.env.local
.env.*.local

# === Cache et logs Symfony ===
/var/
/public/bundles/

# === IDE et éditeurs ===
.idea/
.vscode/
*.swp
*.swo
*~
.phpunit.result.cache

# === Système d'exploitation ===
.DS_Store
Thumbs.db
Desktop.ini

# === Docker (fichiers locaux) ===
docker-compose.override.yml
```

### Exemple pour un projet JavaScript / React

```gitignore
# === Dépendances ===
/node_modules/

# === Build ===
/build/
/dist/

# === Environnement ===
.env
.env.local
.env.production.local

# === Logs ===
*.log
npm-debug.log*

# === OS ===
.DS_Store
Thumbs.db
```

### La syntaxe du .gitignore

```gitignore
# Ceci est un commentaire

# Ignorer un fichier spécifique
secret.txt

# Ignorer tous les fichiers d'un type
*.log
*.tmp

# Ignorer un dossier entier (le / à la fin est important)
node_modules/
vendor/

# Ignorer un dossier uniquement à la racine du projet
/build/

# Négation : NE PAS ignorer un fichier spécifique
!important.log

# Ignorer tout sauf un dossier
/*
!/src/
```

### .gitignore global (pour votre machine)

Certains fichiers sont spécifiques à **votre environnement** (IDE, OS) et n'ont pas besoin d'être dans le `.gitignore` du projet. Configurez un `.gitignore` global :

```bash
git config --global core.excludesfile ~/.gitignore_global
```

Contenu recommandé pour `~/.gitignore_global` :

```gitignore
# macOS
.DS_Store

# Windows
Thumbs.db
Desktop.ini

# Linux
*~

# JetBrains (PhpStorm, WebStorm...)
.idea/

# VS Code
.vscode/
```

### Retirer un fichier déjà suivi par erreur

Si vous avez commité un fichier par erreur avant de l'ajouter au `.gitignore` :

```bash
# Retirer le fichier du suivi Git (sans le supprimer du disque)
git rm --cached nom_du_fichier

# Ajouter la règle au .gitignore
echo "nom_du_fichier" >> .gitignore

# Commiter le changement
git add .gitignore
git commit -m "chore: stop tracking nom_du_fichier"
```

### Le fichier .gitkeep

Git ne suit pas les dossiers vides. Si vous avez besoin de conserver un dossier vide dans votre dépôt (par exemple `uploads/` ou `var/log/`), placez-y un fichier `.gitkeep` vide. C'est une convention, pas une fonctionnalité de Git.

### Générer un .gitignore adapté

Le site [gitignore.io](https://www.toptal.com/developers/gitignore) génère des `.gitignore` adaptés à votre stack technologique. GitHub propose également une collection de templates sur [github/gitignore](https://github.com/github/gitignore).

---

## 5. Les commits : règles et conventions

Le commit est l'**unité fondamentale** de Git. Un bon historique de commits raconte l'histoire de votre projet de manière claire et navigable. Un mauvais historique est un cauchemar de maintenance.

### Les 7 règles d'or d'un bon message de commit

#### 1. Séparer le sujet du corps par une ligne vide

La première ligne est le résumé (sujet). Si des détails sont nécessaires, laissez une ligne vide puis ajoutez le corps du message.

```
Fix redirect loop on login page

The login page was redirecting authenticated users back to itself
instead of the dashboard. This was caused by a missing role check
in the security configuration.

Closes #42
```

#### 2. Limiter le sujet à 50 caractères

Cette limite force la concision et garantit un affichage correct dans les outils Git, sur GitHub, et dans les terminaux.

#### 3. Commencer le sujet par une majuscule

Écrire `Add user authentication` et non `add user authentication`.

#### 4. Ne pas terminer le sujet par un point

L'espace est précieux sur 50 caractères. Le point final est superflu.

#### 5. Utiliser l'impératif présent

Rédigez le message comme une **instruction** : le commit *fait* quelque chose au code. Cette convention correspond aux messages générés automatiquement par Git (`Merge branch...`, `Revert...`).

| ✅ Correct | ❌ Incorrect |
|-----------|-------------|
| `Add login feature` | `Added login feature` |
| `Fix memory leak in parser` | `Fixed memory leak` |
| `Remove deprecated methods` | `Removing deprecated methods` |
| `Update dependencies` | `Updated dependencies` |

**Astuce** : un bon sujet de commit complète la phrase *"Si appliqué, ce commit va..."* → "Si appliqué, ce commit va **Add login feature**."

#### 6. Utiliser le corps pour expliquer le "quoi" et le "pourquoi", pas le "comment"

Le code montre le "comment". Le message de commit doit expliquer **la raison** du changement et le contexte.

#### 7. Limiter le corps à 72 caractères par ligne

Cette convention héritée des emails garantit une bonne lisibilité dans le terminal et les différents outils Git.

### Exemples comparés

**✅ Bon commit :**

```
Fix cart total calculation with percentage discounts

The discount was being applied to each item individually instead
of the cart subtotal, causing rounding errors on orders with
multiple items.

Refs: JIRA-1234
```

**❌ Mauvais commits :**

```
fix bug
```

```
updated stuff
```

```
WIP
```

Ces messages ne donnent aucune information utile sur ce qui a changé ni pourquoi.

### Commits atomiques

Chaque commit doit représenter **une seule unité logique de travail**. C'est le principe des **commits atomiques** :

Un commit = un changement cohérent et complet. Ne mélangez pas dans un même commit un correctif de bug, un ajout de fonctionnalité et du reformatage de code. Créez plutôt trois commits séparés.

Les bénéfices sont nombreux : chaque changement est compréhensible individuellement, les reverts sont ciblés et sans effets de bord, la revue de code est facilitée, et la recherche de bugs avec `git bisect` devient efficace.

### Commiter souvent, mais intelligemment

Commitez **fréquemment** pour garder des unités de travail petites. Mais ne commitez pas n'importe quoi : chaque commit doit compiler et être fonctionnel.

Si vous avez besoin d'une copie de travail propre temporairement (pour changer de branche, tester quelque chose...), utilisez `git stash` plutôt qu'un commit incomplet :

```bash
# Mettre de côté les modifications en cours
git stash save "travail en cours sur le formulaire"

# Faire ce que vous avez à faire...

# Récupérer votre travail
git stash pop
```

### Vérifier avant de commiter

Prenez l'habitude de **toujours vérifier** ce que vous allez commiter :

```bash
# Voir les fichiers modifiés
git status

# Voir les modifications indexées (prêtes à être commitées)
git diff --staged

# Si tout est bon, commiter
git commit -m "feat: add email validation on registration"
```

---

## 6. Les Conventional Commits

Les **Conventional Commits** sont une spécification standardisée pour les messages de commit, adoptée par de nombreux projets professionnels. Elle facilite la lecture de l'historique et permet l'automatisation (changelogs, versioning sémantique).

Site officiel : [conventionalcommits.org](https://www.conventionalcommits.org/fr/v1.0.0/)

### Format

```
<type>(<portée optionnelle>): <description>

[corps optionnel]

[pied de page optionnel]
```

### Les types de commits

| Type | Quand l'utiliser | Exemple |
|------|-----------------|---------|
| `feat` | Nouvelle fonctionnalité pour l'utilisateur | `feat: add user registration form` |
| `fix` | Correction d'un bug | `fix: resolve login redirect loop` |
| `docs` | Changement dans la documentation uniquement | `docs: update API endpoints in README` |
| `style` | Formatage, point-virgule manquant, etc. (pas de changement de logique) | `style: fix indentation in UserController` |
| `refactor` | Refactorisation du code (ni fix, ni feat) | `refactor: extract validation to service` |
| `test` | Ajout ou correction de tests | `test: add unit tests for CartService` |
| `chore` | Tâches de maintenance, sans impact sur le code source | `chore: update .gitignore` |
| `perf` | Amélioration de performance | `perf: optimize database queries` |
| `ci` | Changements dans la configuration CI/CD | `ci: add GitHub Actions workflow` |
| `build` | Changements dans le système de build ou les dépendances | `build: upgrade Symfony to 7.4` |

### Exemples avec portée (scope)

La portée est optionnelle mais utile pour préciser le module ou la fonctionnalité concerné(e) :

```
feat(auth): add Google OAuth2 authentication
fix(cart): correct price calculation with discounts
docs(api): add authentication section to swagger docs
refactor(user): extract address validation to service
test(payment): add integration tests for Stripe webhook
build(docker): update PHP image to 8.4
```

### Changements majeurs (Breaking Changes)

Pour signaler un changement **non rétrocompatible**, ajoutez `!` après le type ou `BREAKING CHANGE:` dans le pied de page :

```
feat!: replace session-based auth with JWT tokens

BREAKING CHANGE: all API consumers must update their
authentication headers to use Bearer tokens instead of
session cookies.

Migration guide: docs/migration-v3.md
```

### Référencer des issues

Utilisez le pied de page pour référencer ou fermer des issues GitHub :

```
fix(auth): prevent session fixation on login

Validates and regenerates the session ID after successful
authentication to prevent session fixation attacks.

Closes #42
Refs #38, #40
```

### Pourquoi adopter cette convention ?

Un historique utilisant les Conventional Commits est **immédiatement lisible** :

```
feat(auth): add two-factor authentication
fix(cart): correct discount rounding error
docs: update installation guide for Docker
refactor(user): simplify role permission logic
chore: update dependencies to latest versions
ci: add automated security scanning
```

On peut aussi filtrer l'historique par type :

```bash
# Voir uniquement les nouvelles fonctionnalités
git log --oneline --grep="^feat"

# Voir uniquement les corrections de bugs
git log --oneline --grep="^fix"
```

Des outils comme **commitlint** valident automatiquement le format des messages, et **semantic-release** génère des changelogs et des numéros de version automatiquement à partir de l'historique.

---

## 7. Les branches : stratégies et nommage

Les branches sont l'une des fonctionnalités les plus puissantes de Git. Bien les utiliser permet de développer en parallèle, de tester sans risque, et de garder la branche principale toujours stable.

### La règle d'or

> **Ne travaillez JAMAIS directement sur `main`.**

La branche `main` doit toujours contenir du code **stable et fonctionnel**. Tout développement se fait sur des branches dédiées.

### Conventions de nommage des branches

Utilisez des **préfixes** cohérents pour catégoriser vos branches :

| Préfixe | Usage | Exemple |
|---------|-------|---------|
| `feature/` | Nouvelle fonctionnalité | `feature/user-registration` |
| `bugfix/` | Correction de bug (non urgente) | `bugfix/login-redirect` |
| `hotfix/` | Correction urgente en production | `hotfix/payment-crash` |
| `release/` | Préparation d'une version | `release/2.1.0` |
| `chore/` | Tâche de maintenance | `chore/update-dependencies` |
| `docs/` | Documentation | `docs/api-reference` |

### Règles de nommage

Voici les conventions à respecter pour nommer une branche :

- Utilisez des **tirets** (`-`) pour séparer les mots (pas d'underscores, pas d'espaces)
- Restez en **minuscules**
- Gardez les noms **courts mais descriptifs**
- Utilisez l'**anglais** de préférence (pour les projets collaboratifs)
- L'essentiel : **l'équipe doit s'accorder sur une convention et s'y tenir**

```
✅ feature/user-registration
✅ bugfix/login-redirect-error
✅ hotfix/payment-null-pointer

❌ Feature/User_Registration   (majuscules, underscores)
❌ ma-nouvelle-branche          (pas de préfixe, pas descriptif)
❌ fix                          (trop vague)
```

### Manipuler les branches

```bash
# Créer une branche et y basculer directement
git checkout -b feature/user-registration

# Ou avec la syntaxe moderne (Git 2.23+)
git switch -c feature/user-registration

# Lister les branches
git branch             # branches locales
git branch -a          # toutes (locales + distantes)
git branch -v          # avec le dernier commit de chaque branche

# Changer de branche
git switch main

# Supprimer une branche locale (après merge)
git branch -d feature/user-registration

# Forcer la suppression (branche non mergée)
git branch -D feature/abandoned-experiment

# Supprimer une branche distante
git push origin --delete feature/user-registration
```

### Garder ses branches à jour

Avant de soumettre votre travail, synchronisez votre branche avec `main` :

```bash
# Méthode rebase (historique linéaire et propre)
git switch main
git pull origin main
git switch feature/user-registration
git rebase main
```

Le **rebase** replace vos commits au-dessus de l'historique à jour de `main`, créant un historique linéaire et facile à lire.

> ⚠️ **Attention** : ne faites **jamais** de rebase sur une branche partagée avec d'autres développeurs (car cela réécrit l'historique).

```bash
# Méthode merge (alternative, conserve l'historique exact)
git switch feature/user-registration
git merge main
```

### Supprimer les branches mergées

Gardez votre dépôt propre en supprimant les branches une fois fusionnées :

```bash
# Voir les branches déjà fusionnées dans main
git branch --merged main

# Supprimer toutes les branches locales fusionnées (sauf main)
git branch --merged main | grep -v "main" | xargs git branch -d
```

---

## 8. Les workflows Git

Un **workflow** (flux de travail) définit comment une équipe organise son utilisation de Git. Le choix dépend de la taille de l'équipe, du rythme de livraison et de la complexité du projet.

### GitHub Flow (recommandé pour débuter)

C'est le workflow le plus simple, idéal pour les petites équipes et les projets en déploiement continu.

**Principe en 5 étapes :**

1. Créer une branche depuis `main` pour chaque fonctionnalité ou correction
2. Travailler dessus et commiter régulièrement
3. Ouvrir une **Pull Request** sur GitHub
4. Discuter, relire et valider le code
5. Fusionner dans `main` après validation

```
main ──●──────────────────────●──────────●──
        \                    /            |
feature  ●────●────●────●──●       merge & deploy
```

### GitFlow (pour les projets avec versions)

GitFlow est un workflow plus structuré, adapté aux projets qui gèrent des **versions numérotées** et plusieurs environnements (dev, staging, production).

Il utilise 5 types de branches :

| Branche | Rôle | Durée de vie |
|---------|------|-------------|
| `main` | Code de production (releases) | Permanente |
| `develop` | Branche d'intégration | Permanente |
| `feature/*` | Nouvelles fonctionnalités | Temporaire (depuis `develop`) |
| `release/*` | Préparation d'une version | Temporaire (depuis `develop`) |
| `hotfix/*` | Correction urgente de production | Temporaire (depuis `main`) |

```
main      ──●────────────────────●──────●──
             |                   ↑      ↑
release      |            ●──●──●      |
             |            ↑            |
develop   ──●────●───●───●────●───●───●──
              \  /         \  /
feature        ●●           ●●
```

Le flux est le suivant : les développeurs créent des branches `feature/` depuis `develop`, qu'ils y fusionnent une fois terminées. Quand assez de fonctionnalités sont prêtes, une branche `release/` est créée depuis `develop` pour la stabilisation. Celle-ci est ensuite fusionnée dans `main` (avec un tag de version) et dans `develop`. Les `hotfix/` sont créés depuis `main` et fusionnés dans `main` et `develop`.

### Quel workflow choisir ?

| Situation | Workflow recommandé |
|-----------|-------------------|
| Projet personnel | GitHub Flow |
| Petite équipe, déploiement continu | GitHub Flow |
| Projet avec releases planifiées | GitFlow |
| Formation CF2M | GitHub Flow + Fork/PR |

L'essentiel : **toute l'équipe doit suivre le même workflow**.

---

## 9. Le travail en équipe : Fork, Upstream et Pull Requests

Pour les projets d'équipe, le workflow recommandé (et utilisé en formation CF2M) repose sur le système **Fork + Pull Request**. Voir le dépôt [base-git-classe-1](https://github.com/WebDevCF2m2026/base-git-classe-1) pour la mise en pratique.

### Vue d'ensemble du workflow

```
┌─────────────────────────────────────────────────┐
│  Dépôt d'équipe (upstream)                      │
│  github.com/WebDevCF2m2026/mon-projet           │
│                                                  │
│  Le chef de projet merge les Pull Requests      │
└──────────────────────┬──────────────────────────┘
                       │ fork
                       ▼
┌─────────────────────────────────────────────────┐
│  Votre fork (origin)                            │
│  github.com/VotreUsername/mon-projet             │
│                                                  │
│  Vous poussez VOS branches ici                  │
└──────────────────────┬──────────────────────────┘
                       │ clone
                       ▼
┌─────────────────────────────────────────────────┐
│  Votre machine locale                           │
│  ~/projets/mon-projet                            │
│                                                  │
│  Vous travaillez ici, sur des branches          │
└─────────────────────────────────────────────────┘
```

### Étape par étape

#### 1. Forker le projet

Sur GitHub, cliquez sur **"Fork"** pour créer votre copie personnelle du dépôt d'équipe.

#### 2. Cloner votre fork

```bash
git clone <URL_DE_VOTRE_FORK>
cd nom-du-projet
```

#### 3. Configurer le dépôt upstream

```bash
# Vérifier les remotes existants (vous devriez voir "origin")
git remote -v

# Ajouter le dépôt d'équipe comme "upstream"
git remote add upstream <URL_DU_DEPOT_EQUIPE>

# Vérifier (vous devriez voir origin ET upstream)
git remote -v
```

#### 4. Créer une branche de travail

```bash
# TOUJOURS créer une branche, ne JAMAIS travailler sur main !
git checkout -b feature/ma-contribution
```

#### 5. Travailler, commiter et pousser

```bash
# Travailler sur vos fichiers...

git add .
git commit -m "feat: add my contribution"

# Pousser sur VOTRE fork (origin), pas sur upstream !
git push origin feature/ma-contribution
```

#### 6. Créer une Pull Request

Sur GitHub, depuis **votre fork**, cliquez sur **"Compare & pull request"** pour proposer vos modifications au dépôt d'équipe.

#### 7. Synchroniser avec le travail de l'équipe

Après que le chef de projet ait fusionné des Pull Requests (les vôtres ou celles d'autres membres) :

```bash
# Revenir sur main
git switch main

# Récupérer le travail de l'équipe depuis upstream
git pull upstream main

# Mettre à jour votre fork sur GitHub
git push origin main
```

Ensuite, créez une **nouvelle branche** depuis `main` à jour pour votre prochaine contribution.

### Les règles d'or du travail en équipe

1. **Ne travaillez JAMAIS sur `main`** — toujours sur une branche
2. **Synchronisez régulièrement** votre fork avec upstream
3. **Communiquez** : prévenez votre équipe de ce sur quoi vous travaillez
4. **Faites des PR courtes** et focalisées (pas de PR de 2000 lignes)
5. **Relisez le code des autres** (la code review est un apprentissage mutuel)
6. **Ne faites JAMAIS** `git push --force` sur une branche partagée

---

## 10. Les Pull Requests : anatomie et bonnes pratiques

La **Pull Request** (PR) est le mécanisme central de la collaboration sur GitHub. Elle permet de proposer des modifications, d'en discuter, de les relire, et de les valider avant intégration dans la branche principale.

### Un bon titre de PR

Comme pour un commit, le titre doit être clair et descriptif :

```
✅ feat: add email verification on user registration
✅ fix: resolve 500 error on empty cart checkout
✅ docs: add Docker installation instructions

❌ update files
❌ fix stuff
❌ changes
```

### Une bonne description de PR

Utilisez un template structuré pour expliquer le contexte :

```markdown
## Description
Ajout de la vérification par email lors de l'inscription utilisateur.

## Changements
- Création du service EmailVerificationService
- Modification du formulaire d'inscription
- Ajout de la route /verify-email/{token}
- Ajout du template d'email de vérification

## Comment tester
1. Créer un nouveau compte
2. Vérifier la réception de l'email (Mailtrap en dev)
3. Cliquer sur le lien de vérification
4. Vérifier que le compte est activé

## Captures d'écran
(si applicable)

## Issues liées
Closes #15
```

### PR courtes et focalisées

Une PR doit traiter **un seul sujet**. Les avantages des PR courtes (moins de 400 lignes modifiées) : elles sont relues plus rapidement, avec plus d'attention, et fusionnées plus vite. Si votre fonctionnalité est volumineuse, découpez-la en plusieurs PR successives.

### La code review

Quand vous relisez une PR d'un collègue :

- Vérifiez la **logique** du code (est-ce que ça fait ce que la description annonce ?)
- Vérifiez la **cohérence** avec les conventions du projet
- Identifiez les **cas limites** potentiels
- Soyez **constructif** : proposez des améliorations, ne vous contentez pas de critiquer
- Utilisez les suggestions GitHub pour proposer des changements concrets

---

## 11. Sécurité : protéger les fichiers sensibles

### Ce qui ne doit JAMAIS être commité

Les éléments suivants ne doivent **jamais** se retrouver dans un dépôt Git, même privé :

- Mots de passe et identifiants de bases de données
- Clés API et tokens d'authentification (Stripe, AWS, etc.)
- Clés SSH privées (`id_rsa`, `id_ed25519`)
- Fichiers `.env` contenant des secrets
- Certificats SSL privés (`.pem`, `.pfx`, `.key`)

### Pourquoi c'est critique ?

Une fois qu'un secret est dans l'historique Git, il y reste **même si vous le supprimez dans un commit ultérieur**. L'historique Git conserve tout. Des attaquants scannent régulièrement les dépôts publics à la recherche de clés et tokens exposés.

### La bonne approche : .env + .env.example

Utilisez des fichiers `.env` pour les variables sensibles et ajoutez-les au `.gitignore`. Fournissez un fichier `.env.example` (sans les vraies valeurs) que vous commitez :

```bash
# .env.example — À COMMITER (valeurs fictives)
APP_ENV=dev
APP_SECRET=change_me_with_a_real_secret
DATABASE_URL="mysql://user:password@127.0.0.1:3306/mydb?charset=utf8mb4"
MAILER_DSN=smtp://localhost
STRIPE_API_KEY=sk_test_xxxxxxxxx
```

```bash
# .env — À NE JAMAIS COMMITER (valeurs réelles)
APP_ENV=prod
APP_SECRET=a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
DATABASE_URL="mysql://real_user:real_pass@db-server:3306/prod_db"
MAILER_DSN=smtp://user:pass@smtp.provider.com:587
STRIPE_API_KEY=sk_live_xxxxxxxxx
```

### Si un secret a été commité par erreur

**Si le secret n'a PAS encore été poussé :**

```bash
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit --amend
```

**Si le secret a été poussé sur GitHub :**

1. **Considérez-le comme compromis immédiatement**
2. **Changez le mot de passe / révoquez la clé API** en priorité
3. Nettoyez l'historique avec [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) :

```bash
bfg --delete-files .env
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force
```

> ⚠️ La priorité absolue est de **révoquer le secret**, pas de nettoyer l'historique.

### Bonnes pratiques de sécurité supplémentaires

- Activez l'**authentification à deux facteurs** (2FA) sur GitHub
- Utilisez des **clés SSH** plutôt que HTTPS avec mot de passe
- Activez les **règles de protection de branche** sur `main` (interdire les push directs, exiger des reviews)
- Utilisez des outils comme **git-secrets** ou **gitleaks** pour scanner votre code avant de commiter
- GitHub propose un service de **Secret Scanning** qui détecte automatiquement les tokens exposés dans les dépôts publics

---

## 12. Le fichier README.md

Le `README.md` est la **vitrine** de votre projet sur GitHub. C'est la première chose que les visiteurs voient. Un bon README rend votre projet professionnel et accessible ; un mauvais README (ou pire, son absence) fait fuir les collaborateurs et les recruteurs.

### Structure recommandée

```markdown
# Nom du projet

Brève description du projet en une ou deux phrases.

## Prérequis

- PHP 8.x
- Composer 2.x
- Symfony CLI
- Docker & Docker Compose (optionnel)
- Node.js 20+ (si front-end)

## Installation

### Cloner le dépôt
git clone git@github.com:username/project.git
cd project

### Installer les dépendances
composer install
npm install  # si applicable

### Configurer l'environnement
cp .env.example .env
# Éditer .env avec vos paramètres

### Créer la base de données
php bin/console doctrine:database:create
php bin/console doctrine:migrations:migrate

### Lancer le serveur
symfony server:start

## Utilisation

Commandes utiles et explication du fonctionnement.

## Structure du projet

Arborescence ou explication de l'organisation du code.

## Contribution

1. Forker le projet
2. Créer une branche (`git checkout -b feature/ma-feature`)
3. Commiter (`git commit -m 'feat: add ma feature'`)
4. Pusher (`git push origin feature/ma-feature`)
5. Ouvrir une Pull Request

## Technologies

- Symfony 7.4
- PHP 8.4
- MariaDB 10.11
- Bootstrap 5.3
- Docker

## Auteur(s)

- **Nom** — [GitHub](https://github.com/username)

## Licence

Ce projet est sous licence MIT — voir le fichier [LICENSE](LICENSE).
```

### Bonnes pratiques

- Maintenez le README **à jour** à chaque changement significatif
- Incluez des **exemples de commandes** copiables (avec les blocs de code)
- Mentionnez les **technologies** utilisées et leurs versions
- Si le projet est visuel, ajoutez des **captures d'écran**
- Le `README.md` doit être présent **dès le premier commit**

---

## 13. Commandes utiles au quotidien

### Vérifier l'état du dépôt

```bash
git status                    # fichiers modifiés, ajoutés, supprimés
git diff                      # différences non indexées
git diff --staged             # différences indexées (prêtes à commiter)
git diff main..feature/xxx    # différences entre deux branches
```

### Consulter l'historique

```bash
git log                          # historique complet
git log --oneline                # une ligne par commit
git log --oneline --graph --all  # graphe avec toutes les branches
git log --author="Michel"        # commits d'un auteur
git log --since="2 weeks ago"    # commits des 2 dernières semaines
git log -- chemin/du/fichier     # historique d'un fichier
git log --grep="fix"             # commits contenant "fix"
```

### Annuler et corriger

```bash
# Modifier le dernier commit (message ou contenu)
git commit --amend -m "nouveau message"

# Désindexer un fichier (le retirer du staging)
git restore --staged nom_du_fichier

# Annuler les modifications d'un fichier (ATTENTION: irréversible !)
git restore nom_du_fichier

# Annuler un commit en créant un nouveau commit d'annulation (safe)
git revert <hash_du_commit>

# Revenir à un état antérieur (DANGER: réécrit l'historique)
git reset --hard <hash_du_commit>
```

> ⚠️ `git reset --hard` supprime les modifications de manière **irréversible**. Utilisez `git revert` en cas de doute.

### Le stash (rangement temporaire)

```bash
git stash save "description de ce que je faisais"   # ranger
git stash list                                        # voir les stash
git stash pop                                         # récupérer le dernier
git stash apply stash@{2}                             # récupérer un spécifique
git stash drop stash@{0}                              # supprimer un stash
git stash clear                                       # tout supprimer
```

### Travailler avec les remotes

```bash
git remote -v                              # voir les dépôts distants
git remote add upstream <URL>              # ajouter un remote
git fetch origin                           # récupérer les infos (sans merger)
git fetch --all                            # récupérer de tous les remotes
git pull origin main                       # fetch + merge
git push origin feature/ma-branche         # pousser une branche
git push -u origin feature/ma-branche      # pousser + définir le tracking
```

### Tags (marquage de versions)

```bash
git tag                                     # lister les tags
git tag -a v1.0.0 -m "Version 1.0.0"       # créer un tag annoté
git tag -a v1.0.1 <hash> -m "Hotfix"       # tagger un ancien commit
git push origin v1.0.0                      # pousser un tag spécifique
git push origin --tags                      # pousser tous les tags
```

### Rechercher dans l'historique

```bash
# Trouver le commit qui a introduit un bug
git bisect start
git bisect bad                   # le commit actuel est bugué
git bisect good <hash_ancien>    # ce commit ancien était OK
# Git va naviguer dans l'historique, testez et répondez good/bad
git bisect reset                 # terminer la recherche

# Trouver qui a modifié chaque ligne d'un fichier
git blame chemin/du/fichier
```

---

## 14. Récapitulatif : la checklist des bonnes pratiques

### ✅ Configuration

- [ ] Configurer `user.name` et `user.email`
- [ ] Configurer `init.defaultBranch main`
- [ ] Configurer `pull.rebase true`
- [ ] Créer des alias pour les commandes fréquentes
- [ ] Configurer un `.gitignore` global pour votre machine

### ✅ Fichiers du projet

- [ ] Créer `.gitignore` **avant** le premier commit
- [ ] Utiliser `.gitkeep` pour les dossiers vides nécessaires
- [ ] Maintenir un `README.md` complet et à jour
- [ ] Fournir un `.env.example` (jamais le vrai `.env`)

### ✅ Commits

- [ ] Un commit = une unité logique de travail (**atomicité**)
- [ ] Sujet en impératif, ≤ 50 caractères, commençant par une majuscule
- [ ] Adopter les **Conventional Commits** (`feat:`, `fix:`, `docs:`, etc.)
- [ ] Commiter souvent, mais toujours du code fonctionnel
- [ ] Vérifier avec `git diff --staged` avant chaque commit
- [ ] Ne **JAMAIS** commiter de secrets, mots de passe ou clés API

### ✅ Branches

- [ ] Ne **JAMAIS** travailler directement sur `main`
- [ ] Utiliser des préfixes cohérents (`feature/`, `bugfix/`, `hotfix/`)
- [ ] Noms en minuscules, séparés par des tirets, en anglais
- [ ] Garder les branches à jour avec `main` (rebase ou merge)
- [ ] Supprimer les branches fusionnées

### ✅ Collaboration

- [ ] Utiliser le workflow **Fork + Pull Request** en équipe
- [ ] Configurer `upstream` pour le dépôt d'équipe
- [ ] Faire des PR **courtes et focalisées** (< 400 lignes)
- [ ] Relire le code des collègues (**code review**)
- [ ] Synchroniser son fork avec `upstream` régulièrement
- [ ] Ne **JAMAIS** faire `git push --force` sur une branche partagée

### ✅ Sécurité

- [ ] Activer le **2FA** sur GitHub
- [ ] Utiliser des **clés SSH** (pas HTTPS avec mot de passe)
- [ ] Protéger la branche `main` (règles de protection)
- [ ] Scanner le code pour les secrets avant de pousser

---

## 15. Ressources

### Documentation officielle

- [Documentation Git](https://git-scm.com/doc)
- [Pro Git — Le livre (gratuit, en français)](https://git-scm.com/book/fr/v2)
- [Documentation GitHub](https://docs.github.com/fr)
- [GitHub Skills — Cours interactifs](https://skills.github.com)

### Cours en ligne

- [OpenClassrooms — Gérez du code avec Git et GitHub](https://openclassrooms.com/fr/courses/7162856-gerez-du-code-avec-git-et-github)
- [Grafikart — Tutoriels Git](https://grafikart.fr/tutoriels/init-config-add-log-585#autoplay)

### Spécifications et outils

- [Conventional Commits (spécification)](https://www.conventionalcommits.org/fr/v1.0.0/)
- [gitignore.io — Générateur de .gitignore](https://www.toptal.com/developers/gitignore)
- [github/gitignore — Collection de templates](https://github.com/github/gitignore)
- [BFG Repo-Cleaner — Nettoyer l'historique](https://rtyley.github.io/bfg-repo-cleaner/)

### Cours CF2M

- [Préformation Git 2026](https://github.com/WebDevCF2m2026/prefo-git-2026)
- [Base Git Classe 1](https://github.com/WebDevCF2m2026/base-git-classe-1)
- [Organisation WebDevCF2m2026](https://github.com/WebDevCF2m2026)

---

*Cours rédigé pour la formation WebDev CF2M 2026 — Février 2026*
