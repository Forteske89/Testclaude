# Testclaude

## Description

Ce dépôt est un bac à sable destiné à expérimenter l'intégration de [Claude](https://claude.com/claude-code) avec GitHub via GitHub Actions. Il ne contient pas de code applicatif : son seul rôle est de servir de terrain de test pour des interactions automatisées entre Claude et les issues / pull requests du dépôt.

## Objectif

L'objectif de ce repo est de **tester l'intégration GitHub Actions de Claude**. Concrètement, il permet de vérifier que :

- l'action [`anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action) est correctement configurée ;
- les permissions du workflow et le secret `ANTHROPIC_API_KEY` sont bien en place ;
- Claude peut lire le contexte d'une issue ou d'une PR, répondre dans les commentaires, et proposer des changements (commits, branches, liens de création de PR).

Ce dépôt sert donc principalement de référence pour valider la configuration avant de déployer la même intégration sur des projets réels.

## Comment ça marche

L'intégration repose sur un unique workflow GitHub Actions : [`.github/workflows/claude.yml`](.github/workflows/claude.yml).

Ce workflow se déclenche automatiquement lorsque :

- une **issue est ouverte ou assignée** et contient `@claude` dans son titre ou son corps ;
- un **commentaire d'issue** contenant `@claude` est créé ;
- un **commentaire de revue de PR** contenant `@claude` est créé ;
- une **revue de PR** soumise contient `@claude`.

Lorsqu'un de ces événements survient, le job exécute l'action `anthropics/claude-code-action@v1`, qui :

1. Lit le contexte (issue, PR, commentaires, fichiers du repo) ;
2. Analyse la demande adressée à `@claude` ;
3. Répond dans un commentaire dédié, en mettant à jour ce même commentaire au fil de sa progression ;
4. Si la demande implique des modifications de code, pousse un commit sur une branche `claude/...` et fournit un lien prêt à l'emploi pour ouvrir la pull request.

### Mention `@claude`

Pour solliciter Claude depuis ce dépôt, il suffit de mentionner `@claude` dans une issue ou un commentaire, suivi de la demande en langage naturel. Par exemple :

> @claude peux-tu ajouter un fichier `CONTRIBUTING.md` avec les conventions de commit ?

### Permissions et secrets requis

Le workflow nécessite :

- le secret `ANTHROPIC_API_KEY` configuré dans les *Settings → Secrets and variables → Actions* du dépôt ;
- les permissions `contents: write`, `pull-requests: write`, `issues: write`, `id-token: write` et `actions: read` sur le job (déjà déclarées dans `claude.yml`).
