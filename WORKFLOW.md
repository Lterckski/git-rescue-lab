# Workflow Log

## Task 1: Find the bug with git bisect

- **Commit:** `c99fb4209e6fb6e5ed2789893fdb2f893d61d6c6` ("asdf")
- **Explanation:** This commit changed the BULK20 discount condition in `pricing.js` from `items.length >= 5` to `items.length > 5`, so orders with exactly 5 items no longer received the 20% discount.

### How it was found

```
git bisect start
git bisect bad HEAD
git bisect good 2daeb72
git bisect run node test.js
```

Git automatically tested each commit in between by running `node test.js` and using its exit code (0 = pass, 1 = fail), narrowing down to `c99fb42` as the first bad commit.

## Task 6: Reflection questions

**Branching strategy for a team of 4:**
GitHub Flow. It's simple enough for a small team, keeps everyone working off short-lived feature branches merged into main, and doesn't need the overhead of Git Flow's release/develop branches for a project this size. Trunk-based is close too, but GitHub Flow's PR-based review step fits a 4-person team better.

**Fully removing the leaked secret from history:**
`git rm --cached` and `.gitignore` only stop tracking it going forward — the old commit that added `.env` still has the secret in git's history and can be seen with `git show <commit>`. To actually remove it you'd need to rewrite history with `git filter-repo` (or BFG Repo-Cleaner) to strip the file out of every commit, then force-push, and get everyone with a clone to re-clone or hard-reset. The assignment didn't require this because it's destructive and disruptive (it changes every commit hash after the leak, same risk as force-pushing over a teammate's work) — in a real incident, rotating/revoking the leaked credential matters more than scrubbing history, since copies may already exist elsewhere.

**Why rewriting history was OK in Task 2 but not on a teammate's pulled commit:**
In Task 2, the commit only existed on my machine — nobody else had it, so changing its hash affected no one. If a teammate had already pulled that commit, they'd have it as part of their own local history; rewriting and force-pushing it would make their branch diverge from the new remote history, and they'd hit conflicts or silently end up with duplicate/orphaned commits when they next pull. Rewriting history is safe only before it's shared — once someone else has it, treat it as permanent.
