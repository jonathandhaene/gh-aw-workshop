<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _A single workflow is easy to trust — a fleet of them needs a plan._

## 🎯 What You'll Do

You'll learn how to apply organisation-level controls to agentic workflows: required reviewers, permitted models, allowed tools, and policy guardrails that apply consistently across every repository in your organisation.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have access to an organisation where you are an owner (or a GitHub Enterprise administrator).
- _(Enterprise)_ Your organisation uses GitHub Enterprise Cloud or GitHub Enterprise Server 3.16+.

## Steps

### Understand why governance matters

When agentic workflows spread across dozens of repositories, two risks appear that did not exist with a single workflow:

- **Inconsistent permissions** — one repository may allow a workflow to push directly to `main`; another may not. If you do not standardise this, security gaps can emerge quietly.
- **Uncontrolled model and tool selection** — different teams may choose different AI models or MCP servers, making costs and outputs hard to predict and audit.

Organisation-level governance lets you set a baseline that every workflow inherits.

### Set organisation-level workflow policies

In the GitHub UI:

1. Go to your organisation page and click **Settings**.
2. In the left sidebar, under **Code, planning, and automation**, click **Actions** → **General**.
3. Under **Workflow permissions**, choose the least-privileged default that still allows your workflows to operate — usually **Read repository contents and packages permissions**.
4. Check **Allow GitHub Actions to create and approve pull requests** only if your agentic workflows need to open PRs (such as the PR Auto-Reviewer you built earlier).
5. Click **Save**.

> [!TIP]
> Individual repositories can still grant more permissive settings on top of the organisation default, but they cannot grant more than the organisation policy allows.

### Restrict which models your workflows may use

The `engines:` frontmatter field controls which AI model a workflow calls. At the organisation level, you can enforce an allowlist so no repository can call an unapproved model.

In a workflow file, a model declaration looks like this:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
engines:
  model: copilot
---
```

> [!NOTE]
> Consult the [Engines reference](https://github.github.com/gh-aw/reference/engines/) for the full list of supported model identifiers and which require additional licensing.

To enforce a model allowlist for your organisation, ask your GitHub Enterprise administrator to configure the permitted model list in the enterprise settings. Workflows that specify a model outside the allowlist fail at compile time with a clear error message, so enforcement is visible before a run starts.

### Apply required reviewers to workflow runs

For high-impact workflows — such as those that push code or open pull requests — add an environment protection rule that requires a human review before the workflow job runs.

1. In your repository, go to **Settings** → **Environments**.
2. Click **New environment** and name it (for example, `agentic-write`).
3. Under **Deployment protection rules**, click **Required reviewers** and add the team or individual who must approve.
4. In your workflow's lock file, reference this environment in the job definition.

> [!IMPORTANT]
> Environment protection rules apply to the compiled `.lock.yml`, not to the `.md` source. If you regenerate the lock file with `gh aw compile`, verify that the `environment:` key is still present in the output.

### Document your governance baseline

Keeping governance decisions in code makes them auditable. Create a short policy document in your repository:

```bash
# In your terminal
cat > .github/agentic-workflow-policy.md << 'EOF'
# Agentic Workflow Policy

- Default model: copilot
- Permitted MCP servers: github (read-only scopes only)
- Write operations: require `agentic-write` environment approval
- AI Credit budget per workflow: 1000 AIC / run, 5000 AIC / day
EOF
git add .github/agentic-workflow-policy.md
git commit -m "docs: add agentic workflow governance policy"
git push
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, click **Add file** → **Create new file**.
2. Enter `.github/agentic-workflow-policy.md` as the file name.
3. Paste your policy content into the editor.
4. Click **Commit new file**.

</details>

### Review the governance reference

For the full set of controls available at the enterprise and organisation level — including network allowlists, proxy configuration, and GHES-specific policy settings — see the [Governance guide](https://github.github.com/gh-aw/guides/governance/).

## ✅ Checkpoint

- [ ] You set the organisation's default workflow permissions to the least-privileged level your workflows need
- [ ] You confirmed or documented which AI models are permitted in your organisation
- [ ] You created (or reviewed) an environment protection rule that requires approval for write operations
- [ ] You committed a short policy document to `.github/agentic-workflow-policy.md`
- [ ] You can explain what happens when a workflow specifies a model that is outside the organisation allowlist
- [ ] You know where to find the full governance reference in the gh-aw docs

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->
