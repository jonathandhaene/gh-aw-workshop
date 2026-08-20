<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Roll Out Agentic Workflows Across Your Organisation

> _A great workflow is worth sharing — but doing it safely at organisation scale means understanding secrets, permissions, and governance before you hit deploy._

## 🎯 What You'll Do

You'll learn how to share a workflow template with your whole organisation, create organisation-level secrets, and apply a basic governance policy so every team adopts the workflow in a controlled, auditable way.

## 📋 Before You Start

- You have a working agentic workflow committed to a repository (completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)).
- You have **Owner** or **Admin** access to a GitHub organisation, or a colleague who does and can follow these steps alongside you.

> [!NOTE]
> If you are a GHES or GHEC user, the organisation settings screens look the same as on github.com unless your administrator has applied custom enterprise policies. Check with your GitHub administrator if a step is grayed out or unavailable.

## Steps

### Create an organisation-level secret

When many repositories share one workflow, storing the Copilot token (or any API key) once at the organisation level is safer and easier to rotate than per-repo secrets.

1. Open your organisation on GitHub and click **Settings**.
2. In the left sidebar, click **Secrets and variables** → **Actions**.
3. Click **New organisation secret**.
4. Name it `COPILOT_GITHUB_TOKEN` (or match the name used in your workflow frontmatter).
5. Under **Repository access**, choose **Selected repositories** and add only the repos that need this workflow.
6. Click **Add secret**.

<details>
<summary>🖥️ Verify the secret is accessible</summary>

After saving, navigate to one of the selected repositories, open **Settings** → **Secrets and variables** → **Actions**, and check that the organisation secret appears in the **Organisation secrets** section.

</details>

### Publish your workflow as a shared template

Any `.md` file pushed to `.github/workflows/` in an organisation's `.github` repository becomes discoverable via `gh aw add`.

1. In your organisation, create (or open) the `.github` repository.
2. Add a `.github/workflows/` folder if it doesn't exist.
3. Copy your finished workflow `.md` and its compiled `.lock.yml` into that folder and commit both files.

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "Add daily-status workflow template"
git push
```

Now any team member in the organisation can install the template with one command:

```bash
gh aw add <org-name>/.github/daily-status
```

### Write a lightweight governance policy

A one-page policy prevents workflow sprawl and sets clear expectations. Create `.github/AGENTIC_WORKFLOWS_POLICY.md` in your `.github` repository with at minimum:

- **Approved templates** — list the templates teams may use without further review.
- **Secrets hygiene** — require organisation-level secrets over per-repo secrets.
- **Cost guardrails** — specify the minimum `max-daily-ai-credits` value for any production workflow.
- **Review cadence** — define how often workflow outputs are reviewed by a human.

> [!TIP]
> GitHub's [governance guide for agentic workflows](https://github.github.com/gh-aw/guides/governance/) walks through policy templates and approval workflows for enterprise environments.

### Announce to your team

Send your team a short message with:

1. The `gh aw add` command to install the template.
2. A link to the policy document.
3. The name of the organisation secret they will use (not the value).

## ✅ Checkpoint

- [ ] You created an organisation-level secret and scoped it to the relevant repositories
- [ ] You pushed your workflow template (`.md` and `.lock.yml`) to the organisation's `.github` repository
- [ ] A teammate (or you in a second repo) successfully installed the template with `gh aw add`
- [ ] You created or drafted an `AGENTIC_WORKFLOWS_POLICY.md` covering secrets, cost guardrails, and review cadence
- [ ] You confirmed the policy is visible in the organisation's `.github` repository

**Next:** [What's Next? Keep Exploring](14-next-steps.md)
