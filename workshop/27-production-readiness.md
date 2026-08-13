<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Harden Your Workflow for Production

> _Getting a workflow running is one thing — making it safe, observable, and reliable enough to leave running unattended in a shared repository is another._

## 🎯 What You'll Do

You'll review a production-readiness checklist for agentic workflows, tighten permissions and cost guardrails, and verify that your workflow is ready to run reliably in a team or enterprise environment.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow runs on `main` and has a passing execution in the Actions tab.
- _(Enterprise users)_ You have access to your organisation's Actions settings page.

## Steps

### Lock down permissions

By default, `GITHUB_TOKEN` has read access to most repository scopes and limited write access. Agentic workflows should declare only the scopes they actually need.

Open your workflow's `.md` file and verify the `permissions:` block is as narrow as possible:

```yaml
---
name: Daily Repository Status
on:
  schedule:
    - cron: "0 8 * * 1-5"
  workflow_dispatch:
permissions:
  contents: read
  issues: write
---
```

If your workflow only reads repository data and posts an issue comment, those two scopes are all it needs. Remove any scope you cannot justify — for example, `pull-requests: write` if you never open or update pull requests.

> [!TIP]
> The full list of available scopes and their effects is documented at [Permissions reference](https://github.github.com/gh-aw/reference/permissions/). Start from zero and add only what is needed.

### Restrict network access

Agentic workflows can make HTTP requests unless you lock them down. If your workflow only needs GitHub APIs, restrict outbound network traffic:

```yaml
---
name: Daily Repository Status
on:
  schedule:
    - cron: "0 8 * * 1-5"
  workflow_dispatch:
permissions:
  contents: read
  issues: write
network:
  allowed:
    - api.github.com
---
```

Adding `network.allowed` tells the runtime to block all other outbound requests. This is especially important in enterprise environments where prompt-injection attacks could attempt to exfiltrate data to an external URL.

> [!NOTE]
> `network.allowed` accepts hostnames, not IP addresses. Wildcards like `*.github.com` are supported. See [Network reference](https://github.github.com/gh-aw/reference/network/) for syntax.

### Add a timeout

Every production workflow should declare a `timeout-minutes` value. Without one, a hung agent run can consume AIC and block your quota for hours.

Add this to your frontmatter:

```yaml
timeout-minutes: 10
```

Ten minutes is a reasonable ceiling for a lightweight status or review workflow. Adjust upward only for workflows that fetch large datasets or call external APIs with known latency.

### Verify safe-output limits

Check that each `safe-outputs` entry has a sensible `limit`. A runaway loop or re-trigger scenario should not be able to spam your repository.

```yaml
safe-outputs:
  create-issue: limit: 1
  add-comment: limit: 3
```

If you omit `limit`, there is no cap on how many times that action can fire in a single run. Always set a limit for any write action in a production workflow.

### Confirm branch protection (enterprise / team repositories)

If your workflow commits files or opens pull requests, verify that branch protection rules are in place on `main`:

1. Go to **Settings** → **Branches** in your repository.
2. Confirm that a branch protection rule exists for `main`.
3. Check that **Require pull request reviews before merging** is enabled if your workflow can push directly.

This ensures a human reviews any code the agent proposes to merge.

### Run a final health check

```bash
gh aw doctor
```

Review the output and resolve any warnings before leaving the workflow running on a schedule.

## ✅ Checkpoint

- [ ] My workflow's `permissions:` block lists only the scopes it actually uses
- [ ] I have added a `network.allowed` list to restrict outbound HTTP requests
- [ ] My workflow frontmatter includes a `timeout-minutes` value
- [ ] Every `safe-outputs` entry has a `limit` set
- [ ] I have verified branch protection rules on `main` (or confirmed they are not required for my setup)
- [ ] `gh aw doctor` reports no unresolved warnings

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose your next path.
<!-- /journey -->
