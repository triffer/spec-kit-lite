---
description: Read a Jira issue and clarify it in the context of this repository.
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty). The input should contain a Jira issue ID (e.g., `PROJ-123`).

## Pre-Execution Checks

**Check for extension hooks (before clarify)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_lite_clarify` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Outline.
    ```
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Outline

1. **Read the Jira issue**:
   - Check if a Jira MCP tool is available. If not, **STOP** and report: "No Jira MCP server configured. See the extension README for setup instructions."
   - Fetch the issue by ID using the available Jira MCP tool
   - Extract: summary, description, acceptance criteria, comments, priority, status, linked issues
   - If the issue cannot be found, **STOP** and report the error

2. Load and analyze the repository context:
   - **IF EXISTS**: Read the constitution file and verify the issue does not conflict with project principles. If a conflict is detected, note it in the clarification.
   - **IF EXISTS**: Read `AGENTS.md` or `.specify/agents.md` for agent-level context
   - Identify the area of the codebase the issue relates to based on the issue description
   - Read the relevant files and their immediate dependencies
   - Read relevant tests to understand current behavior

3. **Clarify the issue** by analyzing it against the codebase:

   **Context**: What part of the codebase does this issue affect? Which files, services, or modules are involved? What is the current behavior?

   **Impact**: What would change if this issue is resolved? Which components are affected? Are there downstream consumers or integrations to consider?

   **Potential problems**: What could go wrong? Are there edge cases the issue doesn't mention? Are there dependencies or constraints the reporter might not be aware of? Does the codebase have patterns that make this harder or easier than the issue suggests?

   **Complexity assessment**: Is this a lite task (bug fix, small change, 1-5 files) or does it need the full spec-kit flow?

4. **Write the clarification** to `specs/lite/clarification.md` (overwrite if it exists):

   ```markdown
   # {ISSUE-ID} — {summary}

   **Date**: {DATE} | **Status**: {issue status} | **Priority**: {issue priority}

   ## Context

   {Which part of the codebase is affected, current behavior, relevant files}

   ## Impact

   {What changes, what's affected downstream}

   ## Potential problems

   {Edge cases, risks, constraints the issue doesn't mention}

   ## Recommendation

   {One of:}
   - "Run `/speckit-lite-implement {brief description}` to fix it."
   - "This needs the full spec-kit flow. Run `/speckit.specify` to define requirements."
   - "The issue needs clarification before proceeding. Ask the reporter: {questions}."
   ```

   If the issue is unclear or missing information, add a **Questions for reporter** section listing what's needed.

5. Progress tracking and error handling:
   - If the Jira fetch fails, report the error with context
   - If the codebase scan finds nothing relevant, note that the issue may not relate to this repository
   - Provide clear error messages with context for debugging
   - Suggest next steps if clarification cannot proceed

Note: This command is read-only — it does not modify code. If the issue turns out to need implementation, use `/speckit-lite-implement` or the full spec-kit flow.

6. **Check for extension hooks**: After completion, check if `.specify/extensions.yml` exists in the project root.
   - If it exists, read it and look for entries under the `hooks.after_lite_clarify` key
   - If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
   - Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
   - For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
     - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
     - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
   - For each executable hook, output the following based on its `optional` flag:
     - **Optional hook** (`optional: true`):
       ```
       ## Extension Hooks

       **Optional Hook**: {extension}
       Command: `/{command}`
       Description: {description}

       Prompt: {prompt}
       To execute: `/{command}`
       ```
     - **Mandatory hook** (`optional: false`):
       ```
       ## Extension Hooks

       **Automatic Hook**: {extension}
       Executing: `/{command}`
       EXECUTE_COMMAND: {command}
       ```
   - If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently
