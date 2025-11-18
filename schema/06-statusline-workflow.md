# Statusline Workflow

This diagram illustrates how the custom statusline displays real-time session information.

```mermaid
flowchart TD
    Start([Claude Code Session Active]) --> HookTrigger[StatusLine Hook Triggered<br/>Every prompt/response]

    HookTrigger --> ReadInput[Read Hook Input from stdin<br/>JSON format]

    ReadInput --> ParseInput[Parse JSON Input:<br/>- Hook type<br/>- Session data<br/>- Transcript path]

    ParseInput --> Parallel{Fetch Data<br/>in Parallel}

    Parallel --> GitStatus[Fetch Git Status]
    Parallel --> ParseTranscript[Parse Transcript File]
    Parallel --> APICall[Call Claude OAuth API]

    %% GIT STATUS BRANCH
    GitStatus --> RunGit[Execute: git status --porcelain -b]

    RunGit --> ParseGit[Parse Git Output:<br/>- Current branch<br/>- Ahead/behind count<br/>- Changed files<br/>- Untracked files]

    ParseGit --> GitData[Git Data Ready:<br/>branch, changes, status]

    %% TRANSCRIPT BRANCH
    ParseTranscript --> ReadFile[Read Transcript JSON]

    ReadFile --> ExtractContext[Extract Context Usage:<br/>- Input tokens<br/>- Output tokens<br/>- Total tokens<br/>- Model used]

    ExtractContext --> CalculateCost[Calculate Session Cost:<br/>Based on model pricing]

    CalculateCost --> ContextData[Context Data Ready:<br/>tokens, cost, duration]

    %% API BRANCH
    APICall --> HTTPRequest[GET api.claude.ai/api/organizations/.../usage]

    HTTPRequest --> ParseAPI[Parse API Response:<br/>- Rate limits<br/>- Current usage<br/>- Percentage used<br/>- Reset time]

    ParseAPI --> UsageData[Usage Data Ready:<br/>limits, percentage]

    %% MERGE AND FORMAT
    GitData --> MergeData[Merge All Data]
    ContextData --> MergeData
    UsageData --> MergeData

    MergeData --> FormatLine1[Format Line 1:<br/>branch | path | model]

    FormatLine1 --> FormatLine2[Format Line 2:<br/>cost | duration | tokens | usage%]

    FormatLine2 --> ColorFormat[Apply Color Formatting:<br/>- Green: safe usage<br/>- Yellow: moderate<br/>- Red: high usage]

    ColorFormat --> Output[Output to stdout:<br/>2 lines with ANSI colors]

    Output --> Display[Claude Code Displays:<br/>Statusline in terminal]

    Display --> NextPrompt{User Sends<br/>Next Prompt?}

    NextPrompt -->|Yes| HookTrigger
    NextPrompt -->|No| End([Session Ends])

    style Start fill:#e1f5ff
    style End fill:#d4edda
    style Parallel fill:#fff3cd
    style MergeData fill:#d1ecf1
    style Display fill:#cfe2ff
```

## Statusline Output Format

### Line 1: Context Information
```
🌿 main | ~/projects/app | sonnet-4.5
```
- Git branch with icon
- Current working directory
- Active model

### Line 2: Metrics
```
💰 $0.45 | ⏱️  2m 34s | 📊 25K/200K (12%) | 🔥 450/500 (90%)
```
- Session cost
- Duration
- Token usage (current/limit)
- Rate limit percentage

## Data Sources

### 1. Git Status
**Command**: `git status --porcelain -b`

**Extracted Info**:
- Current branch name
- Ahead/behind remote count
- Number of changed files
- Untracked files count

### 2. Transcript Parsing
**File**: `.claude/transcript-<session-id>.json`

**Extracted Info**:
- Input tokens per message
- Output tokens per message
- Cumulative total
- Model identifier

### 3. Claude OAuth API
**Endpoint**: `https://api.claude.ai/api/organizations/{org_id}/usage`

**Extracted Info**:
- Daily/hourly rate limits
- Current usage count
- Percentage consumed
- Reset timestamp

## Performance Optimizations

1. **Parallel Execution**: All 3 data sources fetched simultaneously
2. **Caching**: Git status cached for 1 second
3. **Error Handling**: Graceful degradation if API fails
4. **Minimal Processing**: Only parse necessary JSON fields

## Color Scheme

| Usage % | Color | Meaning |
|---------|-------|---------|
| 0-50% | Green | Safe |
| 51-75% | Yellow | Moderate |
| 76-100% | Red | High |

## Related Files

- Script: `claude-code-config/scripts/statusline/src/index.ts`
- Package: `claude-code-config/scripts/statusline/package.json`
- Installer: `src/commands/statusline.ts`
- Dependencies: `ccusage` (cost calculation)

## Installation

**Standalone**:
```bash
aiblueprint claude-code statusline
```

**Via Setup**:
```bash
aiblueprint claude-code setup
# Select "Custom Statusline"
```

## Requirements

- **Bun**: Runtime for statusline script
- **ccusage**: Token cost calculation
- **Git**: Repository detection
- **Claude API**: Rate limit data
