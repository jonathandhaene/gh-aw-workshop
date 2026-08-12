<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Enforce Organisation-Level Workflow Policies

> _Agentic workflows are powerful — organisation policy controls ensure that power is wielded safely across every team and repository._

## 🎯 What You'll Do

You'll learn how to apply organisation-level governance to agentic workflows: required reviewers before a run, allowed-actions restrictions, repository-level approval gates, and how to audit policy compliance on GHEC and GHES environments.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to a GitHub organisation (or your instructor has shared a demo organisation).
- _(GHES users)_ Your instance is version 3.12 or later and Copilot Enterprise is enabled. If you haven't confirmed this, see [Enterprise Setup Considerations](side-quest-enterprise-setup.md) first.

## Steps

### Understand the three layers of policy

Governance for agentic workflows happens at three levels. Each layer narrows what the layer below it can do.

| Layer | Where it lives | Controls |
|---|---|---|
| **Organisation** | Org → Settings → Actions | Which actions and workflows are allowed; default permissions for `GITHUB_TOKEN` |
| **Repository** | Repo → Settings → Actions | Inherits org settings; can be further restricted per repo |
| **Workflow file** | `.md` frontmatter `permissions:` | Per-run least-privilege scope applied at compile time |

The organisation and repository layers are managed by admins. The workflow-file layer is the author's responsibility — you own it.

> [!NOTE]
> On GHES, the org and enterprise-level controls are mirrored under **Enterprise Settings → Policies → Actions**. Settings applied at the enterprise level override org-level settings for all organisations in that enterprise.

### Review your organisation's allowed-actions policy

1. Go to your organisation on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Actions permissions**, review which option is selected.

| Setting | Effect on agentic workflows |
|---|---|
| **Allow all actions** | Any action referenced in the compiled `.lock.yml` can run |
| **Allow actions created by GitHub** | Only GitHub-authored actions — blocks third-party actions used in the lock file |
| **Allow select actions** | You must add any action to the allowlist before it can run |

If your organisation uses **Allow select actions**, add the `gh-aw` runner action to the allowlist. Your admin can locate the exact action identifier in an existing workflow run log.

### Require a manual approval before agentic runs

Agentic workflows can write to your repository, open issues, and post comments. You may want a human to approve each run before it starts — especially for workflows targeting `main` or production systems.

**From the repository Actions settings:**

1. Navigate to your repository on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Fork pull request workflows**, choose **Require approval for first-time contributors** or the stricter **Require approval for all outside contributors**, depending on your team's threat model.
4. For internal workflows, use [Environments with required reviewers](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#required-reviewers) instead:
   - Create an environment named `agentic-write` (or any descriptive name).
   - Add one or more required reviewers.
   - In your agentic workflow's compiled `.lock.yml`, set `environment: agentic-write` on any job that writes to the repository.

> [!TIP]
> Required reviewers add friction intentionally. Use them for high-consequence workflows (those that merge PRs, push to `main`, or post to external systems) and leave lower-stakes workflows unrestricted.

### Set a default `GITHUB_TOKEN` permission for the organisation

Agentic workflows compile `permissions:` into the `.lock.yml`. However, any permission not declared in the workflow falls back to the organisation default.

1. Go to **Organisation Settings → Actions → General**.
2. Under **Workflow permissions**, select **Read repository contents and packages permissions** (read-only default).
3. Leave **Allow GitHub Actions to create and approve pull requests** unchecked unless your workflows specifically need it.

With a read-only default, any agentic workflow that needs write access must explicitly declare it in `permissions:`. This makes permission escalation visible in code review.

### Audit which workflows have write permissions

Use the `gh` CLI to list all workflow files and spot any that declare broad write permissions:

```bash
gh api repos/{owner}/{repo}/contents/.github/workflows \
  --jq '.[].name'
```

For each `.lock.yml` in the list, search for `write` in the compiled output:

```bash
grep -r "write" .github/workflows/*.lock.yml
```

Review each hit. If a workflow declares `contents: write` or `pull-requests: write`, confirm that those permissions are necessary for the workflow's stated goal and that the brief's `safe-outputs:` block limits what the agent can actually write.

<details>
<summary>🖥️ Review workflow permissions in the GitHub UI</summary>

1. Go to your repository on GitHub and click the **Actions** tab.
2. Select a workflow run.
3. Click a job, then expand the **Set up job** step.
4. Scroll to the **GITHUB_TOKEN Permissions** section — it lists every scope the run was granted.

This view is useful for confirming that a compiled lock file is enforcing the permissions declared in the workflow `.md` file.

</details>

### Apply branch protection to guard write targets

If your agentic workflow can push commits or merge pull requests, protect the target branch:

1. Go to **Repository Settings → Branches** → **Add branch ruleset** (or edit an existing rule for `main`).
2. Enable **Require a pull request before merging** and set a minimum number of approvals (at least 1 for agentic-authored PRs).
3. Enable **Require status checks to pass** so CI must pass before the agent's PR can merge.
4. Enable **Do not allow bypassing the above settings** — this prevents the `GITHUB_TOKEN` from bypassing protection even when it has `contents: write`.

> [!IMPORTANT]
> <details>
> <summary><b>GHES users: confirm branch rulesets are available on your instance version.</b></summary>
>
> Branch rulesets were introduced in GHES 3.11. If you are on an older instance, use the legacy **Branch protection rules** UI instead. The protection options are equivalent; only the interface and API paths differ.
>
> </details>

### Review policy compliance with `gh aw audit`

After applying org and repository policy, run a compliance check on your workflow:

```bash
gh aw audit --workflow .github/workflows/daily-report-status.md
```

The audit report includes a **Governance** section that flags:

- Permissions wider than the minimum needed for the declared `safe-outputs`
- `safe-outputs` entries that allow writes without a matching `permissions:` scope
- Missing `timeout-minutes` values

Fix any flagged items in the `.md` file and recompile with `gh aw compile`.

## ✅ Checkpoint

- [ ] You reviewed your organisation's **Actions permissions** setting and know which policy is active
- [ ] You can explain the difference between the organisation-level permission default and the per-workflow `permissions:` block
- [ ] You identified at least one workflow in your repository that has write permissions and confirmed those permissions are necessary
- [ ] You created or identified an environment with required reviewers for a high-consequence agentic workflow
- [ ] You enabled a read-only `GITHUB_TOKEN` default at the organisation level (or confirmed it is already set)
- [ ] You ran `gh aw audit` and resolved any flagged governance findings

<!-- journey: all -->
Want to explore another advanced topic? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
