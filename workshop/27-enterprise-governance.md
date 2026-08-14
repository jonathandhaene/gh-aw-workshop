<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Running agentic workflows for yourself is one thing — rolling them out safely across an organisation requires policies, permissions, and a plan._

## 🎯 What You'll Do

Configure org-level controls that let your team run agentic workflows confidently: review the policy switches that administrators need to enable, set up repository-level permission scoping, and understand the approval workflow for third-party actions and MCP servers.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have Owner or Admin access to a GitHub organisation (or you can follow along with a personal account and note which steps require an org).

## Steps

### Confirm organisation-level Copilot policy

Agentic workflows run as the [Copilot cloud agent](https://github.github.com/gh-aw/reference/copilot-cloud-agent/), which must be enabled at the organisation level before any repository can use it.

In your GitHub organisation settings:

1. Go to **Settings** → **Copilot** → **Policies**.
2. Under **Agentic workflows**, confirm the toggle is set to **Enabled for all members** (or the scope your security team requires).
3. If the toggle is missing, your GHES instance may be below version 3.12 or the Copilot Enterprise licence may not be active — contact your GitHub administrator.

> [!NOTE]
> On GitHub Enterprise Cloud (GHEC) with an Enterprise Managed Users (EMU) configuration, the policy is set at the enterprise level, not the organisation level. Your enterprise administrator controls the toggle.

### Review repository-level `permissions:` scoping

Each agentic workflow declares the permissions it needs in its frontmatter `permissions:` block. This is your primary defence against over-privileged agents.

Follow the principle of least privilege:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * *"
permissions:
  issues: write
  contents: read
---
```

Start with `contents: read` and `issues: write` only. Add scopes one at a time as your workflow genuinely needs them. Review the [permissions reference](https://github.github.com/gh-aw/reference/permissions/) to understand what each scope allows.

### Set up a workflow approval policy

For workflows that write to issues, PRs, or repository contents, consider requiring a human review before each run in sensitive environments.

1. Go to your repository → **Settings** → **Actions** → **General**.
2. Under **Fork pull request workflows**, choose **Require approval for all outside collaborators**.
3. For internal repositories with external contributors, enable **Require approval for first-time contributors**.

This does not pause existing scheduled workflows — it governs who can create or modify workflow files, which indirectly controls what the agent can do.

### Apply network restrictions

If your organisation has security requirements around outbound network access, use the `network:` block to allow only the domains your workflow needs:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * *"
permissions:
  issues: write
  contents: read
network:
  allowed-domains:
    - api.github.com
---
```

The [network reference](https://github.github.com/gh-aw/reference/network/) lists the default-allowed domains. Adding an explicit list replaces the defaults, so include any domain your workflow calls.

> [!TIP]
> On GHES, the firewall rules you set here interact with your instance's outbound proxy configuration. Work with your GitHub administrator if the agent needs to reach external APIs through a corporate proxy.

### Document and audit your deployed workflows

Create a short inventory of every agentic workflow in your organisation. For each workflow, record:

- Repository and file path
- Trigger type (schedule, event, manual)
- Permissions it requests
- What it writes (issues, PRs, files, comments)
- Owner (team or individual responsible)

Store this inventory in a shared wiki page or repository. When the list grows, consider a dedicated `.github` repository that lists approved workflow templates — see [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md) for the template pattern.

<details>
<summary>🖥️ GitHub UI — review deployed workflows</summary>

1. In your organisation, click **Repositories** → filter by topic or name to find repositories with agentic workflows.
2. In each repository, open the **Actions** tab → **Workflows** to see what is deployed.
3. Click a workflow run and open the **Summary** to review what the agent wrote.

</details>

## ✅ Checkpoint

- [ ] You confirmed the Copilot agentic workflow policy is enabled in your organisation (or noted who to ask)
- [ ] Your workflow's `permissions:` block contains only the scopes it actually needs
- [ ] You reviewed the Actions approval policy in at least one repository
- [ ] You added or verified a `network.allowed-domains` list (or consciously chose the defaults)
- [ ] You drafted a one-line inventory entry for your deployed `daily-status` workflow
- [ ] You can name the two places — organisation settings and frontmatter — where administrators and authors share responsibility for agentic workflow security

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->
