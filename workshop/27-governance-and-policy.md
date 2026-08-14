# Govern Your Agentic Workflows at Scale

> _Good governance turns individual workflows into trustworthy, maintainable organisational assets._

## 🎯 What You'll Do

You'll apply a governance checklist to your `daily-status` workflow: lock down permissions, declare a code-owner review requirement, and document your workflow's scope. By the end, your workflow will be ready to hand off to a team or deploy across an enterprise environment.

## 📋 Before You Start

- Complete [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a `daily-status.md` workflow in your repository with a compiled `.lock.yml`.

## Steps

### Review your permissions block

Open `.github/workflows/daily-status.md` in the GitHub editor (click the pencil ✏️ icon) and find the `permissions:` section. Apply the **principle of least privilege**: each scope should be no broader than what the workflow actually needs.

A well-scoped permissions block looks like this:

```yaml
permissions:
  contents: read
  issues: write
```

Remove any scope that is not used by a step. In particular, watch for `contents: write` — most status-report workflows only need `contents: read`.

> [!TIP]
> If you are unsure which scopes your workflow uses, run `gh aw audit <run-id>` and look at the **Permissions Used** section in the report.

### Add a code-owner rule

Agentic workflows have the authority to write to your repository and post comments. Treat them like application code — require a review before changes merge.

Create (or update) `.github/CODEOWNERS` to require your team to approve edits to any agentic workflow definition:

```
.github/workflows/*.md @your-org/platform-team
.github/workflows/*.lock.yml @your-org/platform-team
```

Replace `@your-org/platform-team` with your actual team or username. Commit this change.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your repository on GitHub.
2. Click **Add file** → **Create new file**.
3. Enter `.github/CODEOWNERS` as the filename.
4. Paste the two lines above, adjusted for your team.
5. Click **Commit new file**.

</details>

> [!NOTE]
> On GitHub Enterprise Server, branch protection rules and CODEOWNERS behave identically to github.com. If your organisation uses required status checks, add a check for the `gh aw compile` step in the same ruleset.

### Document the workflow's scope

Each workflow `.md` file is also its own design document. Add a brief `<!-- governance -->` comment block near the top of `daily-status.md` (just below the closing `---` of the frontmatter) to make the intent explicit for reviewers:

```markdown
<!-- governance
  owner: @your-org/platform-team
  data-classification: internal
  safe-outputs: issues/write
  review-required: yes
-->
```

This comment is ignored by the compiler but is visible in pull request diffs, making reviewer intent clear.

### Run a final compile and push

After all edits, compile and commit:

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml .github/CODEOWNERS
git commit -m "chore: apply governance baseline to daily-status workflow"
git push
```

Verify the workflow still runs clean with a manual trigger from the **Actions** tab.

## ✅ Checkpoint

- [ ] Your workflow's `permissions:` block contains only the scopes the workflow actually uses
- [ ] `.github/CODEOWNERS` requires team approval for edits to `*.md` and `*.lock.yml` files in `.github/workflows/`
- [ ] You added a `<!-- governance -->` comment block documenting owner, data classification, and safe-outputs
- [ ] `gh aw compile` passes with no errors after all edits
- [ ] A manual run completes successfully after the governance changes
- [ ] You can explain the difference between `contents: read` and `contents: write` and why least-privilege matters for agentic workflows

<!-- journey: all -->
Want to explore more? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
