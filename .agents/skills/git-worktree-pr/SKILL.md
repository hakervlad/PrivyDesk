---
name: git-worktree-pr
description: Organize issue branches, worktrees, reviewed commits, safe pushes and PR merges only when Main Manager explicitly assigns the operation.
---

# Git, Worktrees and Pull Requests

Use gh first for GitHub, Git for local operations. Read AGENTS.md and
`docs/development-workflow-v2.md`. Never edit application code or decide architecture.
This skill grants no OS, sandbox, network, or GitHub permissions.

## Branch and worktree
Check issue existence, linked branches, `git status --short`, `git branch -avv`,
`git worktree list --porcelain`, and the remote/base before changes.
One issue, one branch, one worktree: feat/<number>-<slug> or fix/<number>-<slug>.
Reuse existing work; never duplicate it or develop on main. Never discard uncommitted
work. gh issue develop here creates remote linked branches but not worktrees;
prefer local creation/T3 to preserve the review-before-first-push gate.
A T3 child in the issue checkout uses shared isolation. Do not create a worktree
in a delegate prompt/shell for a child using isolation=worktree: T3 owns that setup.
For a user-requested new top-level thread bound to an existing worktree use
workspaceStrategy={type:"existing_worktree",worktreePath:<absolute path>}.
Check actual cwd, repository, branch and head before writing; never infer binding from title.

## Commit and push
Require Main Manager's reviewed diff, allowed file list and passing test evidence.
Review unstaged, staged and untracked paths. Stage explicit files; inspect staged
diff and secrets; use `feat: ... (#N)`, `fix: ... (#N)` or an appropriate conventional
type. Do not amend unrelated commits or use --no-verify. After committing, Main
Manager verifies commit/tree matches the approved diff and records full SHA.
Push only that approved feature branch to the inspected origin. Never push main,
force-push, or mix issues. Unexpected remote divergence: stop and report.
For a created or pushed PR call T3 link_pull_request as the next tool call.
Use exact body text via --body-file for gh; do not interpolate shell syntax.

## Pull request
Inspect existing PRs for the branch before creating one. Base main, head the issue
branch; include acceptance criteria, local test evidence, review SHA, dependencies,
privacy findings and issue link. Use Closes #N only for work that will complete that
issue when merged; otherwise Refs #N. GitHub owns the durable progress record.

## Merge gate
Require issue link, Main Manager code review, relevant local tests passing,
actual successful GitHub CI for the final head and applicable base, no unresolved
review blockers, no conflicts, and no unreviewed commits. Missing CI is not green.
Approval must name PR, full head SHA and reviewed base SHA. Re-read these immediately
before merge. Any change invalidates approval. User final confirmation is mandatory
until the user explicitly changes the policy; low-risk auto-approval is inactive.
Use gh pr merge with --match-head-commit <approved-sha> and the approved merge method;
never --admin, protection bypass, skipped checks or unrequested auto-merge/queueing.
Follow host link/watch rules: finish authorized updates before watching a waiting PR;
children return state to Main Manager and never watch. Do not poll in loops.

## Conflicts and cleanup
Stop on conflict; report files to Main Manager. Do not resolve application code or
rebase/force-push independently. Resolutions require new tests/review and approval.
Confirm mergedAt, issue closure and Project Done before cleanup. Ensure no active
agents/threads need the worktree, git status is clean, and no unpublished work exists.
Only then remove the worktree and approved merged branch. Never force-remove a dirty
worktree or delete unmerged work. Do not change another person's PR.

## Controlled execution
The Git Manager stays read-only in intent; custom-agent sandbox_mode may be ignored.
If actual read-only enforcement cannot be confirmed, propose commands and let Main
Manager execute precisely authorized writes through the host. No permission escalation
or tool substitution. A separate deterministic allowlisted executor may be proposed,
not silently installed. Token scope, local write rights and approval are distinct.
