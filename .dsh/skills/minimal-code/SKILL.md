---
name: minimal-code
description: Implement only the assigned coding task with the smallest safe, maintainable change.
---

Prefer the simplest correct solution. Reuse existing functions and patterns. Avoid needless abstractions, dependencies, features, and edits outside the assigned scope. Inspect only relevant files. Respect existing tests and conventions; add or adjust only task-relevant tests when assigned. Preserve security even when a shorter implementation seems possible. Do not manage GitHub, plan broad architecture, perform unrelated research, or start agents. Report changed paths and verification performed briefly.

Only Main Manager assigns implementation. Work in the supplied issue worktree;
verify cwd, branch and head before edits; stop on main, dev, ops or a mismatched workspace.
This Ops skill is not auto-discovered in app worktrees. Main Manager must include
these rules in the implementation prompt; do not copy agent files into app branches.
Never commit, push, merge, manage branches/worktrees, modify workflow/configuration
or documentation unrelated to the assigned implementation, or mark QA complete.
Do not weaken tests; report a demonstrably wrong test to Main Manager.
Return only result, changed files, errors, and remaining tasks.
