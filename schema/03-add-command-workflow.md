# Add Command Workflow

This diagram illustrates the add command workflow for installing individual Claude Code commands.

```mermaid
flowchart TD
    Start([User runs: aiblueprint claude-code add commands]) --> HasArg{Command Name<br/>Specified?}

    HasArg -->|No| ListMode[List Mode:<br/>Show all available commands]
    HasArg -->|Yes| InstallMode[Install Mode:<br/>Install specific command]

    ListMode --> FetchList{Fetch Command List}

    FetchList --> GHList{GitHub<br/>Available?}

    GHList -->|Yes| ListGH[List from GitHub API<br/>github.com/api/contents/commands]
    GHList -->|No| ListLocal[List from Local<br/>claude-code-config/commands/]

    ListGH --> ParseMetadata
    ListLocal --> ParseMetadata

    ParseMetadata[Parse YAML Frontmatter<br/>Extract: description, allowed-tools, argument-hint] --> DisplayList[Display Formatted List:<br/>- Command name<br/>- Description<br/>- Usage syntax<br/>- Allowed tools]

    DisplayList --> EndList([User can now run install command])

    InstallMode --> ValidateCmd{Valid Command<br/>Name?}

    ValidateCmd -->|No| ErrorCmd([❌ Error: Command not found])
    ValidateCmd -->|Yes| CheckExist{Command File<br/>Already Exists?}

    CheckExist -->|Yes| PromptOverwrite{Prompt User:<br/>Overwrite existing?}
    CheckExist -->|No| Download

    PromptOverwrite -->|No| Cancel([❌ Operation Cancelled])
    PromptOverwrite -->|Yes| Download

    Download[Download Command] --> GHCheck{GitHub<br/>Available?}

    GHCheck -->|Yes| DownloadGH[Download from GitHub<br/>raw.githubusercontent.com]
    GHCheck -->|No| UseLocal[Copy from Local<br/>claude-code-config/commands/]

    DownloadGH --> WriteCmd[Write to ~/.claude/commands/]
    UseLocal --> WriteCmd

    WriteCmd --> Success([✅ Command Installed<br/>Ready to use: /command-name])

    style Start fill:#e1f5ff
    style Success fill:#d4edda
    style EndList fill:#d4edda
    style ErrorCmd fill:#f8d7da
    style Cancel fill:#f8d7da
    style ParseMetadata fill:#d1ecf1
```

## Available Commands (16 templates)

### Development Workflow
- `/commit` - Quick conventional commits with auto-push
- `/create-pull-request` - Create PR with summary and test plan
- `/fix-pr-comments` - Address PR review comments
- `/run-tasks` - Execute project tasks efficiently

### Code Analysis
- `/deep-code-analysis` - Comprehensive code review
- `/explain-architecture` - Document system architecture

### Project Management
- `/claude-memory` - Manage Claude's project memory
- `/cleanup-context` - Clean up conversation context

### Utilities
- `/epct` - Execute and parallelize complex tasks
- `/prompt-command` - Generate new command templates
- `/prompt-agent` - Generate new agent templates
- `/watch-ci` - Monitor CI/CD pipeline

### And more...

## Command Structure

Each command is a Markdown file with:

```markdown
---
description: "Command description"
allowed-tools: "Bash, Read, Edit"
argument-hint: "<required-arg> [optional-arg]"
---

# Command Instructions

[Detailed instructions for Claude...]
```

## Related Files

- Source: `src/commands/addCommand.ts`
- Commands Directory: `claude-code-config/commands/`
- Utilities: `src/utils/claude-config.ts` (YAML parsing)
