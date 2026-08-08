# Side Quest: Shell Output Basics

This page explains the shell utilities used in the commit-count step so you understand what each piece does before moving on.

## The full command

```bash
COUNT=$(git log --oneline --since="24 hours ago" | wc -l | tr -d ' ')
echo "commit_count=$COUNT" >> $GITHUB_OUTPUT
```

## Breaking it down

| Piece | What it does |
|---|---|
| `git log --oneline --since="24 hours ago"` | Lists one line per commit made in the last 24 hours. |
| `wc -l` | Counts the number of lines in the input — one line = one commit. |
| `tr -d ' '` | Strips leading/trailing spaces that some platforms add to `wc -l` output. Without this, the value might be `" 3"` instead of `"3"`, which would fail the `!= '0'` comparison. |
| `$(...)` | Captures the output of the whole pipeline as a variable. |
| `echo "commit_count=$COUNT" >> $GITHUB_OUTPUT` | Writes `commit_count=3` (or whatever the count is) to the special `$GITHUB_OUTPUT` file. GitHub Actions reads this file after the step finishes and exposes each `key=value` pair as a step output. |

## Why $GITHUB_OUTPUT instead of an environment variable?

Environment variables set in one step are not visible to later steps. `$GITHUB_OUTPUT` is a file-based mechanism designed specifically to pass values between steps. Any step with an `id:` can write to it, and any later step or `if:` condition can read those values with `steps.<id>.outputs.<key>`.

## Related references

- [GitHub Actions: Setting step outputs](https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions#setting-an-output-parameter)
- [Side Quest: GitHub Actions Expressions and Contexts](side-quest-15-01-expressions-and-contexts.md)

---

← Back to [Make Your Workflow Smarter with Conditional Logic](15-conditional-logic.md)
