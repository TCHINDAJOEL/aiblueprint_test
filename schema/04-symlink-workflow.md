# Workflow de création de liens symboliques

Ce diagramme illustre le workflow de création de liens symboliques pour partager des commandes/agents entre différents outils CLI d'IA.

```mermaid
flowchart TD
    Start([L'utilisateur exécute: aiblueprint claude-code symlink]) --> SelectSource[Interactif: Sélectionner l'outil source]

    SelectSource --> SourceOptions{Sélection de l'outil<br/>source}

    SourceOptions --> ClaudeCode[Claude Code<br/>~/.claude/]
    SourceOptions --> Codex[Codex<br/>~/.codex/prompts]
    SourceOptions --> OpenCode[OpenCode<br/>~/.config/opencode/command]
    SourceOptions --> FactoryAI[FactoryAI<br/>~/.factory/]

    ClaudeCode --> SelectContent
    Codex --> SelectContent
    OpenCode --> SelectContent
    FactoryAI --> SelectContent

    SelectContent{Sélectionner le type de contenu}

    SelectContent --> Commands[Commandes uniquement]
    SelectContent --> Agents[Agents uniquement<br/>si supporté]
    SelectContent --> Both[Commandes + Agents]

    Commands --> SelectDest
    Agents --> SelectDest
    Both --> SelectDest

    SelectDest[Multi-sélection: Choisir les outils de destination] --> DestCheck{Pour chaque<br/>destination}

    DestCheck --> ValidatePath{Chemin de destination<br/>valide?}

    ValidatePath -->|Non| Skip[Ignorer cette destination<br/>Logger un avertissement]
    ValidatePath -->|Oui| CheckExist{Le répertoire cible<br/>existe?}

    CheckExist -->|Non| CreateSymlink
    CheckExist -->|Oui| IsSymlink{Déjà un<br/>lien symbolique?}

    IsSymlink -->|Oui| SkipExist[Ignorer: Déjà lié]
    IsSymlink -->|Non| PromptReplace{Demander:<br/>Remplacer par un lien symbolique?}

    PromptReplace -->|Non| SkipManual[Ignorer: Garder le répertoire existant]
    PromptReplace -->|Oui| Backup[Sauvegarder le répertoire existant<br/>dirname.backup]

    Backup --> CreateSymlink[Créer le lien symbolique:<br/>ln -s source target]

    CreateSymlink --> NextDest{Plus de<br/>destinations?}
    Skip --> NextDest
    SkipExist --> NextDest
    SkipManual --> NextDest

    NextDest -->|Oui| DestCheck
    NextDest -->|Non| Report[Générer le rapport récapitulatif:<br/>- Créés: X<br/>- Ignorés: Y<br/>- Échoués: Z]

    Report --> Success([✅ Opération de lien symbolique terminée])

    style Start fill:#e1f5ff
    style Success fill:#d4edda
    style CreateSymlink fill:#cfe2ff
    style Backup fill:#fff3cd
```

## Outils supportés

### Options de source et de destination

| Outil | Chemin des commandes | Chemin des agents | Support |
|------|--------------|-------------|---------|
| **Claude Code** | `~/.claude/commands/` | `~/.claude/agents/` | Complet |
| **Codex** | `~/.codex/prompts` | N/A | Commandes uniquement |
| **OpenCode** | `~/.config/opencode/command` | N/A | Commandes uniquement |
| **FactoryAI** | `~/.factory/commands/` | `~/.factory/droids/` | Complet |

## Stratégie de liens symboliques

### Avantages
- **Source unique de vérité**: Mettre à jour les commandes en un seul endroit
- **Compatibilité inter-outils**: Utiliser les mêmes commandes sur différents CLI d'IA
- **Maintenance facile**: Les changements se propagent automatiquement

### Vérifications de sécurité
1. Valide tous les chemins avant de créer des liens symboliques
2. Détecte les répertoires existants non-symboliques
3. Propose une sauvegarde avant le remplacement
4. Ignore les destinations invalides de manière élégante

### Exemple de cas d'usage

Partager les commandes Claude Code avec Codex:
```bash
aiblueprint claude-code symlink
# Sélectionner: Claude Code (source)
# Sélectionner: Commandes
# Sélectionner: Codex (destination)
# Résultat: ~/.codex/prompts -> ~/.claude/commands
```

## Fichiers associés

- Source: `src/commands/symlink.ts`
- Utilitaires de chemins: `src/utils/claude-config.ts`

## Notes importantes

- Les liens symboliques sont bidirectionnels (peuvent synchroniser de n'importe quel outil vers n'importe quel autre)
- Le support des agents dépend des capacités de l'outil
- Des chemins de dossier personnalisés peuvent être spécifiés via les options CLI
