<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Individual workflow hygiene is good — org-level policy makes that hygiene mandatory, auditable, and scalable across every team._

## 🎯 What You'll Do

You'll explore the organisation-level controls that let admins define what agentic workflows can do, require peer review before deployment, and enforce guardrails automatically. By the end of this step you'll know which knobs exist, where to find them, and how to communicate them to your teammates.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to a GitHub organisation, or you're following along to understand what to ask your admin.
- _(GHES users)_ Your site admin has confirmed that Copilot is enabled at the enterprise level.

## Steps

### Understand the policy layers

Agentic workflow governance operates at three nested levels:

| Level | Who controls it | Example controls |
|-------|----------------|-----------------|
| **Enterprise** | Enterprise owner | Copilot enablement, approved models, network egress |
| **Organisation** | Org admin | Workflow review requirements, allowed safe-output types |
| **Repository** | Repo admin / `CODEOWNERS` | Branch protection, required reviewers for `.github/workflows/` |

Each lower level can only restrict — it cannot grant more than the level above allows.

### Require review for workflow files

Agentic workflow definitions live in `.github/workflows/`. Treat them like application code: require at least one reviewer before a change merges.

**To add a `CODEOWNERS` rule:**

1. In your repository, open (or create) `.github/CODEOWNERS`.
2. Add a line that maps workflow files to the team responsible for reviewing them:

```text
.github/workflows/*.md   @your-org/ai-workflow-reviewers
.github/workflows/*.lock.yml   @your-org/ai-workflow-reviewers
```

1. Save and commit the file.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your repository on GitHub.
2. Click **Add file → Create new file** and enter `.github/CODEOWNERS` as the path.
3. Paste the ownership rules into the editor.
4. Click **Commit new file**.

</details>

With this in place, any pull request that modifies a workflow definition requires approval from a member of `ai-workflow-reviewers` before it can merge.

> [!NOTE]
> CODEOWNERS rules only take effect when branch protection is enabled and **Require review from Code Owners** is checked in the branch protection settings.

### Enable branch protection on `main`

1. Go to **Settings → Branches** in your repository.
2. Click **Add branch ruleset** (or edit the existing rule for `main`).
3. Enable **Require a pull request before merging** and **Require review from Code Owners**.
4. Save the ruleset.

Now no workflow change can reach `main` without a human reviewer signing off.

### Configure organisation-wide Copilot policies

Org admins can restrict which Copilot features and models are available to agentic workflows:

1. Go to your organisation's **Settings → Copilot → Policies**.
2. Review the **Allowed models** list. Remove models your organisation hasn't approved.
3. Check **Agentic workflow permissions** — this controls whether workflows in your org can use `safe-outputs` types like `push_to_branch` or `create_pull_request`.

> [!IMPORTANT]
> If you restrict models at the org level, any workflow that explicitly names a disallowed model in its frontmatter `engine:` field will fail at runtime. Audit existing workflows with `gh aw list` before changing the allowed model set.

### Audit deployed workflows across the org

Use `gh aw list` to see all workflows in a repository:

```bash
gh aw list --repo your-org/your-repo
```

To scan multiple repositories, loop over them:

```bash
gh repo list your-org --json nameWithOwner --jq '.[].nameWithOwner' \
  | xargs -I{} gh aw list --repo {}
```

This gives you an inventory of every agentic workflow in the org — useful before rolling out a new policy.

> [!TIP]
> On GitHub Enterprise Server (GHES), replace `your-org` with the full `enterprise/org` path if your CLI targets a non-github.com host. Set `GH_HOST` or pass `--hostname` to the `gh` commands above.

### Document your governance decisions

Create a short `WORKFLOW_POLICY.md` at the repository or org level that captures:

- Which safe-output types are permitted (and which are blocked by policy)
- The review process for new or modified workflows
- The approved model list and how to request an exception
- The on-call contact for agentic workflow incidents

Keeping this doc in the repository makes it discoverable and version-controlled alongside the workflows it governs.

## ✅ Checkpoint

- [ ] You can describe the three levels of agentic workflow governance (enterprise, org, repository)
- [ ] `.github/CODEOWNERS` maps workflow files to a review team in your repository
- [ ] Branch protection on `main` requires a Code Owner review before merging
- [ ] You have reviewed the **Allowed models** list in your org's Copilot settings
- [ ] You ran `gh aw list` and can describe what workflows are deployed in your repository
- [ ] You know who to contact if a policy change is needed in your organisation
- [ ] _(GHES)_ You've confirmed that your site admin has applied the enterprise-level Copilot enablement before repository-level controls take effect

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
