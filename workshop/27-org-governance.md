# Govern Agentic Workflows Across Your Organisation

> _Before your AI workflows go org-wide, make sure the right guardrails are in place._

## 🎯 What You'll Do

Apply organisation-level controls to your agentic workflows: restrict who can create and approve workflow changes, set org-wide AI credit policies, and confirm that your workflow runs inside your enterprise's approved boundaries.

## 📋 Before You Start

- You have a working, scheduled agentic workflow from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You have reviewed AI credit costs in [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have repository **admin** access (or work with someone who does).

## Steps

### Understand what org admins control

When you run agentic workflows inside a GitHub organisation, several controls live at the org level — not in your repository.

| Control | Where to find it | What it does |
|---------|-----------------|--------------|
| Actions permissions | Org → **Settings** → **Actions** → **General** | Which repositories can run Actions |
| Copilot access for Actions | Org → **Settings** → **Copilot** | Enables/disables Copilot in runners |
| Allowed actions | Org → **Settings** → **Actions** → **General** | Which action publishers are permitted |
| Secrets visibility | Org → **Settings** → **Secrets and variables** | Org-level secrets for selected repositories |

Ask your org admin which of these policies are active before deploying a workflow that relies on Copilot or external network access.

> [!NOTE]
> On GitHub Enterprise Server (GHES), these settings may be pre-configured by a site administrator. Check with your GitHub admin team before assuming defaults apply.

### Restrict who can change workflow files

A new agentic workflow takes effect as soon as its `.md` (and compiled `.lock.yml`) is merged to your default branch. Anyone with write access can modify it.

To add a review gate:

1. In your repository, go to **Settings** → **Branches**.
2. Click **Add branch protection rule** (or **Edit** for an existing rule on `main`).
3. Enable **Require a pull request before merging** and set **Require approvals** to at least **1**.
4. Optionally enable **Require review from Code Owners** — then add a `CODEOWNERS` file:

```text
# .github/CODEOWNERS
.github/workflows/ @your-org/workflow-reviewers
```

Every agentic workflow change now needs an approved PR before it can run.

<details>
<summary>🏢 Enterprise: use required review on GHES or GHEC</summary>

On GHES, branch protection rules work identically to github.com. On GHEC with enterprise-managed users (EMU), your enterprise admin can enforce branch protection across all repositories using a **repository policy** in the enterprise settings panel. Ask your GitHub admin whether a org-wide required-review policy is already in place — you may not need to create one per repository.

</details>

### Validate that your workflow stays inside enterprise network boundaries

If your organisation requires all outbound traffic to go through a proxy or allowlist, your agentic workflow's `network.allowed-domains` must match those constraints.

Check your current workflow file for a `network:` block:

```yaml
network:
  allowed-domains:
    - api.github.com
    - copilot-proxy.githubusercontent.com
```

Domains not in the allowlist are blocked at the runner level. If your workflow calls an external MCP server or API, confirm with your network team that the target domain is reachable from your Actions runners.

> [!TIP]
> Self-hosted runners on a private network may have a different allowlist than GitHub-hosted runners. Test with `gh aw run` and check the firewall log in the audit report (`gh aw audit <run-id>`) if connections are refused unexpectedly.

## ✅ Checkpoint

- [ ] You identified which Actions permissions and Copilot policies apply to your organisation
- [ ] You added (or confirmed) branch protection on the workflow branch with at least one required review
- [ ] You created or reviewed a `CODEOWNERS` entry covering `.github/workflows/`
- [ ] You calculated a per-workflow AI credit cap aligned with your org's monthly allocation
- [ ] You confirmed that your workflow's `network.allowed-domains` is compatible with your network policies

**Next:** [What's Next? Keep Exploring](14-next-steps.md)
