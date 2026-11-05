---
name: github-project-management
description: Manage PrivyDesk GitHub Issues, pull request links, labels, and Project status when explicitly assigned.
---

# GitHub Project Management

Use `gh` first. Read current repository conventions and relevant Project automation before editing records.

## Find and avoid duplicates
Search open and closed issues by exact phrases, related labels, and affected area (`gh issue list --search ...`). Read likely matches. Update a matching issue instead of creating a duplicate; explain why a new issue is distinct if overlap is close.

## Create issues
Before the first GitHub write action, obtain the user's confirmation. Use a concise title and body with: problem/context, expected outcome, scope, priority (use existing P0/P1/P2 conventions), known dependencies, branch and PR links when present, and test notes. Write observable acceptance criteria as checkboxes with one verifiable result per item. Keep implementation prescriptions out unless required by a decision.

## Status and labels
Inspect available labels, Project fields, and automations first. Prefer existing automation for simple status movement; otherwise use `gh` and change only the relevant field. Mark blocked work with the repository's existing blocked label/status and record the concrete dependency. Never invent labels or fields when an existing convention can be reused.

## Pull requests and closure
Link the relevant PR to its issue using GitHub's closing keywords in the PR body when appropriate, or the repository's established linkage method. Do not close an issue until the user-confirmed acceptance criteria are verified as complete. Keep completion notes factual, including verification evidence and remaining limitations. Push and merge require the separate git-worktree-pr skill, explicit Main Manager authorization, verified technical permissions, and user confirmation for the final merge. Do not close before the merge is confirmed.

Invoke this skill only for new development tasks, important status changes, blockers, or completed tasks—not after every code edit.

## PrivyDesk lifecycle
Use the existing Project Status field: Backlog, Ready, In progress, In review, Done.
Ready to Merge and Blocked are desired additions, pending user authorization;
do not create a second Status field. Until then retain In review for merge-ready
work and record the exact approval/head SHA in the PR. For blockers retain the
current state and document the dependency in the issue; do not use Done or close it.
Prefer existing Project automations after their behavior has been inspected.
Project selection and automation settings remain unconfirmed; see docs/development-workflow-v2.md.
Only Main Manager may mark implementation reviewed or authorize the next development step.
