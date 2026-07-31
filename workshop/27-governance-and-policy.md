# Govern Agentic Workflows Across Your Team

> _Shipping an agentic workflow to a team or org means deciding who can change it, who approves its output, and what limits apply — before something goes wrong._

## 🎯 What You'll Do

You'll apply three governance controls to your agentic workflow: required reviewers on the workflow file, an approval gate for sensitive write actions, and a repository ruleset that scopes write access. By the end, your workflow is ready for shared team use.

## 📋 Before You Start

- You've completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow file is committed to the repository.
- You have at least **Maintain** role on the repository (or ask an admin to apply the ruleset in the final step).

## Steps

### Protect the workflow file with a CODEOWNERS rule

A `CODEOWNERS` file assigns required reviewers to specific paths. Any pull request touching those paths must be approved before merge.

In your repository, open (or create) `.github/CODEOWNERS` and add a line like this:

```text
.github/workflows/daily-status.md  @your-team-or-username
```

Replace `@your-team-or-username` with your GitHub team slug (e.g. `@acme/platform`) or your own username for solo testing. Now every change to the workflow file triggers a required review.

<details>
<summary>🖥️ Create or edit CODEOWNERS in the GitHub UI</summary>

1. In your repository, click **Add file → Create new file** (or find `.github/CODEOWNERS` and click the pencil icon ✏️).
2. Type `.github/CODEOWNERS` in the file name box if creating.
3. Add the line shown above.
4. Click **Commit changes**, choose a branch, and open a pull request.

</details>

### Add an approval gate with Environment protection rules

GitHub Environments let you require a human approval before a workflow job runs. This is especially useful for jobs that write to production resources.

1. In your repository, go to **Settings → Environments → New environment**.
2. Name it `agentic-production` (or similar).
3. Under **Protection rules**, enable **Required reviewers** and add yourself or your team.
4. Click **Save protection rules**.

Now update your workflow's frontmatter to reference the environment:

```yaml
---
name: Daily Status
on:
  schedule:
    - cron: "0 9 * * *"
jobs:
  run:
    environment: agentic-production
permissions:
  contents: read
  issues: write
---
```

Any run that reaches the `run` job will pause and wait for a reviewer before executing.

> [!TIP]
> For scheduled workflows that run unattended overnight, consider using environment protection only on a separate `deploy` job that applies the output — not on the analysis job itself. This gives you an audit gate without blocking the daily run.

### Scope write permissions with a repository ruleset

Rulesets can block pushes that match certain file patterns. This prevents an agentic workflow — even one that gained `contents: write` — from modifying files outside its intended scope.

1. Go to **Settings → Rules → Rulesets → New ruleset → New branch ruleset**.
2. Name it `protect-workflow-files`.
3. Set **Enforcement status** to **Active**.
4. Under **Target branches**, select **Default branch**.
5. Under **Rules**, enable **Restrict file paths** and add `.github/workflows/**`.
6. Under **Bypass list**, add the GitHub Actions service (`github-actions[bot]`) only if your workflow legitimately updates its own file — otherwise leave the bypass list empty.
7. Click **Create**.

Any push (from an agent or a human) that modifies `.github/workflows/` must now go through a pull request — giving your team a review opportunity.

> [!NOTE]
> Repository rulesets require GitHub Free or higher. On GHES 3.14+ rulesets are available at the organization level, letting platform teams apply a single policy across many repositories.

## ✅ Checkpoint

- [ ] You added a `CODEOWNERS` entry for your workflow file pointing to a reviewer
- [ ] You created a `agentic-production` environment with at least one required reviewer
- [ ] You added `environment: agentic-production` to your workflow frontmatter (or know how you would)
- [ ] You created a branch ruleset that restricts write access to `.github/workflows/`
- [ ] You can explain the difference between a CODEOWNERS review gate and an Environment protection gate

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->
