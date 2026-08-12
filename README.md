# SKILL-OF/worktree-stewardship

How to identify, create, and operate within the two distinct types of working
copy that appear in the BIAFRAL filesystem grammar.

## The two types

### Main working copy (branch-movable)

```
_/AS/<repo>/           ← .git/ is a DIRECTORY
```

- Created by `git clone` or `git init`
- HEAD is a mutable ref: `git switch <other>` works freely
- An agent born here can change branch without administrative action

### Linked worktree (branch-locked)

```
_/AS/worktree/_/AS/<name>/    ← .git is a FILE pointing to ../<repo>/.git/worktrees/<name>/
```

- Created by `git worktree add -b <branch> <path> <start-point>`
- HEAD is locked: git refuses to check out the same branch elsewhere
- An agent born here must not attempt branch operations — the lock is enforced
  at the filesystem level by git

## Detecting type from path without touching git

```js
import fs from 'node:fs';
const gitEntry = fs.statSync('.git');
const type = gitEntry.isDirectory() ? 'main-copy' : 'linked-worktree';
```

An agent can also read its own working directory path: if `worktree` appears as
an AS segment above the gitroot, the agent is in a linked worktree.

## Creating a linked worktree

```bash
# in the main copy, on branch main:
git worktree add -b feature/thing ../repo-feature main
#                                  ^^^^^^^^^^^^^ <name>: also becomes .git/worktrees/<name>
#                                                         ^^^^^ start-point branch
```

The `<name>` in `.git/worktrees/<name>/gitdir` is always the last path segment
of the destination — no independent name exists.

## AS path placement

Linked worktrees are siblings of the main copy, not children:

```
_/AS/<org>/
  _/AS/<repo>/                         ← main copy (branch-movable)
  _/AS/worktree/_/AS/<repo>.<name>/    ← linked worktree (branch-locked), sibling
```

This reflects the git data model: the worktree lives beside the repo on disk,
shares the `.git/` object store, and its identity is its directory name.

## Boundary with agent-branch-ownership

This skill covers the filesystem mechanics of working copy types. The semantic
grammar for encoding branch lineage (start-point, upstream, current,
downstream) and agent role assignment (worker vs integrator) belongs in
`SKILL-OF/agent-branch-ownership`.
