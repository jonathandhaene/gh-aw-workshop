<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Enterprise teams need more than a single well-built workflow — they need controls that ensure every workflow in the organisation follows consistent rules._

## 🎯 What You'll Do

You will learn the organisation-level controls available for agentic workflows: required approvals, org-wide policy settings, GHES-specific admin options, and branch-protection patterns that keep AI-driven automation within safe boundaries. By the end, you will have configured at least one governance control and know where to find the rest.

## 📋 Before You Start

- You have a working agentic workflow from [Your First Workflow](07-your-first-workflow.md).
- You are an organisation owner or repository admin, or you are working alongside one.
- (GHES only) Confirm your instance is on GHES 3.12 or later — see [Enterprise Setup Considerations](side-quest-enterprise-setup.md).

## Steps

### Understand the four governance layers

Governance for agentic workflows works at four levels. Each layer adds an independent check.

| Layer | Where it lives | What it controls |
|---|---|---|
| **Workflow permissions** | Workflow frontmatter `permissions:` | What the agent can read or write in that specific run |
| **Repository settings** | Repository → **Settings** → **Actions** | Which workflows can run and under what conditions |
| **Organisation policy** | Org → **Settings** → **Actions** | Allowed actions, required approval for external actions |
| **Required approvals (Environments)** | Repository → **Settings** → **Environments** | Human sign-off before a workflow deploys or writes |

Start at the layer closest to the workflow (frontmatter), then work outward.

### Audit frontmatter permissions across your workflows

Every agentic workflow declares its permissions in the frontmatter `permissions:` block. Least-privilege means each workflow only gets what it needs.

Open each `.md` workflow file in your repository and check:

- Does `permissions.contents` use `write` only when the workflow actually commits or creates a PR?
- Does `permissions.issues` use `write` only when the workflow posts issues or comments?
- Are any fields set to `write` that could be `read` or omitted entirely?

Change any over-scoped field to the minimal value that makes the workflow work correctly. Then recompile:

```bash
gh aw compile
```

> [!TIP]
> The `gh aw audit` command (covered in [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md)) shows permissions actually used versus permissions declared. Use it to find over-scoped fields you might have missed.

### Apply an Environment with required reviewers

GitHub Environments let you gate a workflow run on human approval. This is especially useful for agentic workflows that write to production repositories, post public comments, or trigger downstream deployments.

In the GitHub UI:

1. Navigate to your repository → **Settings** → **Environments**.
2. Click **New environment** and name it (for example, `agentic-production`).
3. Under **Deployment protection rules**, enable **Required reviewers** and add at least one person or team.
4. Click **Save protection rules**.

Now reference this environment in your workflow frontmatter:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * 1-5"
jobs:
  report:
    runs-on: ubuntu-latest
    environment: agentic-production
    permissions:
      issues: write
---
```

With this in place, each scheduled run pauses for a reviewer to approve before the agent executes.

<details>
<summary>🖥️ When to skip required reviewers</summary>

Required reviewers are a good fit for workflows that make permanent or visible changes — posting issues, creating PRs, or updating project boards. They are overkill for read-only workflows (for example, a workflow that only summarises activity and posts to Slack). Use them deliberately where human oversight adds real value.

</details>

### Set an organisation-level Actions policy (GHEC / GHES)

Organisation owners can restrict which actions and reusable workflows are allowed. For agentic workflows, the most important setting is controlling which `gh aw`-compiled workflows can call external tools.

1. Navigate to your organisation → **Settings** → **Actions** → **General**.
2. Under **Policies**, choose the option that matches your risk posture:
   - **Allow all actions** — maximum flexibility, minimal restriction.
   - **Allow actions created by GitHub** — blocks third-party actions but allows GitHub-authored ones.
   - **Allow select actions** — specify an explicit allowlist.
3. Click **Save**.

> [!NOTE]
> On GitHub Enterprise Server, this setting is also available at the enterprise level. Enterprise-level policies override organisation-level settings. Confirm with your admin which level applies in your deployment.

### Add a branch protection rule for workflow files

Prevent accidental or unauthorised changes to compiled `.lock.yml` files by requiring pull request reviews on the `main` branch before merging.

1. Navigate to your repository → **Settings** → **Branches**.
2. Click **Add rule** (or edit an existing rule for `main`).
3. Enable **Require a pull request before merging**.
4. Optionally add **Require review from Code Owners** and create a `CODEOWNERS` file that maps `.github/workflows/` to a security team.

Example `CODEOWNERS` entry:

```text
.github/workflows/ @my-org/workflow-approvers
```

With this in place, any change to an agentic workflow definition — including the compiled `.lock.yml` — requires sign-off from the designated team.

## ✅ Checkpoint

- [ ] You can name all four governance layers and describe what each one controls
- [ ] You audited at least one workflow's `permissions:` block and removed any over-scoped fields
- [ ] You created a GitHub Environment with at least one required reviewer (or confirmed your team's policy on when to use environments)
- [ ] You reviewed your organisation's Actions policy setting and know which tier applies to your deployment
- [ ] You added or confirmed a branch protection rule that requires pull request reviews before merging changes to `.github/workflows/`
- [ ] You can explain to a teammate why least-privilege permissions matter even for agentic workflows that "just read"

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
