# Workflow des commandes Pro (Premium)

Ce diagramme illustre le workflow des fonctionnalités premium avec authentification par token.

```mermaid
flowchart TD
    Start([L'utilisateur exécute: aiblueprint claude-code pro]) --> SubCommand{Quelle<br/>sous-commande?}

    SubCommand --> Activate[pro activate]
    SubCommand --> Status[pro status]
    SubCommand --> Setup[pro setup]
    SubCommand --> Update[pro update]

    %% ACTIVATE FLOW
    Activate --> HasToken{Token<br/>fourni?}

    HasToken -->|Non| PromptToken[Demander: Entrer le token Premium]
    HasToken -->|Oui| ValidateAPI

    PromptToken --> ValidateAPI[Valider le token via l'API<br/>codeline.app/api/oauth/usage]

    ValidateAPI --> APICheck{Réponse de l'API<br/>OK?}

    APICheck -->|Non| ErrorInvalid([❌ Erreur: Token invalide])
    APICheck -->|Oui| ExtractGH[Extraire le token GitHub<br/>des métadonnées du produit]

    ExtractGH --> SaveLocal[Sauvegarder dans la config locale:<br/>~/.aiblueprint/config.json]

    SaveLocal --> SuccessActivate([✅ Premium activé<br/>Token sauvegardé localement])

    %% STATUS FLOW
    Status --> CheckToken{Token Premium<br/>existe localement?}

    CheckToken -->|Non| NoToken([ℹ️  Aucun token Premium trouvé])
    CheckToken -->|Oui| ShowStatus[Afficher les infos du token:<br/>- Plateforme: codeline.app<br/>- Statut: Actif]

    ShowStatus --> SuccessStatus([✅ Statut affiché])

    %% SETUP FLOW
    Setup --> VerifyToken{Vérifier que le token<br/>Premium existe?}

    VerifyToken -->|Non| ErrorNoToken([❌ Erreur: Exécuter 'pro activate' d'abord])
    VerifyToken -->|Oui| InstallFree[Installer les fonctionnalités gratuites:<br/>- Commandes<br/>- Agents<br/>- Raccourcis shell<br/>⚠️  Ignorer la statusline gratuite]

    InstallFree --> InstallPremium[Installer les fonctionnalités Premium<br/>depuis le dépôt GitHub privé]

    InstallPremium --> AuthGH{S'authentifier<br/>avec l'API GitHub}

    AuthGH --> DownloadTree[Télécharger l'arborescence complète<br/>github.com/api/repos/.../contents]

    DownloadTree --> ProcessFiles{Pour chaque fichier<br/>dans l'arborescence}

    ProcessFiles --> IsDir{Est un répertoire?}

    IsDir -->|Oui| CreateDir[Créer le répertoire local]
    IsDir -->|Non| DownloadFile[Télécharger et écrire le fichier]

    CreateDir --> NextFile{Plus de fichiers?}
    DownloadFile --> NextFile

    NextFile -->|Oui| ProcessFiles
    NextFile -->|Non| MergeConfigs[Fusionner Premium avec Gratuit:<br/>Premium écrase Gratuit]

    MergeConfigs --> UpdateSettingsPro[Mettre à jour settings.json:<br/>- Tous les hooks<br/>- Statusline premium<br/>- Fonctionnalités avancées]

    UpdateSettingsPro --> SuccessSetup([✅ Configuration Premium terminée])

    %% UPDATE FLOW
    Update --> VerifyTokenUpdate{Vérifier que le token<br/>Premium existe?}

    VerifyTokenUpdate -->|Non| ErrorNoTokenUpdate([❌ Erreur: Exécuter 'pro activate' d'abord])
    VerifyTokenUpdate -->|Oui| RedownloadPremium[Re-télécharger les configs Premium<br/>depuis le dépôt privé]

    RedownloadPremium --> OverwriteExisting[Écraser les fichiers<br/>Premium existants]

    OverwriteExisting --> SuccessUpdate([✅ Configs Premium mises à jour])

    style Start fill:#e1f5ff
    style SuccessActivate fill:#d4edda
    style SuccessStatus fill:#d4edda
    style SuccessSetup fill:#d4edda
    style SuccessUpdate fill:#d4edda
    style ErrorInvalid fill:#f8d7da
    style ErrorNoToken fill:#f8d7da
    style ErrorNoTokenUpdate fill:#f8d7da
    style NoToken fill:#fff3cd
    style InstallPremium fill:#ffc107
    style MergeConfigs fill:#d1ecf1
```

## Fonctionnalités Premium

### Qu'est-ce qui est inclus?

**Commandes Premium**:
- Automatisation de workflow avancée
- Commandes de productivité améliorées
- Templates exclusifs

**Agents Premium**:
- Agents IA spécialisés
- Optimisations spécifiques aux tâches

**Statusline Premium**:
- Métriques avancées
- Visualisations améliorées
- Insights en temps réel

**Hooks Premium**:
- Couches de sécurité supplémentaires
- Optimisations de performance
- Gestionnaires d'événements personnalisés

### Flux d'authentification

1. **Validation du token**: Appel API vers `codeline.app/api/oauth/usage`
2. **Extraction du token GitHub**: Depuis les métadonnées du produit
3. **Stockage local**: Sauvegardé dans `~/.aiblueprint/config.json`
4. **Authentification API**: Utilisé pour l'accès au dépôt privé

### Intégration API

**Endpoint**: `https://codeline.app/api/products`

**Authentification**: Bearer token (token premium)

**Structure de réponse**:
```json
{
  "products": [{
    "metadata": {
      "github_token": "ghp_xxx..."
    }
  }]
}
```

### Premium vs Gratuit

| Fonctionnalité | Gratuit | Premium |
|---------|------|---------|
| Commandes | 16 templates | Bibliothèque étendue |
| Agents | 3 basiques | Agents avancés |
| Statusline | Basique | Métriques améliorées |
| Dépôt GitHub | Public | Privé |
| Support | Communauté | Prioritaire |

## Fichiers associés

- Commande principale: `src/commands/pro.ts`
- Installateur Premium: `src/lib/pro-installer.ts`
- Stockage de config: `~/.aiblueprint/config.json`
- Client API: Intégré dans la commande pro

## Notes de sécurité

- Les tokens premium sont stockés localement (non transmis)
- Les tokens GitHub sont utilisés uniquement pour l'authentification API
- Tous les appels API utilisent HTTPS
- Aucune donnée sensible dans les logs
