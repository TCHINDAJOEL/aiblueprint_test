# Pro (Premium) Command Workflow

This diagram illustrates the premium features workflow with token-based authentication.

```mermaid
flowchart TD
    Start([User runs: aiblueprint claude-code pro]) --> SubCommand{Which<br/>Subcommand?}

    SubCommand --> Activate[pro activate]
    SubCommand --> Status[pro status]
    SubCommand --> Setup[pro setup]
    SubCommand --> Update[pro update]

    %% ACTIVATE FLOW
    Activate --> HasToken{Token<br/>Provided?}

    HasToken -->|No| PromptToken[Prompt: Enter Premium Token]
    HasToken -->|Yes| ValidateAPI

    PromptToken --> ValidateAPI[Validate Token via API<br/>codeline.app/api/oauth/usage]

    ValidateAPI --> APICheck{API<br/>Response OK?}

    APICheck -->|No| ErrorInvalid([❌ Error: Invalid Token])
    APICheck -->|Yes| ExtractGH[Extract GitHub Token<br/>from Product Metadata]

    ExtractGH --> SaveLocal[Save to Local Config:<br/>~/.aiblueprint/config.json]

    SaveLocal --> SuccessActivate([✅ Premium Activated<br/>Token saved locally])

    %% STATUS FLOW
    Status --> CheckToken{Premium Token<br/>Exists Locally?}

    CheckToken -->|No| NoToken([ℹ️  No Premium Token Found])
    CheckToken -->|Yes| ShowStatus[Display Token Info:<br/>- Platform: codeline.app<br/>- Status: Active]

    ShowStatus --> SuccessStatus([✅ Status Displayed])

    %% SETUP FLOW
    Setup --> VerifyToken{Verify Premium<br/>Token Exists?}

    VerifyToken -->|No| ErrorNoToken([❌ Error: Run 'pro activate' first])
    VerifyToken -->|Yes| InstallFree[Install Free Features:<br/>- Commands<br/>- Agents<br/>- Shell Shortcuts<br/>⚠️  Skip free statusline]

    InstallFree --> InstallPremium[Install Premium Features<br/>from Private GitHub Repo]

    InstallPremium --> AuthGH{Authenticate<br/>with GitHub API}

    AuthGH --> DownloadTree[Download Entire Directory Tree<br/>github.com/api/repos/.../contents]

    DownloadTree --> ProcessFiles{For Each File<br/>in Tree}

    ProcessFiles --> IsDir{Is Directory?}

    IsDir -->|Yes| CreateDir[Create Local Directory]
    IsDir -->|No| DownloadFile[Download & Write File]

    CreateDir --> NextFile{More Files?}
    DownloadFile --> NextFile

    NextFile -->|Yes| ProcessFiles
    NextFile -->|No| MergeConfigs[Merge Premium with Free:<br/>Premium overrides Free]

    MergeConfigs --> UpdateSettingsPro[Update settings.json:<br/>- All hooks<br/>- Premium statusline<br/>- Advanced features]

    UpdateSettingsPro --> SuccessSetup([✅ Premium Setup Complete])

    %% UPDATE FLOW
    Update --> VerifyTokenUpdate{Verify Premium<br/>Token Exists?}

    VerifyTokenUpdate -->|No| ErrorNoTokenUpdate([❌ Error: Run 'pro activate' first])
    VerifyTokenUpdate -->|Yes| RedownloadPremium[Re-download Premium Configs<br/>from Private Repo]

    RedownloadPremium --> OverwriteExisting[Overwrite Existing<br/>Premium Files]

    OverwriteExisting --> SuccessUpdate([✅ Premium Configs Updated])

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

## Premium Features

### What's Included?

**Premium Commands**:
- Advanced workflow automation
- Enhanced productivity commands
- Exclusive templates

**Premium Agents**:
- Specialized AI agents
- Task-specific optimizations

**Premium Statusline**:
- Advanced metrics
- Enhanced visualizations
- Real-time insights

**Premium Hooks**:
- Additional security layers
- Performance optimizations
- Custom event handlers

### Authentication Flow

1. **Token Validation**: API call to `codeline.app/api/oauth/usage`
2. **GitHub Token Extraction**: From product metadata
3. **Local Storage**: Saved in `~/.aiblueprint/config.json`
4. **API Authentication**: Used for private repo access

### API Integration

**Endpoint**: `https://codeline.app/api/products`

**Authentication**: Bearer token (premium token)

**Response Structure**:
```json
{
  "products": [{
    "metadata": {
      "github_token": "ghp_xxx..."
    }
  }]
}
```

### Premium vs Free

| Feature | Free | Premium |
|---------|------|---------|
| Commands | 16 templates | Extended library |
| Agents | 3 basic | Advanced agents |
| Statusline | Basic | Enhanced metrics |
| GitHub Repo | Public | Private |
| Support | Community | Priority |

## Related Files

- Main Command: `src/commands/pro.ts`
- Premium Installer: `src/lib/pro-installer.ts`
- Config Storage: `~/.aiblueprint/config.json`
- API Client: Embedded in pro command

## Security Notes

- Premium tokens are stored locally (not transmitted)
- GitHub tokens are used only for API authentication
- All API calls use HTTPS
- No sensitive data in logs
