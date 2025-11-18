# Workflow d'ajout de commande

Ce diagramme illustre le workflow de la commande d'ajout pour installer des commandes Claude Code individuelles.

```mermaid
flowchart TD
    Start([L'utilisateur exécute: aiblueprint claude-code add commands]) --> HasArg{Nom de commande<br/>spécifié?}

    HasArg -->|Non| ListMode[Mode liste:<br/>Afficher toutes les commandes disponibles]
    HasArg -->|Oui| InstallMode[Mode installation:<br/>Installer une commande spécifique]

    ListMode --> FetchList{Récupérer la liste des commandes}

    FetchList --> GHList{GitHub<br/>disponible?}

    GHList -->|Oui| ListGH[Lister depuis l'API GitHub<br/>github.com/api/contents/commands]
    GHList -->|Non| ListLocal[Lister depuis les fichiers locaux<br/>claude-code-config/commands/]

    ListGH --> ParseMetadata
    ListLocal --> ParseMetadata

    ParseMetadata[Parser le Frontmatter YAML<br/>Extraire: description, allowed-tools, argument-hint] --> DisplayList[Afficher la liste formatée:<br/>- Nom de la commande<br/>- Description<br/>- Syntaxe d'utilisation<br/>- Outils autorisés]

    DisplayList --> EndList([L'utilisateur peut maintenant exécuter la commande d'installation])

    InstallMode --> ValidateCmd{Nom de commande<br/>valide?}

    ValidateCmd -->|Non| ErrorCmd([❌ Erreur: Commande introuvable])
    ValidateCmd -->|Oui| CheckExist{Le fichier de commande<br/>existe déjà?}

    CheckExist -->|Oui| PromptOverwrite{Demander à l'utilisateur:<br/>Écraser l'existant?}
    CheckExist -->|Non| Download

    PromptOverwrite -->|Non| Cancel([❌ Opération annulée])
    PromptOverwrite -->|Oui| Download

    Download[Télécharger la commande] --> GHCheck{GitHub<br/>disponible?}

    GHCheck -->|Oui| DownloadGH[Télécharger depuis GitHub<br/>raw.githubusercontent.com]
    GHCheck -->|Non| UseLocal[Copier depuis les fichiers locaux<br/>claude-code-config/commands/]

    DownloadGH --> WriteCmd[Écrire dans ~/.claude/commands/]
    UseLocal --> WriteCmd

    WriteCmd --> Success([✅ Commande installée<br/>Prêt à utiliser: /command-name])

    style Start fill:#e1f5ff
    style Success fill:#d4edda
    style EndList fill:#d4edda
    style ErrorCmd fill:#f8d7da
    style Cancel fill:#f8d7da
    style ParseMetadata fill:#d1ecf1
```

## Commandes disponibles (16 templates)

### Workflow de développement
- `/commit` - Commits conventionnels rapides avec auto-push
- `/create-pull-request` - Créer une PR avec résumé et plan de test
- `/fix-pr-comments` - Traiter les commentaires de revue de PR
- `/run-tasks` - Exécuter les tâches du projet efficacement

### Analyse de code
- `/deep-code-analysis` - Revue de code complète
- `/explain-architecture` - Documenter l'architecture du système

### Gestion de projet
- `/claude-memory` - Gérer la mémoire de projet de Claude
- `/cleanup-context` - Nettoyer le contexte de conversation

### Utilitaires
- `/epct` - Exécuter et paralléliser des tâches complexes
- `/prompt-command` - Générer de nouveaux templates de commandes
- `/prompt-agent` - Générer de nouveaux templates d'agents
- `/watch-ci` - Surveiller le pipeline CI/CD

### Et plus encore...

## Structure de commande

Chaque commande est un fichier Markdown avec:

```markdown
---
description: "Description de la commande"
allowed-tools: "Bash, Read, Edit"
argument-hint: "<required-arg> [optional-arg]"
---

# Instructions de la commande

[Instructions détaillées pour Claude...]
```

## Fichiers associés

- Source: `src/commands/addCommand.ts`
- Répertoire des commandes: `claude-code-config/commands/`
- Utilitaires: `src/utils/claude-config.ts` (parsing YAML)
