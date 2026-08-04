<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Agentic workflows that write code, open PRs, and post comments need clear guardrails — this step shows you how to set and enforce them._

## 🎯 What You'll Do

You will apply two layers of governance to your agentic workflows: **repository-level policy settings** (permissions, required reviewers, and branch protection) and **workflow-level safeguards** (safe-output limits, permission scoping, and audit hooks). By the end, any workflow you ship will be auditable, reviewable, and safe to run in a shared or enterprise environment.

## 📋 Before You Start

- Your agentic workflow runs and produces outputs you're happy with (see [Test and Improve Your Workflow](12-test-and-iterate.md)).
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) — governance builds directly on audit visibility.
- If you are on GHES or GHEC, read [Enterprise Setup Considerations](side-quest-enterprise-setup.md) before starting.

## Understand the two-layer model

Governance for agentic workflows works at two levels.

**Repository layer** — controlled through GitHub settings:
- Which branches can be targeted by automated commits.
- Whether pull requests require a human review before merging, even when opened by an agent.
- Which Actions are allowed to run at all (Actions permissions policy).

**Workflow layer** — controlled inside your `.md` workflow file:
- Which GitHub API scopes the workflow is granted (`permissions:`).
- How many write actions the agent can perform (`max-outputs:`).
- Whether the workflow must pause for human approval before a sensitive step.

Good governance uses both layers together.

## Scope permissions in your workflow frontmatter

The single most effective governance change is to declare only the permissions your workflow actually needs.

Open your workflow's `.md` file and locate the `permissions:` block in the frontmatter. Replace any overly broad grants with specific scopes:

```yaml
---
name: PR Reviewer
on:
  pull_request:
    types: [opened, synchronize]
permissions:
  contents: read
  pull-requests: write
  issues: read
---
```

This example grants read access to code, write access to pull request comments, and read access to issues — nothing more. An agent cannot open new issues, push commits, or access secrets it was not given.

> [!TIP]
> The reference for every available permission scope is at [github.github.com/gh-aw/reference/permissions/](https://github.github.com/gh-aw/reference/permissions/). Start with all scopes set to `read`, then add `write` only for the outputs your brief explicitly produces.

<details>
<summary>🏢 Enterprise note: org-level Actions permissions policy</summary>

On GHES and GHEC, an organisation admin can restrict which Actions are allowed to run. If your agentic workflow is blocked by an Actions policy, ask your admin to allowlist the `gh-aw` action or configure the policy to allow Actions created by GitHub.

To check the current policy in your org:
1. Go to your organisation → **Settings** → **Actions** → **General**.
2. Under **Actions permissions**, confirm that Actions in your repository are allowed.

</details>

## Protect branches that agents can target

If your workflow can push commits or open pull requests targeting `main`, add a branch protection rule so those changes always require a human review before merging.

1. Go to your repository on GitHub and click **Settings** → **Branches**.
2. Click **Add branch protection rule**.
3. Set **Branch name pattern** to `main` (or your default branch name).
4. Enable **Require a pull request before merging**.
5. Enable **Require approvals** and set the minimum to `1`.
6. Click **Save changes**.

![Branch protection rule with required review enabled](images/27-branch-protection.png)

With this rule, even if an agent opens a pull request against `main`, a human must approve it before it can merge.

## Cap safe outputs in your workflow brief

The Markdown brief for your workflow controls how many write actions the agent is permitted to make per run. Add an explicit cap in your brief:

```markdown
**Output limit:** Create at most one pull request per run. Do not push commits directly to `main`.
```

Prose limits like this are enforced by the agent's reasoning — they complement (but do not replace) the API-scope restrictions in your frontmatter.

For workflows that produce many outputs (for example, a labeler that updates dozens of issues), use the `max-outputs` frontmatter key if your gh-aw version supports it, or add a countable limit to the brief (e.g., "label at most 20 issues per run").

## Require a human approval step for sensitive workflows

Some workflows should pause before taking a destructive or high-impact action. Use GitHub's [Environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) feature to require manual approval.

1. Go to **Settings** → **Environments** → **New environment**.
2. Name it `production` (or a name that reflects the sensitivity).
3. Enable **Required reviewers** and add yourself or a team.
4. In your workflow frontmatter, add `environment: production` to the job.

```yaml
---
name: Deploy Summary Report
on:
  schedule:
    - cron: '0 9 * * 1'
permissions:
  contents: write
environment: production
---
```

The workflow will pause at the environment gate and send a notification to the required reviewers before proceeding.

> [!NOTE]
> Environments are a GitHub Actions feature — they work identically in agentic workflows because `gh-aw` frontmatter is Actions-compatible.

## ✅ Checkpoint

- [ ] You reviewed your workflow's `permissions:` block and removed any scopes it does not need.
- [ ] You set at least one scope explicitly (not using the default `permissions: write-all`).
- [ ] Your target branch (`main` or equivalent) has a branch protection rule requiring at least one human approval.
- [ ] You added an explicit output limit in your workflow brief or frontmatter.
- [ ] You can explain the difference between the repository-layer and workflow-layer governance controls.

**Next:** [Workshop Complete — What to Explore Next](14-next-steps.md)
