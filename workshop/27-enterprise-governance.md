<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Autonomy without guardrails creates risk — this step shows you how to set the right organisational controls so your team can ship confidently at scale._

## 🎯 What You'll Do

You will configure organisation-level policies that govern every agentic workflow in your GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) environment. By the end of this step, you will have:

- Reviewed the key org-level settings that control whether agentic workflows can run at all.
- Applied workflow approval gates so new or changed workflows require review before they execute.
- Verified that network and proxy constraints don't silently block your agent.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** or **Admin** access to your GitHub organisation, or can pair with someone who does.
- You are running on GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) 3.12+. If you are on `github.com` Personal accounts skip the GHES-specific sections; the approval-gate and permission sections still apply.

> [!NOTE]
> If you haven't confirmed your GHES version or Copilot Enterprise licensing yet, see [Side Quest: Enterprise Setup Considerations](side-quest-enterprise-setup.md) before continuing.

## Steps

### Review the Copilot and Actions policies for your organisation

Agentic workflows depend on two separate policy surfaces: **GitHub Actions** and **GitHub Copilot**. Both must be enabled and correctly scoped before any agentic workflow can run.

In your organisation's settings:

1. Go to **Settings → Actions → General**.
2. Under **Actions permissions**, confirm that Actions are enabled for the repositories where your workflows live.
3. Go to **Settings → Copilot → Policies**.
4. Confirm **Copilot in GitHub Actions** (the cloud agent feature) is enabled for the organisation.
5. Check whether Copilot access is limited to specific users or teams — agents running under the default `GITHUB_TOKEN` will use the account that triggered the run. Confirm that account has a Copilot seat.

![Org-level Copilot policy settings](images/27-copilot-org-policy.png)

<details>
<summary>🖥️ Finding the right settings page</summary>

- Organisation settings are at `https://github.com/organizations/<org>/settings/actions` (or your GHES equivalent).
- For GHEC with Enterprise Managed Users (EMU), some policies are inherited from the enterprise account and cannot be overridden at the org level — check **Enterprise settings → Policies → Actions** first.

</details>

### Configure workflow approval gates

Approval gates prevent unreviewed or modified workflows from running automatically. This is especially important when workflows have write permissions or access to secrets.

1. Go to **Settings → Actions → General → Fork pull request workflows**.
2. Under **Required approval for first-time contributors**, choose a policy appropriate for your organisation's risk tolerance.
3. For tighter control on protected branches, add a **branch protection rule** requiring at least one reviewer before merging changes to `.github/workflows/`.

> [!TIP]
> A simple ruleset works well: require one reviewer from a `workflow-approvers` team before any `.github/workflows/` file can be merged. This gives your security team visibility without slowing down normal development.

### Restrict which repositories can run agentic workflows

Not every repository in a large organisation needs agentic workflow access. You can scope access:

1. In **Settings → Actions → General**, set **Actions permissions** to **Allow select non-admin workflows** or restrict to specific repositories.
2. Use [repository rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) to enforce code-review requirements on the `.github/workflows/` path specifically.
3. Consider creating a dedicated **agentic-workflows** team with write access only to approved repositories, and requiring all workflow changes to go through that team.

### Check for network and proxy constraints (GHES)

If your GHES instance sits behind a proxy or firewall, the Copilot cloud agent needs outbound HTTPS access to reach GitHub's model infrastructure.

1. Confirm your GHES instance has outbound access to `api.github.com` and `copilot-proxy.githubusercontent.com`.
2. If a corporate proxy is required, set the `HTTPS_PROXY` environment variable in your self-hosted runner's configuration. Edit the runner `.env` file and add:

```bash
# In the runner's .env file (e.g., /actions-runner/.env)
HTTPS_PROXY=https://proxy.example.com:8080
NO_PROXY=localhost,127.0.0.1,.example.com
```

Restart the runner service after saving, then trigger a manual test run from the **Actions** tab to verify the agent can connect.

<details>
<summary>🖥️ Verify connectivity without a runner restart</summary>

From the runner host, run:

```bash
curl -I https://api.github.com
curl -I https://copilot-proxy.githubusercontent.com
```

Both should return an HTTP `200` or `301` — any `000` or connection-refused response indicates a proxy or firewall block.

</details>

### Audit who can approve and trigger workflows

Separation of duties matters. Review your current permission structure:

1. In **Settings → Actions → General**, check **Workflow permissions** — confirm the default `GITHUB_TOKEN` scope matches what your workflows actually need (prefer `read` as the org default; grant `write` per-workflow in frontmatter).
2. Review which teams have **write** access to repositories that contain agentic workflows.
3. Check **Environments** — if your workflows target a deployment environment, you can add required reviewers to that environment so every run that touches production needs human sign-off.

> [!IMPORTANT]
> Granting `write` access to the `GITHUB_TOKEN` at the org level means every workflow in every repository can push code or open PRs. Prefer setting the org default to `read` and elevating permissions only in the workflow frontmatter where needed.

## ✅ Checkpoint

- [ ] You confirmed that **Copilot in GitHub Actions** is enabled for your organisation
- [ ] You verified that Actions permissions are correctly scoped to the repositories that need them
- [ ] You added or confirmed a branch protection rule requiring review before changes to `.github/workflows/` can merge
- [ ] You checked that the default `GITHUB_TOKEN` scope in your org is set to `read` (write-only where explicitly needed)
- [ ] If on GHES, you verified outbound connectivity to `api.github.com` and `copilot-proxy.githubusercontent.com` from your runner host

**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose another path, or explore the [Governance Guide](https://github.github.com/gh-aw/guides/governance/) for deeper policy documentation.
