# Guide d'installation de l'application GitHub Claude Code

## Guide complet pour Windows 11 + WSL2 (Ubuntu 24.04) + VS Code

Ce guide explique comment installer et configurer l'application GitHub Claude Code pour automatiser les revues de code sur vos pull requests.

---

## 📋 Prérequis

Avant de commencer, assurez-vous d'avoir :

- **Windows 11** avec **WSL2** activé
- **Ubuntu 24.04** installé dans WSL2
- **VS Code** avec l'extension **Remote - WSL**
- **Claude Code** installé dans WSL
- Un compte **GitHub** (exemple : Touadi85)
- Un dépôt Git relié à votre compte GitHub

---

## 🚀 Étape 1 : Préparation de l'environnement

### Ouvrir votre projet dans WSL

1. **Ouvrez VS Code**
2. Connectez-vous à **Remote - WSL** (icône en bas à gauche de VS Code)
3. Ouvrez le dossier de votre projet (ex : `~/projects/IceBreaker`)
4. Ouvrez un **terminal WSL** dans VS Code (`Ctrl + ù` ou Terminal → New Terminal)

> ⚠️ **Important** : Toutes les commandes doivent être exécutées dans le **terminal WSL**, pas dans PowerShell Windows.

---

## 🔧 Étape 2 : Installation de GitHub CLI

GitHub CLI (`gh`) est nécessaire pour l'authentification et la gestion des pull requests.

### Installation sur Ubuntu 24.04

```bash
# Mettre à jour les paquets
sudo apt update

# Installer GitHub CLI
sudo apt install -y gh

# Vérifier l'installation
gh --version
```

Vous devriez voir une version affichée (ex : `gh version 2.x.x`).

---

## 🔐 Étape 3 : Authentification GitHub

### Se connecter à GitHub

```bash
gh auth login
```

### Configuration interactive

Suivez ces étapes :

1. **Sélectionnez** : `GitHub.com`
2. **Protocole** : `HTTPS`
3. **Méthode d'authentification** : `Login with a web browser`

### Particularité WSL : Validation dans le navigateur Windows

Après avoir choisi "web browser", vous verrez :

```
! First copy your one-time code: XXXX-YYYY
Press Enter to open github.com in your browser...
```

**Dans WSL, le navigateur ne s'ouvre pas automatiquement.** Voici comment procéder :

1. **Copiez le code** affiché (ex : `XXXX-YYYY`)
2. **Appuyez sur Entrée** dans le terminal
3. Si aucun navigateur ne s'ouvre, **ouvrez manuellement** dans Windows :
   - Allez sur : `https://github.com/login/device`
4. **Collez le code** et autorisez l'accès
5. Revenez au terminal WSL

### Vérification

```bash
gh auth status
```

Vous devriez voir :

```
✓ Logged in to github.com as Touadi85 (...)
✓ Git operations for github.com configured to use https protocol.
```

---

## 📂 Étape 4 : Vérification du dépôt Git

Avant d'installer l'application Claude Code, vérifiez que vous êtes dans un dépôt Git valide relié à **votre compte GitHub**.

```bash
# Vérifier le dossier courant
pwd

# Vérifier le statut Git
git status

# Vérifier les dépôts distants
git remote -v
```

### Points de contrôle

✅ Le remote `origin` doit pointer vers **votre dépôt GitHub** :

```
origin  https://github.com/Touadi85/IceBreaker.git (fetch)
origin  https://github.com/Touadi85/IceBreaker.git (push)
```

❌ Si `origin` pointe vers un autre compte (ex : un fork), vous n'aurez pas les droits nécessaires.

**Solution** : Modifiez le remote pour pointer vers votre dépôt :

```bash
git remote set-url origin https://github.com/Touadi85/IceBreaker.git
```

---

## 🤖 Étape 5 : Installation de l'application Claude Code

### Lancer Claude Code

Depuis le terminal WSL dans le dossier de votre projet :

```bash
claude
```

### Exécuter la commande d'installation

Dans Claude Code, tapez :

```
/install-github-app
```

### Gestion de l'avertissement WSL

Vous pourriez voir un message comme :

```
⚠️ GitHub CLI not authenticated
```

**Ne paniquez pas !** Si vous avez déjà fait `gh auth login` avec succès, c'est un faux négatif.

➡️ **Appuyez simplement sur Entrée pour continuer.**

### Installation de l'application GitHub

Claude Code vous donnera un lien vers :

```
https://github.com/apps/claude
```

**Marche à suivre** :

