---
name: no-staging
description: Keep Git working-tree changes unstaged and avoid intentional Git index/staging changes. Use only when the user explicitly invokes `$no-staging`; never infer invocation from an ordinary Git task, a request to leave changes unstaged, or a mention of the skill.
metadata:
  version: "1.1.0"
---

# No Staging

## Invocation Requirement

Apply this skill only when the user explicitly invokes `$no-staging`.

Do not infer invocation from an ordinary Git task, a request to leave changes
unstaged, a staging-related risk, or a mention of the skill. A natural-language
request to use the named skill does not invoke it unless it includes
`$no-staging`. Discussing, maintaining, installing, or configuring the skill is
not an invocation.

Once the skill is explicitly invoked, apply the rules below for the current
task.

Keep all task changes in the working tree and avoid intentionally changing the
Git index or staging area.

## Required Behavior

When this skill is active:

- Do not stage files, even temporarily.
- Do not run `git add`, `git stage`, `git commit`, `git mv`, `git rm`, or any
  other command that intentionally changes the Git index or staging area.
- Do not unstage, reset, or otherwise rewrite staged changes that existed
  before the task.
- You may create, edit, move, or delete files in the working tree with ordinary
  file-editing operations.
- You may use read-only Git inspection commands such as `git status`,
  `git diff`, `git diff --stat`, `git diff --cached`, and `git ls-files`.
- If a command may change the index and its behavior is unclear, do not run it.
- If staging is required to complete the task, stop and ask the user for
  explicit permission before running an index-changing command. Do not infer
  permission.
- At the end, report the files you added, changed, moved, or deleted and
  explicitly state that you did not stage them.

Preserve and distinguish staged changes that predated the task. State that you
did not stage files; do not claim that the index is empty unless that has been
independently verified.

## Scope and Limitations

This skill governs only your actions while it is active.

It does not control the user, an IDE, another process, another agent, or another
session. It does not prevent working-tree changes or provide a broader Git
safety policy.

Some inspection commands may refresh Git index metadata without staging file
content. This skill prohibits intentional changes to the staging state; it does
not guarantee that `.git/index` remains byte-for-byte unchanged.
