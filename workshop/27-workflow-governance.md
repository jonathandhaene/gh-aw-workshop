<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows in an Enterprise

> _Putting guardrails around who can create, approve, and run agentic workflows turns a powerful tool into a trustworthy one — for your team, your security team, and your auditors._

## 🎯 What You'll Do

Configure organisation-level policies that control which agentic workflows can run, who must review AI-generated outputs before they are applied, and how you can verify compliance across your fleet of repositories.

## 📋 Before You Start

- You have at least one working agentic workflow (from [Your First Agentic Workflow](07-your-first-workflow.md)).
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) — you know what run artifacts look like.
- You are an organisation owner or have been delegated admin access to the repositories you want to govern.

> [!NOTE]
> Most governance controls live in the GitHub organisation settings. If you are using GitHub Enterprise Server (GHES) or GitHub Enterprise Cloud (GHEC), some options may be labelled differently or gated by your enterprise policy tier. Check with your GitHub admin if a setting is not visible.

## Steps

### Understand the governance surfaces

Agentic workflows touch three governance surfaces:

| Surface | What it controls |
|---------|-----------------|
| **Actions permissions** | Which repos can run Actions workflows at all |
| **Workflow permissions** | What the `GITHUB_TOKEN` inside a run is allowed to do |
| **Safe outputs & approvals** | Whether AI-generated writes must pass a human review gate before being applied |

You can apply controls at each surface independently.

### Restrict which repositories can run Actions workflows

1. Go to your organisation on GitHub and open **Settings → Actions → General**.
2. Under **Actions permissions**, choose one of:
   - **All repositories** — any repo in the org can run Actions.
   - **Selected repositories** — only repositories you approve can run Actions (recommended for enterprise teams piloting agentic workflows).
   - **Disabled** — no Actions runs anywhere in the org.
3. Click **Save**.

For agentic workflow pilots, **Selected repositories** lets you approve each repository before it can run AI-powered workflows.

### Lock down the default workflow token permissions

By default, the `GITHUB_TOKEN` used inside an Actions job has read-write access to the repository. Agentic workflows can request additional permissions via their frontmatter — but the baseline token should be as narrow as possible.

1. Still in **Settings → Actions → General**, scroll to **Workflow permissions**.
2. Select **Read repository contents and packages permissions** (read-only by default).
3. Click **Save**.

Each workflow that needs write access must now declare it explicitly in its frontmatter, making privileges visible in code review:

```yaml
---
name: Daily Status Report
permissions:
  issues: write
  contents: read
---
```

This explicit declaration appears in every pull request diff, so reviewers can see exactly what a workflow is permitted to write.

### Add required reviewers for AI-generated outputs

For high-stakes workflows — those that write to issues, open PRs, or modify configuration files — you can require a human to approve the AI output before it is applied using GitHub's **environment protection rules**:

1. In your repository, go to **Settings → Environments**.
2. Click **New environment** and name it `ai-review`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add the team or individuals who should sign off.
4. In your agentic workflow frontmatter, set `environment: ai-review`:

```yaml
---
name: AI Pull Request Summary
on:
  pull_request:
    types: [opened, synchronize]
environment: ai-review
permissions:
  pull-requests: write
---
```

Now every run that reaches a deployment step pauses and waits for a reviewer to approve before the AI's outputs are written.

<details>
<summary>🖥️ Setting up environments via the GitHub UI</summary>

1. Open your repository on GitHub.
2. Click **Settings** → **Environments** → **New environment**.
3. Name it `ai-review` and click **Configure environment**.
4. Under **Deployment protection rules**, click the toggle next to **Required reviewers**.
5. Search for and add the reviewers you want.
6. Click **Save protection rules**.

</details>

### Audit policy compliance across repositories

Once governance controls are in place, you need visibility that they are being respected. The `gh aw audit` command produces a per-run report (covered in [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md)), but for org-wide compliance you can use the GitHub REST API or `gh api` to query Actions permissions across repositories.

List all repositories in your organisation with Actions enabled:

```bash
gh api "orgs/{owner}/actions/permissions/repositories" --paginate \
  --jq '.repositories[].name'
```

Replace `{owner}` with your organisation name. Pipe the output to a file to build a compliance snapshot you can diff over time.

> [!NOTE]
> On GHES, the API base URL differs. Set the `GH_HOST` environment variable to your enterprise hostname before running `gh api` commands:
> `export GH_HOST=github.your-company.com`

### Document your governance policy

Write a short governance policy document and store it in your organisation's `.github` repository at `docs/agentic-workflow-policy.md`. Include:

- Which repository categories are approved to run agentic workflows.
- The minimum required permissions model (read-only token as baseline).
- Whether AI-generated writes require an `ai-review` environment gate.
- Who is responsible for reviewing and approving new agentic workflow PRs.

Keeping the policy in a `.github` repository makes it visible to every contributor and auditable via git history.

## ✅ Checkpoint

- [ ] You located your organisation's **Actions permissions** setting and selected the appropriate access level
- [ ] You set the default workflow token to **read-only** in organisation settings
- [ ] At least one workflow frontmatter explicitly declares its `permissions:` block
- [ ] You created (or reviewed) an `ai-review` environment with required reviewers on at least one repository
- [ ] You ran the `gh api` command above and produced a list of repos with Actions enabled
- [ ] You can explain the difference between Actions permissions, workflow permissions, and environment protection rules
- [ ] You know where to find the GHES-specific API base URL guidance for your organisation

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
