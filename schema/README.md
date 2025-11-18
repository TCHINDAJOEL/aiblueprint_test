# AIBlueprint CLI - Architecture & Workflow Diagrams

This folder contains comprehensive Mermaid diagrams documenting all workflows and architectural patterns in the AIBlueprint CLI project.

## 📋 Table of Contents

1. [Setup Command Workflow](#1-setup-command-workflow)
2. [Add Hook Workflow](#2-add-hook-workflow)
3. [Add Command Workflow](#3-add-command-workflow)
4. [Symlink Workflow](#4-symlink-workflow)
5. [Pro Command Workflow](#5-pro-command-workflow)
6. [Statusline Workflow](#6-statusline-workflow)
7. [Security Hook Flow](#7-security-hook-flow)
8. [CLI Architecture](#8-cli-architecture)
9. [Installation Flow](#9-installation-flow)
10. [Testing Workflow](#10-testing-workflow)

---

## Overview

These diagrams provide visual documentation of:

- **User-Facing Workflows**: How users interact with the CLI
- **Internal Processes**: How the system processes commands
- **Security Mechanisms**: How security validation works
- **Data Flows**: How data moves through the system
- **Testing Strategies**: How the system is validated

All diagrams are written in Mermaid format and can be viewed:
- On GitHub (automatic rendering)
- In VS Code (with Mermaid extension)
- Online at [mermaid.live](https://mermaid.live)

---

## Diagrams

### 1. Setup Command Workflow
**File**: [`01-setup-workflow.md`](./01-setup-workflow.md)

**Description**: The main setup command that installs all AIBlueprint configurations.

**Key Features**:
- Interactive feature selection
- GitHub-first with local fallback
- Conditional feature installation
- Settings.json merge logic
- Dependency auto-installation

**Use Cases**:
- First-time installation
- Feature updates
- Configuration refresh

---

### 2. Add Hook Workflow
**File**: [`02-add-hook-workflow.md`](./02-add-hook-workflow.md)

**Description**: Installing individual Claude Code hooks for enhanced functionality.

**Key Features**:
- Hook type validation
- Project vs global detection
- Environment variable usage (`$CLAUDE_PROJECT_DIR`)
- Executable permissions management

**Supported Hooks**:
- `post-edit-typescript`: TypeScript validation after editing

**Use Cases**:
- Adding project-specific hooks
- Installing validation hooks
- Setting up CI/CD hooks

---

### 3. Add Command Workflow
**File**: [`03-add-command-workflow.md`](./03-add-command-workflow.md)

**Description**: Installing individual command templates or listing available commands.

**Key Features**:
- Command discovery (list mode)
- YAML frontmatter parsing
- Individual command installation
- Metadata extraction (description, allowed-tools)

**Available Commands**: 16 pre-configured templates including:
- `/commit`: Quick commits
- `/create-pull-request`: PR creation
- `/deep-code-analysis`: Code review
- And 13 more...

**Use Cases**:
- Adding specific commands
- Browsing available commands
- Installing custom templates

---

### 4. Symlink Workflow
**File**: [`04-symlink-workflow.md`](./04-symlink-workflow.md)

**Description**: Sharing commands and agents between different AI CLI tools.

**Key Features**:
- Multi-tool support (Claude Code, Codex, OpenCode, FactoryAI)
- Bidirectional syncing
- Safety checks for existing directories
- Backup before replacement

**Supported Tools**:
- Claude Code: Commands + Agents
- Codex: Commands only
- OpenCode: Commands only
- FactoryAI: Commands + Droids

**Use Cases**:
- Syncing configs across tools
- Maintaining single source of truth
- Cross-tool compatibility

---

### 5. Pro Command Workflow
**File**: [`05-pro-command-workflow.md`](./05-pro-command-workflow.md)

**Description**: Premium features with token-based authentication.

**Key Features**:
- Token validation via Codeline API
- GitHub token extraction
- Private repository access
- Premium config installation

**Subcommands**:
- `activate [token]`: Activate premium
- `status`: Check activation status
- `setup`: Install premium configs
- `update`: Refresh premium configs

**Premium Features**:
- Extended command library
- Advanced agents
- Enhanced statusline
- Priority support

**Use Cases**:
- Activating premium subscription
- Installing premium features
- Updating premium configs

---

### 6. Statusline Workflow
**File**: [`06-statusline-workflow.md`](./06-statusline-workflow.md)

**Description**: Real-time session metrics and information display.

**Key Features**:
- Parallel data fetching (Git, Context, API)
- Cost calculation
- Token usage tracking
- Rate limit monitoring

**Data Sources**:
- Git status (branch, changes)
- Transcript parsing (tokens, cost)
- Claude OAuth API (rate limits)

**Display Format**:
- Line 1: Branch, path, model
- Line 2: Cost, duration, tokens, usage %

**Use Cases**:
- Monitoring session costs
- Tracking token usage
- Checking rate limits
- Git branch awareness

---

### 7. Security Hook Flow
**File**: [`07-security-hook-flow.md`](./07-security-hook-flow.md)

**Description**: Comprehensive bash command validation security layer.

**Key Features**:
- 700+ line security system
- 50+ validation rules
- Command chain parsing
- Quote-aware splitting
- Security logging

**Security Categories**:
1. Critical commands (dd, mkfs, fdisk)
2. Privilege escalation (sudo, su)
3. Network commands (ssh, curl, wget)
4. Dangerous patterns (pipe to shell, command injection)
5. rm -rf validation
6. File write protection
7. Whitelisted safe commands

**Use Cases**:
- Preventing destructive commands
- Blocking privilege escalation
- Validating file operations
- Logging security events

---

### 8. CLI Architecture
**File**: [`08-cli-architecture.md`](./08-cli-architecture.md)

**Description**: Overall system architecture and component relationships.

**Key Components**:
- Entry Point (CLI parser)
- Command Layer (setup, add, pro, etc.)
- Utility Layer (GitHub, file installer, config)
- Configuration Templates
- Installation Targets

**Architecture Layers**:
1. Entry Point: Commander.js routing
2. Commands: Business logic handlers
3. Utils: Shared functionality
4. External: GitHub, APIs
5. Templates: Configuration files
6. Targets: Installation destinations

**Use Cases**:
- Understanding system structure
- Planning new features
- Debugging issues
- Onboarding new developers

---

### 9. Installation Flow
**File**: [`09-installation-flow.md`](./09-installation-flow.md)

**Description**: Complete end-to-end installation process.

**Key Phases**:
1. Package installation (npm/yarn/pnpm/bun)
2. Configuration source selection (GitHub/Local)
3. Feature selection (interactive/skip)
4. Parallel installation (all features concurrently)
5. Settings configuration (merge strategy)
6. Verification & reporting

**Installation Targets**:
- Commands: `~/.claude/commands/`
- Agents: `~/.claude/agents/`
- Scripts: `~/.claude/scripts/`
- Settings: `~/.claude/settings.json`

**Platform Support**:
- macOS: Full support
- Linux: Partial support
- Windows: Limited support

**Use Cases**:
- First-time setup
- Understanding installation process
- Troubleshooting installation issues

---

### 10. Testing Workflow
**File**: [`10-testing-workflow.md`](./10-testing-workflow.md)

**Description**: Testing strategy and critical development practices.

**Critical Rules**:
1. **ALWAYS** run `bun test:run` after changes
2. **NEVER** skip tests before commit
3. **USE** tests to validate instead of manual testing

**Test Types**:
- Integration tests (real CLI execution)
- Settings validation
- File installation verification

**Test Flow**:
1. Temporary environment setup
2. Real CLI execution
3. Asynchronous validation
4. Comprehensive assertions
5. Cleanup

**Use Cases**:
- Validating code changes
- Preventing regressions
- Ensuring quality
- CI/CD integration

---

## How to Use These Diagrams

### For Developers

**Understanding the System**:
1. Start with [CLI Architecture](#8-cli-architecture) for overview
2. Deep dive into specific workflows as needed
3. Reference security and testing for best practices

**Implementing New Features**:
1. Review [CLI Architecture](#8-cli-architecture)
2. Study similar workflow (e.g., [Add Command](#3-add-command-workflow))
3. Follow [Testing Workflow](#10-testing-workflow)
4. Follow patterns from existing implementations

**Debugging Issues**:
1. Identify affected workflow diagram
2. Follow flow to locate issue
3. Check related files listed in diagram
4. Validate with tests

### For Users

**Getting Started**:
1. Read [Installation Flow](#9-installation-flow)
2. Understand [Setup Workflow](#1-setup-command-workflow)
3. Review available commands in [Add Command](#3-add-command-workflow)

**Advanced Usage**:
1. [Symlink Workflow](#4-symlink-workflow) for cross-tool syncing
2. [Pro Workflow](#5-pro-command-workflow) for premium features
3. [Statusline Workflow](#6-statusline-workflow) for metrics

**Security Understanding**:
1. Review [Security Hook Flow](#7-security-hook-flow)
2. Understand what commands are blocked/allowed
3. Check security logs when needed

---

## Viewing Mermaid Diagrams

### On GitHub
Diagrams render automatically when viewing `.md` files on GitHub.

### In VS Code
1. Install [Mermaid Preview](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-mermaid) extension
2. Open diagram file
3. Click preview button

### Online Editor
1. Visit [mermaid.live](https://mermaid.live)
2. Copy diagram code
3. View/edit in browser

### Export as Image
1. Use [mermaid.live](https://mermaid.live)
2. Paste diagram code
3. Click "Export" → PNG/SVG

---

## Diagram Conventions

### Color Coding

| Color | Meaning | Example |
|-------|---------|---------|
| 🔵 Light Blue | Start/Entry Point | User command input |
| 🟢 Green | Success/Completion | Operation completed |
| 🔴 Red | Error/Failure | Validation failed |
| 🟡 Yellow | Warning/Important | Critical decision |
| 🔷 Blue | Information/Process | Data processing |

### Node Shapes

| Shape | Meaning |
|-------|---------|
| Rounded Rectangle | Process/Action |
| Diamond | Decision Point |
| Circle | Start/End |
| Rectangle | Data/Entity |
| Hexagon | External Service |

### Arrow Types

| Arrow | Meaning |
|-------|---------|
| `-->` | Standard flow |
| `-.->` | Optional/Alternative flow |
| `==>` | Important/Priority flow |

---

## Maintaining These Diagrams

### When to Update

Update diagrams when:
- Adding new commands or features
- Changing workflow logic
- Modifying architecture
- Adding/removing dependencies
- Changing security rules

### How to Update

1. Edit the `.md` file
2. Modify Mermaid code between ` ```mermaid ` and ` ``` `
3. Preview changes
4. Run tests: `bun test:run`
5. Commit with descriptive message

### Diagram Style Guide

- Keep diagrams focused (one workflow per file)
- Use consistent naming
- Add comments for complex logic
- Include related files section
- Update table of contents in README

---

## Related Documentation

- Main README: [`../README.md`](../README.md)
- Claude Instructions: [`../CLAUDE.md`](../CLAUDE.md)
- Package Config: [`../package.json`](../package.json)
- TypeScript Config: [`../tsconfig.json`](../tsconfig.json)

---

## Contributing

When adding new diagrams:

1. Follow numbering convention: `XX-name.md`
2. Include description and key features
3. Add to table of contents in this README
4. Use consistent Mermaid syntax
5. Add color coding for clarity
6. Include "Related Files" section
7. Test rendering on GitHub

---

## Questions or Issues?

- Check existing diagrams for similar patterns
- Review related source files
- Consult CLAUDE.md for development guidelines
- Open issue on GitHub for diagram improvements

---

**Last Updated**: 2025-11-18

**Maintained By**: AIBlueprint CLI Team

**License**: Same as main project
