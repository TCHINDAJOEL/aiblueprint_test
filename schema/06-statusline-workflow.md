# Workflow de la Statusline

Ce diagramme illustre comment la statusline personnalisée affiche les informations de session en temps réel.

```mermaid
flowchart TD
    Start([Session Claude Code active]) --> HookTrigger[Hook StatusLine déclenché<br/>À chaque prompt/réponse]

    HookTrigger --> ReadInput[Lire l'entrée du hook depuis stdin<br/>Format JSON]

    ReadInput --> ParseInput[Parser l'entrée JSON:<br/>- Type de hook<br/>- Données de session<br/>- Chemin de transcript]

    ParseInput --> Parallel{Récupérer les données<br/>en parallèle}

    Parallel --> GitStatus[Récupérer le statut Git]
    Parallel --> ParseTranscript[Parser le fichier de transcript]
    Parallel --> APICall[Appeler l'API OAuth Claude]

    %% GIT STATUS BRANCH
    GitStatus --> RunGit[Exécuter: git status --porcelain -b]

    RunGit --> ParseGit[Parser la sortie Git:<br/>- Branche actuelle<br/>- Compteur ahead/behind<br/>- Fichiers modifiés<br/>- Fichiers non suivis]

    ParseGit --> GitData[Données Git prêtes:<br/>branche, changements, statut]

    %% TRANSCRIPT BRANCH
    ParseTranscript --> ReadFile[Lire le JSON du transcript]

    ReadFile --> ExtractContext[Extraire l'utilisation du contexte:<br/>- Tokens d'entrée<br/>- Tokens de sortie<br/>- Total de tokens<br/>- Modèle utilisé]

    ExtractContext --> CalculateCost[Calculer le coût de la session:<br/>Basé sur la tarification du modèle]

    CalculateCost --> ContextData[Données de contexte prêtes:<br/>tokens, coût, durée]

    %% API BRANCH
    APICall --> HTTPRequest[GET api.claude.ai/api/organizations/.../usage]

    HTTPRequest --> ParseAPI[Parser la réponse API:<br/>- Limites de taux<br/>- Utilisation actuelle<br/>- Pourcentage utilisé<br/>- Temps de réinitialisation]

    ParseAPI --> UsageData[Données d'utilisation prêtes:<br/>limites, pourcentage]

    %% MERGE AND FORMAT
    GitData --> MergeData[Fusionner toutes les données]
    ContextData --> MergeData
    UsageData --> MergeData

    MergeData --> FormatLine1[Formater Ligne 1:<br/>branche | chemin | modèle]

    FormatLine1 --> FormatLine2[Formater Ligne 2:<br/>coût | durée | tokens | usage%]

    FormatLine2 --> ColorFormat[Appliquer le formatage de couleur:<br/>- Vert: utilisation sûre<br/>- Jaune: modéré<br/>- Rouge: utilisation élevée]

    ColorFormat --> Output[Sortie vers stdout:<br/>2 lignes avec couleurs ANSI]

    Output --> Display[Claude Code affiche:<br/>Statusline dans le terminal]

    Display --> NextPrompt{L'utilisateur envoie<br/>le prompt suivant?}

    NextPrompt -->|Oui| HookTrigger
    NextPrompt -->|Non| End([Session terminée])

    style Start fill:#e1f5ff
    style End fill:#d4edda
    style Parallel fill:#fff3cd
    style MergeData fill:#d1ecf1
    style Display fill:#cfe2ff
```

## Format de sortie de la Statusline

### Ligne 1: Informations de contexte
```
🌿 main | ~/projects/app | sonnet-4.5
```
- Branche Git avec icône
- Répertoire de travail actuel
- Modèle actif

### Ligne 2: Métriques
```
💰 $0.45 | ⏱️  2m 34s | 📊 25K/200K (12%) | 🔥 450/500 (90%)
```
- Coût de la session
- Durée
- Utilisation des tokens (actuel/limite)
- Pourcentage de limite de taux

## Sources de données

### 1. Statut Git
**Commande**: `git status --porcelain -b`

**Informations extraites**:
- Nom de la branche actuelle
- Compteur ahead/behind du remote
- Nombre de fichiers modifiés
- Nombre de fichiers non suivis

### 2. Parsing du Transcript
**Fichier**: `.claude/transcript-<session-id>.json`

**Informations extraites**:
- Tokens d'entrée par message
- Tokens de sortie par message
- Total cumulé
- Identifiant du modèle

### 3. API OAuth Claude
**Endpoint**: `https://api.claude.ai/api/organizations/{org_id}/usage`

**Informations extraites**:
- Limites de taux journalières/horaires
- Compteur d'utilisation actuel
- Pourcentage consommé
- Horodatage de réinitialisation

## Optimisations de performance

1. **Exécution parallèle**: Les 3 sources de données sont récupérées simultanément
2. **Mise en cache**: Le statut Git est mis en cache pendant 1 seconde
3. **Gestion des erreurs**: Dégradation élégante si l'API échoue
4. **Traitement minimal**: Seuls les champs JSON nécessaires sont parsés

## Schéma de couleurs

| Usage % | Couleur | Signification |
|---------|-------|---------|
| 0-50% | Vert | Sûr |
| 51-75% | Jaune | Modéré |
| 76-100% | Rouge | Élevé |

## Fichiers associés

- Script: `claude-code-config/scripts/statusline/src/index.ts`
- Package: `claude-code-config/scripts/statusline/package.json`
- Installateur: `src/commands/statusline.ts`
- Dépendances: `ccusage` (calcul du coût)

## Installation

**Autonome**:
```bash
aiblueprint claude-code statusline
```

**Via Setup**:
```bash
aiblueprint claude-code setup
# Sélectionner "Custom Statusline"
```

## Prérequis

- **Bun**: Runtime pour le script de statusline
- **ccusage**: Calcul du coût des tokens
- **Git**: Détection du dépôt
- **API Claude**: Données de limite de taux
