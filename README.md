# Lite — Spec Kit Extension

Lightweight spec-kit extension for small, well-scoped tasks where the full flow is not needed.

## Usage

```
/speckit-lite-implement Fix the NPE in OrderService.processOrder when customer has no address
```

```
/speckit-lite-implement Add a test for the edge case where the product list is empty
```

```
/speckit-lite-implement Rename UserRepository.fetch to UserRepository.findById
```

## What it does

When you run `/speckit-lite-implement`, the extension performs these steps:

1. Checks complexity — suggests the full flow if the task is too big
2. Reads the constitution and agent context
3. Scans relevant files and testing conventions
4. Verifies ignore files if creating new files
5. Implements the change with tests
6. Runs the build
7. Writes a summary to `specs/lite-summary.md`

## Hooks

The extension supports `before_lite` and `after_lite` hooks for integration with
other spec-kit extensions.

## When to use

| Task type                   | `/speckit-lite-implement` | Full flow |
|-----------------------------|---------------------------|-----------|
| Fix a bug                   | Yes                       |           |
| Add a test                  | Yes                       |           |
| Small refactor              | Yes                       |           |
| Update config               | Yes                       |           |
| Small feature (1-5 files)   | Yes                       |           |
| New feature (multi-concern) |                           | Yes       |
| New service or module       |                           | Yes       |

## Installation

```bash
specify extension add <path-to-speckit-lite> --dev
```
