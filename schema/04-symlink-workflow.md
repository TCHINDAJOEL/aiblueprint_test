# Symlink Command Workflow

This diagram illustrates the symlink workflow for sharing commands/agents between different AI CLI tools.

```mermaid
flowchart TD
    Start([User runs: aiblueprint claude-code symlink]) --> SelectSource[Interactive: Select Source Tool]

    SelectSource --> SourceOptions{Source Tool<br/>Selection}

    SourceOptions --> ClaudeCode[Claude Code<br/>~/.claude/]
    SourceOptions --> Codex[Codex<br/>~/.codex/prompts]
    SourceOptions --> OpenCode[OpenCode<br/>~/.config/opencode/command]
    SourceOptions --> FactoryAI[FactoryAI<br/>~/.factory/]

    ClaudeCode --> SelectContent
    Codex --> SelectContent
    OpenCode --> SelectContent
    FactoryAI --> SelectContent

    SelectContent{Select Content Type}

    SelectContent --> Commands[Commands Only]
    SelectContent --> Agents[Agents Only<br/>if supported]
    SelectContent --> Both[Both Commands + Agents]

    Commands --> SelectDest
    Agents --> SelectDest
    Both --> SelectDest

    SelectDest[Multi-Select: Choose Destination Tools] --> DestCheck{For Each<br/>Destination}

    DestCheck --> ValidatePath{Destination<br/>Path Valid?}

    ValidatePath -->|No| Skip[Skip this destination<br/>Log warning]
    ValidatePath -->|Yes| CheckExist{Target Directory<br/>Exists?}

    CheckExist -->|No| CreateSymlink
    CheckExist -->|Yes| IsSymlink{Already a<br/>Symlink?}

    IsSymlink -->|Yes| SkipExist[Skip: Already symlinked]
    IsSymlink -->|No| PromptReplace{Prompt:<br/>Replace with symlink?}

    PromptReplace -->|No| SkipManual[Skip: Keep existing directory]
    PromptReplace -->|Yes| Backup[Backup existing directory<br/>dirname.backup]

    Backup --> CreateSymlink[Create Symlink:<br/>ln -s source target]

    CreateSymlink --> NextDest{More<br/>Destinations?}
    Skip --> NextDest
    SkipExist --> NextDest
    SkipManual --> NextDest

    NextDest -->|Yes| DestCheck
    NextDest -->|No| Report[Generate Summary Report:<br/>- Created: X<br/>- Skipped: Y<br/>- Failed: Z]

    Report --> Success([✅ Symlink Operation Complete])

    style Start fill:#e1f5ff
    style Success fill:#d4edda
    style CreateSymlink fill:#cfe2ff
    style Backup fill:#fff3cd
```

## Supported Tools

### Source & Destination Options

| Tool | Commands Path | Agents Path | Support |
|------|--------------|-------------|---------|
| **Claude Code** | `~/.claude/commands/` | `~/.claude/agents/` | Full |
| **Codex** | `~/.codex/prompts` | N/A | Commands Only |
| **OpenCode** | `~/.config/opencode/command` | N/A | Commands Only |
| **FactoryAI** | `~/.factory/commands/` | `~/.factory/droids/` | Full |

## Symlink Strategy

### Benefits
- **Single Source of Truth**: Update commands in one place
- **Cross-Tool Compatibility**: Use same commands across different AI CLIs
- **Easy Maintenance**: Changes propagate automatically

### Safety Checks
1. Validates all paths before creating symlinks
2. Detects existing non-symlink directories
3. Offers backup before replacement
4. Skips invalid destinations gracefully

### Example Use Case

Share Claude Code commands with Codex:
```bash
aiblueprint claude-code symlink
# Select: Claude Code (source)
# Select: Commands
# Select: Codex (destination)
# Result: ~/.codex/prompts -> ~/.claude/commands
```

## Related Files

- Source: `src/commands/symlink.ts`
- Path Utilities: `src/utils/claude-config.ts`

## Important Notes

- Symlinks are bidirectional (can sync from any tool to any other)
- Agent support depends on tool capabilities
- Custom folder paths can be specified via CLI options
