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

## Task 6: Reflection Questions

**Branching strategy for a team of 4:**

GitHub Flow is a good choice because it is simple and works well for small teams. Each member can work on a separate feature branch and merge it into the main branch through a pull request. It is easier to manage than Git Flow.

**Fully removing the leaked secret from history:**

`git rm --cached` and `.gitignore` only stop Git from tracking the file in future commits. The secret can still exist in older commits. To completely remove it, you would need tools like `git filter-repo` and then force-push the changes. In a real situation, the leaked password or key should also be changed immediately.

**Why rewriting history was OK in Task 2 but not on a teammate's pulled commit:**

In Task 2, the commit was only on my computer, so changing it did not affect anyone else. If a teammate already pulled the commit, rewriting it could cause conflicts because their local history would be different. It is safest to rewrite history only before the commits are shared with others.

