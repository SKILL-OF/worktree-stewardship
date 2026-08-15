# Worktree Safety Patterns

**Contribution:** Hazrat-hawk stewardship applied to worktree management

Safe patterns for managing git worktrees in SOPHIA agent workflows:

## Pattern 1: Bounded Scope

Each worktree serves ONE purpose:
- Feature branch development (temporary)
- Specific repo operation (scoped)
- Never use worktree for unrelated work

## Pattern 2: Atomic Operations

All worktree mutations must be atomic:
- Create worktree with fresh clone (no state carries over)
- Do work in isolated context
- Commit or discard (no partial states)
- Clean up worktree when done

## Pattern 3: State Preservation

Before removing worktree:
- Push all commits to origin
- Verify remote has your work
- Only then remove local worktree
- Leave no unmerged work behind

## Pattern 4: Path Safety

Worktree paths must be:
- Deterministic (same path = same work)
- Scoped (never in home directory)
- Explicit (never guessed from context)

Example safe path:
```
/tmp/work-on-skill-of-worktree-stewardship-feature-branch/
```

Not safe:
```
~/worktree/  # home directory
./temp/      # relative path
/work/       # shared, unscoped
```

## Pattern 5: Restart Safety

After restart, worktree may still exist:
1. Check if path exists
2. If yes: verify branch and remote match intention
3. If diverged: discard and clone fresh
4. If matches: resume work

Never assume worktree state across restarts.

## Pattern 6: Lock Prevention

Before using worktree:
1. Verify no other agent holds lock
2. Check .git/index.lock doesn't exist
3. Ensure git gc is not running

Multi-agent safety requires verification before assuming ownership.