1. **Ouvrez le lien** dans votre navigateur Windows
2. **Cliquez** sur "Install" ou "Configure"
3. **Sélectionnez votre compte** : Touadi85
4. **Choisissez les dépôts** :
   - Option 1 : **All repositories** (tous vos dépôts)
   - Option 2 : **Only select repositories** → Sélectionnez `IceBreaker`
5. **Cliquez** sur "Install" ou "Save"

### Finalisation

Revenez au terminal WSL et **appuyez sur Entrée** quand demandé.

Vous devriez voir :

```
✅ GitHub Actions setup complete!
```

---

## 📝 Étape 6 : Configuration Git et création du PR

### Configurer votre identité Git (si nécessaire)

Si Git vous demande de configurer votre identité :

```bash
# Configuration locale (uniquement pour ce projet)
git config user.name "Touadi85"
git config user.email "touadi.ilies@gmail.com"

# OU configuration globale (pour tous les projets)
git config --global user.name "Touadi85"
git config --global user.email "touadi.ilies@gmail.com"
```

### Vérifier les modifications

Claude a probablement modifié/créé des fichiers de workflows :

```bash
git status
```

### Créer une branche et commiter

```bash
# Créer une nouvelle branche
git checkout -b add-claude-github-integration

# Ajouter les fichiers modifiés
git add .github/workflows/

# Créer le commit
git commit -m "Add Claude Code GitHub integration"

# Pousser vers GitHub
git push -u origin add-claude-github-integration
```

### Créer le Pull Request

```bash
gh pr create --title "Add Claude Code GitHub integration" --body "Configure automated code reviews with Claude Code"
```

GitHub vous donnera un lien vers le PR créé.

---

## ⚠️ Erreur normale au premier PR

Lors du **premier PR** après l'installation, vous verrez probablement cette erreur dans GitHub Actions :

```
Error: Workflow validation failed. The workflow file must exist and have
identical content to the version on the repository's default branch.
```

### C'est normal ! 🎉

Cette erreur apparaît parce que :

1. Les fichiers de workflow sont dans votre PR
2. Mais ils n'existent pas encore dans la branche `main`
3. GitHub exige que les workflows soient dans `main` pour des raisons de sécurité

### Solution

**Fusionnez simplement ce premier PR !**

1. Allez sur votre PR dans GitHub
2. Cliquez sur **"Merge pull request"**
3. Confirmez avec **"Confirm merge"**

Une fois fusionné, tous les **prochains PRs** seront automatiquement analysés par Claude Code ! ✨

---

## 🔍 Dépannage

### Problème : `gh auth login` ne fonctionne pas

**Solution** : Assurez-vous d'être dans WSL (pas PowerShell) et que GitHub CLI est bien installé :

```bash
which gh
gh --version
```

### Problème : Le navigateur ne s'ouvre pas sous WSL

**Solution** : C'est normal sous WSL. Ouvrez manuellement l'URL dans Windows :

```
https://github.com/login/device
```

### Problème : "Permission denied" lors du push

**Vérifications** :

1. Êtes-vous bien authentifié ?
   ```bash
   gh auth status
   ```

2. Le remote pointe-t-il vers votre dépôt ?
   ```bash
   git remote -v
   ```

3. Avez-vous les droits sur le dépôt GitHub ?

### Problème : Claude Code dit "not a git repository"

**Solution** : Assurez-vous d'être dans le bon dossier :

```bash
cd ~/projects/IceBreaker
git status
```

---

## ✅ Vérification finale

Une fois tout installé, vérifiez :

- [ ] GitHub CLI installé : `gh --version`
- [ ] Authentification OK : `gh auth status`
- [ ] Application Claude installée sur GitHub : https://github.com/apps/claude
- [ ] Workflows GitHub Actions présents : `.github/workflows/claude-code-review.yml`
- [ ] Premier PR fusionné dans `main`

---

## 🎯 Utilisation quotidienne

Maintenant que tout est configuré :

1. **Créez une branche** pour vos modifications
2. **Commitez et poussez** vos changements
3. **Créez un PR** via `gh pr create` ou l'interface GitHub
4. **Claude analysera automatiquement** votre code ! 🤖
5. Vous recevrez des suggestions directement dans le PR

---

## 📚 Ressources

- [Documentation Claude Code](https://docs.anthropic.com/claude/docs)
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [WSL2 Documentation](https://docs.microsoft.com/windows/wsl/)

---

**Auteur** : Touadi85
**Date** : Février 2026
**Environnement** : Windows 11 + WSL2 (Ubuntu 24.04) + VS Code + Claude Code
