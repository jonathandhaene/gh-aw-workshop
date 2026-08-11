<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Enterprise Governance and Policy Controls

> _Knowing your workflows run correctly is not enough — enterprise teams also need confidence that every run stays within approved boundaries._

## 🎯 What You'll Do

You'll apply two organisation-level guardrails — a required-approval environment and a branch ruleset — so your agentic workflow runs only when authorised, and only trusted contributors can change it.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow runs successfully with the cost guardrails from the previous step.

> [!NOTE]
> <details>
> <summary><b>GHES users: confirm your version before continuing.</b></summary>
>
> Required-approval environments and rulesets require GitHub Enterprise Server 3.12 or later. Confirm your version with your admin before proceeding.
>
> </details>

## Steps

### Add a required-approval environment

Required approvals give a human a chance to review each run before it executes — valuable for any workflow that writes to shared resources.

1. In your practice repository, go to **Settings** → **Environments** → **New environment**.
2. Name it `agentic-review`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add yourself.
4. In your workflow frontmatter, add an `environment:` key:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * 1-5"
  workflow_dispatch:
environment: agentic-review
permissions:
  issues: write
  contents: read
```

1. Commit the change and recompile:

```bash
gh aw compile
```

<details>
<summary>🖥️ GitHub UI alternative (no terminal)</summary>

Edit your workflow `.md` file directly from the GitHub UI using the pencil icon (✏️), commit the change, then trigger a manual run from the **Actions** tab. Any frontmatter errors appear in the run log.

</details>

On the next manual run, GitHub pauses at the environment gate and sends a review notification. Approve it to let the run continue.

### Protect workflow files with a branch ruleset

Workflow files are code. A ruleset lets you restrict pushes to `.github/workflows/` so only approved contributors can change what your workflows do.

1. Go to **Settings** → **Rules** → **Rulesets** → **New branch ruleset**.
2. Set the **Target branches** to `main`.
3. Under **Rules**, enable **Restrict pushes** and add a file path rule for `.github/workflows/**`.
4. Add the users or teams allowed to push workflow changes.
5. Save the ruleset.

Pull requests from other contributors will now require review from an approved committer before they can be merged into `main`.

### Verify governance in the audit log

1. Trigger a manual workflow run from the **Actions** tab.
2. Approve it when the review notification arrives.
3. Once complete, run:

```bash
gh aw audit <run-id>
```

Confirm the audit entry records the approver and the environment name.

## ✅ Checkpoint

- [ ] You created an `agentic-review` environment with at least one required reviewer
- [ ] You added `environment: agentic-review` to your workflow frontmatter and compiled the change
- [ ] You triggered a manual run, approved it at the environment gate, and confirmed it completed
- [ ] You created a branch ruleset that restricts pushes to `.github/workflows/**` to approved contributors
- [ ] You confirmed the approval is visible in `gh aw audit` output or the GitHub audit log
- [ ] You can explain which governance layer to use for each of the three risk types: model access, run approval, and file integrity

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->
