# CLI AIBlueprint - Diagrammes d'architecture & de workflow

Ce dossier contient des diagrammes Mermaid complets documentant tous les workflows et motifs architecturaux du projet CLI AIBlueprint.

## 📋 Table des matières

1. [Workflow de la commande Setup](#1-workflow-de-la-commande-setup)
2. [Workflow d'ajout de Hook](#2-workflow-dajout-de-hook)
3. [Workflow d'ajout de commande](#3-workflow-dajout-de-commande)
4. [Workflow de Symlink](#4-workflow-de-symlink)
5. [Workflow des commandes Pro](#5-workflow-des-commandes-pro)
6. [Workflow de Statusline](#6-workflow-de-statusline)
7. [Flux du Hook de sécurité](#7-flux-du-hook-de-sécurité)
8. [Architecture du CLI](#8-architecture-du-cli)
9. [Flux d'installation](#9-flux-dinstallation)
10. [Workflow de test](#10-workflow-de-test)

---

## Vue d'ensemble

Ces diagrammes fournissent une documentation visuelle de:

- **Workflows orientés utilisateur**: Comment les utilisateurs interagissent avec le CLI
- **Processus internes**: Comment le système traite les commandes
- **Mécanismes de sécurité**: Comment fonctionne la validation de sécurité
- **Flux de données**: Comment les données circulent dans le système
- **Stratégies de test**: Comment le système est validé

Tous les diagrammes sont écrits en format Mermaid et peuvent être visualisés:
- Sur GitHub (rendu automatique)
- Dans VS Code (avec l'extension Mermaid)
- En ligne sur [mermaid.live](https://mermaid.live)

---

## Diagrammes

### 1. Workflow de la commande Setup
**Fichier**: [`01-setup-workflow.md`](./01-setup-workflow.md)

**Description**: La commande setup principale qui installe toutes les configurations AIBlueprint.

**Fonctionnalités clés**:
- Sélection interactive de fonctionnalités
- GitHub en premier avec repli local
- Installation conditionnelle de fonctionnalités
- Logique de fusion de settings.json
- Auto-installation des dépendances

**Cas d'usage**:
- Installation initiale
- Mises à jour de fonctionnalités
- Rafraîchissement de configuration

---

### 2. Workflow d'ajout de Hook
**Fichier**: [`02-add-hook-workflow.md`](./02-add-hook-workflow.md)

**Description**: Installation de hooks Claude Code individuels pour fonctionnalités améliorées.

**Fonctionnalités clés**:
- Validation de type de hook
- Détection projet vs global
- Utilisation de variables d'environnement (`$CLAUDE_PROJECT_DIR`)
- Gestion des permissions exécutables

**Hooks supportés**:
- `post-edit-typescript`: Validation TypeScript après édition

**Cas d'usage**:
- Ajout de hooks spécifiques au projet
- Installation de hooks de validation
- Configuration de hooks CI/CD

---

### 3. Workflow d'ajout de commande
**Fichier**: [`03-add-command-workflow.md`](./03-add-command-workflow.md)

**Description**: Installation de templates de commandes individuels ou liste des commandes disponibles.

**Fonctionnalités clés**:
- Découverte de commandes (mode liste)
- Parsing de frontmatter YAML
- Installation de commande individuelle
- Extraction de métadonnées (description, allowed-tools)

**Commandes disponibles**: 16 templates préconfigurés incluant:
- `/commit`: Commits rapides
- `/create-pull-request`: Création de PR
- `/deep-code-analysis`: Revue de code
- Et 13 autres...

**Cas d'usage**:
- Ajout de commandes spécifiques
- Navigation des commandes disponibles
- Installation de templates personnalisés

---

### 4. Workflow de Symlink
**Fichier**: [`04-symlink-workflow.md`](./04-symlink-workflow.md)

**Description**: Partage de commandes et agents entre différents outils CLI d'IA.

**Fonctionnalités clés**:
- Support multi-outils (Claude Code, Codex, OpenCode, FactoryAI)
- Synchronisation bidirectionnelle
- Vérifications de sécurité pour répertoires existants
- Sauvegarde avant remplacement

**Outils supportés**:
- Claude Code: Commandes + Agents
- Codex: Commandes uniquement
- OpenCode: Commandes uniquement
- FactoryAI: Commandes + Droids

**Cas d'usage**:
- Synchronisation de configs entre outils
- Maintien d'une source unique de vérité
- Compatibilité inter-outils

---

### 5. Workflow des commandes Pro
**Fichier**: [`05-pro-command-workflow.md`](./05-pro-command-workflow.md)

**Description**: Fonctionnalités premium avec authentification par token.

**Fonctionnalités clés**:
- Validation de token via API Codeline
- Extraction de token GitHub
- Accès dépôt privé
- Installation de config premium

**Sous-commandes**:
- `activate [token]`: Activer le premium
- `status`: Vérifier le statut d'activation
- `setup`: Installer configs premium
- `update`: Rafraîchir configs premium

**Fonctionnalités Premium**:
- Bibliothèque de commandes étendue
- Agents avancés
- Statusline améliorée
- Support prioritaire

**Cas d'usage**:
- Activation d'abonnement premium
- Installation de fonctionnalités premium
- Mise à jour de configs premium

---

### 6. Workflow de Statusline
**Fichier**: [`06-statusline-workflow.md`](./06-statusline-workflow.md)

**Description**: Affichage de métriques et informations de session en temps réel.

**Fonctionnalités clés**:
- Récupération de données en parallèle (Git, Contexte, API)
- Calcul de coût
- Suivi d'utilisation de tokens
- Surveillance de limite de taux

**Sources de données**:
- Statut Git (branche, changements)
- Parsing de transcript (tokens, coût)
- API OAuth Claude (limites de taux)

**Format d'affichage**:
- Ligne 1: Branche, chemin, modèle
- Ligne 2: Coût, durée, tokens, usage %

**Cas d'usage**:
- Surveillance des coûts de session
- Suivi d'utilisation de tokens
- Vérification des limites de taux
- Conscience de branche Git

---

### 7. Flux du Hook de sécurité
**Fichier**: [`07-security-hook-flow.md`](./07-security-hook-flow.md)

**Description**: Couche de sécurité complète de validation de commandes bash.

**Fonctionnalités clés**:
- Système de sécurité de 700+ lignes
- 50+ règles de validation
- Parsing de chaînes de commandes
- Découpage conscient des guillemets
- Journalisation de sécurité

**Catégories de sécurité**:
1. Commandes critiques (dd, mkfs, fdisk)
2. Élévation de privilèges (sudo, su)
3. Commandes réseau (ssh, curl, wget)
4. Motifs dangereux (pipe vers shell, injection de commande)
5. Validation de rm -rf
6. Protection d'écriture de fichiers
7. Commandes sûres en liste blanche

**Cas d'usage**:
- Prévention de commandes destructives
- Blocage d'élévation de privilèges
- Validation d'opérations de fichiers
- Journalisation d'événements de sécurité

---

### 8. Architecture du CLI
**Fichier**: [`08-cli-architecture.md`](./08-cli-architecture.md)

**Description**: Architecture système globale et relations entre composants.

**Composants clés**:
- Point d'entrée (parser CLI)
- Couche Commande (setup, add, pro, etc.)
- Couche Utilitaire (GitHub, installateur de fichiers, config)
- Templates de configuration
- Cibles d'installation

**Couches d'architecture**:
1. Point d'entrée: Routage Commander.js
2. Commandes: Gestionnaires de logique métier
3. Utilitaires: Fonctionnalités partagées
4. Externe: GitHub, APIs
5. Templates: Fichiers de configuration
6. Cibles: Destinations d'installation

**Cas d'usage**:
- Compréhension de la structure système
- Planification de nouvelles fonctionnalités
- Débogage de problèmes
- Intégration de nouveaux développeurs

---

### 9. Flux d'installation
**Fichier**: [`09-installation-flow.md`](./09-installation-flow.md)

**Description**: Processus d'installation complet de bout en bout.

**Phases clés**:
1. Installation de package (npm/yarn/pnpm/bun)
2. Sélection de source de configuration (GitHub/Local)
3. Sélection de fonctionnalités (interactif/skip)
4. Installation parallèle (toutes fonctionnalités simultanément)
5. Configuration de paramètres (stratégie de fusion)
6. Vérification & rapport

**Cibles d'installation**:
- Commandes: `~/.claude/commands/`
- Agents: `~/.claude/agents/`
- Scripts: `~/.claude/scripts/`
- Paramètres: `~/.claude/settings.json`

**Support de plateforme**:
- macOS: Support complet
- Linux: Support partiel
- Windows: Support limité

**Cas d'usage**:
- Configuration initiale
- Compréhension du processus d'installation
- Dépannage de problèmes d'installation

---

### 10. Workflow de test
**Fichier**: [`10-testing-workflow.md`](./10-testing-workflow.md)

**Description**: Stratégie de test et pratiques de développement critiques.

**Règles critiques**:
1. **TOUJOURS** exécuter `bun test:run` après changements
2. **JAMAIS** ignorer les tests avant commit
3. **UTILISER** les tests pour valider au lieu de tests manuels

**Types de tests**:
- Tests d'intégration (exécution CLI réelle)
- Validation de settings
- Vérification d'installation de fichiers

**Flux de test**:
1. Configuration d'environnement temporaire
2. Exécution CLI réelle
3. Validation asynchrone
4. Assertions complètes
5. Nettoyage

**Cas d'usage**:
- Validation de changements de code
- Prévention de régressions
- Assurance qualité
- Intégration CI/CD

---

## Comment utiliser ces diagrammes

### Pour les développeurs

**Comprendre le système**:
1. Commencer avec [Architecture du CLI](#8-architecture-du-cli) pour vue d'ensemble
2. Approfondir les workflows spécifiques selon besoin
3. Référencer sécurité et test pour meilleures pratiques

**Implémenter de nouvelles fonctionnalités**:
1. Examiner [Architecture du CLI](#8-architecture-du-cli)
2. Étudier workflow similaire (ex: [Ajout de commande](#3-workflow-dajout-de-commande))
3. Suivre [Workflow de test](#10-workflow-de-test)
4. Suivre les motifs d'implémentations existantes

**Déboguer les problèmes**:
1. Identifier le diagramme de workflow affecté
2. Suivre le flux pour localiser le problème
3. Vérifier les fichiers associés listés dans le diagramme
4. Valider avec les tests

### Pour les utilisateurs

**Démarrage**:
1. Lire [Flux d'installation](#9-flux-dinstallation)
2. Comprendre [Workflow Setup](#1-workflow-de-la-commande-setup)
3. Examiner les commandes disponibles dans [Ajout de commande](#3-workflow-dajout-de-commande)

**Usage avancé**:
1. [Workflow Symlink](#4-workflow-de-symlink) pour sync inter-outils
2. [Workflow Pro](#5-workflow-des-commandes-pro) pour fonctionnalités premium
3. [Workflow Statusline](#6-workflow-de-statusline) pour métriques

**Compréhension de la sécurité**:
1. Examiner [Flux Hook de sécurité](#7-flux-du-hook-de-sécurité)
2. Comprendre quelles commandes sont bloquées/autorisées
3. Vérifier les logs de sécurité si nécessaire

---

## Visualisation des diagrammes Mermaid

### Sur GitHub
Les diagrammes se rendent automatiquement lors de la visualisation des fichiers `.md` sur GitHub.

### Dans VS Code
1. Installer l'extension [Mermaid Preview](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-mermaid)
2. Ouvrir le fichier de diagramme
3. Cliquer sur le bouton de prévisualisation

### Éditeur en ligne
1. Visiter [mermaid.live](https://mermaid.live)
2. Copier le code du diagramme
3. Voir/éditer dans le navigateur

### Exporter en image
1. Utiliser [mermaid.live](https://mermaid.live)
2. Coller le code du diagramme
3. Cliquer "Export" → PNG/SVG

---

## Conventions des diagrammes

### Code couleur

| Couleur | Signification | Exemple |
|-------|---------|---------|
| 🔵 Bleu clair | Début/Point d'entrée | Entrée commande utilisateur |
| 🟢 Vert | Succès/Complétion | Opération terminée |
| 🔴 Rouge | Erreur/Échec | Validation échouée |
| 🟡 Jaune | Avertissement/Important | Décision critique |
| 🔷 Bleu | Information/Processus | Traitement de données |

### Formes de nœud

| Forme | Signification |
|-------|---------|
| Rectangle arrondi | Processus/Action |
| Losange | Point de décision |
| Cercle | Début/Fin |
| Rectangle | Données/Entité |
| Hexagone | Service externe |

### Types de flèche

| Flèche | Signification |
|-------|---------|
| `-->` | Flux standard |
| `-.->` | Flux optionnel/alternatif |
| `==>` | Flux important/prioritaire |

---

## Maintenance de ces diagrammes

### Quand mettre à jour

Mettre à jour les diagrammes quand:
- Ajout de nouvelles commandes ou fonctionnalités
- Changement de logique de workflow
- Modification d'architecture
- Ajout/suppression de dépendances
- Changement de règles de sécurité

### Comment mettre à jour

1. Éditer le fichier `.md`
2. Modifier le code Mermaid entre ` ```mermaid ` et ` ``` `
3. Prévisualiser les changements
4. Exécuter les tests: `bun test:run`
5. Commit avec message descriptif

### Guide de style des diagrammes

- Garder les diagrammes focalisés (un workflow par fichier)
- Utiliser un nommage cohérent
- Ajouter des commentaires pour logique complexe
- Inclure section fichiers associés
- Mettre à jour la table des matières dans README

---

## Documentation associée

- README principal: [`../README.md`](../README.md)
- Instructions Claude: [`../CLAUDE.md`](../CLAUDE.md)
- Config Package: [`../package.json`](../package.json)
- Config TypeScript: [`../tsconfig.json`](../tsconfig.json)

---

## Contribution

Lors de l'ajout de nouveaux diagrammes:

1. Suivre la convention de numérotation: `XX-nom.md`
2. Inclure description et fonctionnalités clés
3. Ajouter à la table des matières dans ce README
4. Utiliser syntaxe Mermaid cohérente
5. Ajouter code couleur pour clarté
6. Inclure section "Fichiers associés"
7. Tester le rendu sur GitHub

---

## Questions ou problèmes?

- Vérifier les diagrammes existants pour motifs similaires
- Examiner les fichiers source associés
- Consulter CLAUDE.md pour directives de développement
- Ouvrir une issue sur GitHub pour améliorations de diagrammes

---

**Dernière mise à jour**: 2025-11-18

**Maintenu par**: Équipe CLI AIBlueprint

**Licence**: Identique au projet principal
