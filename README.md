# Lite — Spec Kit Extension

Lightweight implementation for simple tasks where the full spec-kit flow is too heavy.

## Commands

| Command | Slash command | Purpose |
|---|---|---|
| `speckit.lite.clarify` | `/speckit-lite-clarify` | Read a Jira issue and clarify it against this repository |
| `speckit.lite.implement` | `/speckit-lite-implement` | Implement a simple task in one pass |

## Usage

### Clarify a Jira issue

```
/speckit-lite-clarify PROJ-123
```

Reads the issue, scans the codebase, and presents:
- **Context** — which files and services are affected
- **Impact** — what changes, what's affected downstream
- **Potential problems** — edge cases, risks, constraints
- **Recommendation** — lite implement, full flow, or ask the reporter for more info

Requires the Jira MCP server to be configured.

### Implement a task

```
/speckit-lite-implement Fix the NPE in OrderService.processOrder when customer has no address
```

1. Checks complexity — suggests the full flow if the task is too big
2. Reads the constitution and agent context
3. Scans relevant files and testing conventions
4. Verifies ignore files if creating new files
5. Implements the change with tests
6. Runs the build
7. Writes a summary to `specs/lite/summary.md`

### Combined flow

```
/speckit-lite-clarify PROJ-123       ← understand the issue
/speckit-lite-implement Fix the ...  ← act on it
```

## When to use

| Task type | Lite | Full flow |
|---|---|---|
| Fix a bug | Yes | |
| Add a test | Yes | |
| Small refactor | Yes | |
| Update config | Yes | |
| Small feature (1-5 files) | Yes | |
| New feature (multi-concern) | | Yes |
| New service or module | | Yes |

## Installation

```bash
specify extension add <path-to-spec-kit-lite> --dev
```

For the clarify command, configure the Jira MCP server in your Claude Code settings.
