<!-- page-journey: all -->
<!-- page-adventure: core -->
# Build a Cross-Repository Workflow

> _When one repository is not enough, agentic workflows can gather data from many repositories and synthesise it into a single report — a superpower for team leads, platform teams, and enterprise org admins._

## 🎯 What You'll Do

You'll extend your daily status workflow to pull data from two or more repositories and merge the results into one coherent summary. By the end of this step, you'll have a workflow that produces an org-level health report automatically, every day.

## 📋 Before You Start

- Completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)
- Your `daily-report-status` workflow runs successfully on a schedule
- You have **read access** to at least one other repository in your organization (or a second personal repository you own)
- Your workflow's `permissions` block already includes `issues: write` (added in earlier steps)

> [!NOTE]
> Cross-repository reads use the same `GITHUB_TOKEN` that all agentic workflows receive. That token is scoped to the repository where the workflow lives. To read **other** repositories in the same organization, the organization's admin must grant the token org-level read access, or you can use a [fine-grained personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token) stored as a repository secret.

---

## Understand What "Cross-Repository" Means in gh-aw

In a standard workflow, the agent reads data from the repository it runs in — that is its default scope. Cross-repository workflows expand that scope by:

1. **Listing repositories** — using a GitHub API call or `gh` command to enumerate which repos to inspect.
2. **Fetching data per repo** — looping through each repository and collecting the metrics you care about (open issues, stale PRs, last push date, etc.).
3. **Synthesising the data** — passing the collected data to the agent brief so it can compare, rank, and summarise across all repositories.

The gh-aw [`cross-repository`](https://github.github.com/gh-aw/reference/cross-repository/) reference covers advanced patterns such as using a matrix job to fan-out across repos in parallel, then converging results.

---

## Add a Deterministic Data-Fetch Step

The most reliable pattern is a **hybrid workflow**: a deterministic step collects the raw data using `gh` commands, and the agent brief synthesises it. This keeps your AI credits focused on reasoning rather than data retrieval.

Open `.github/workflows/daily-report-status.md` in your editor and add a `steps` block to the frontmatter. The step below runs before the agent and outputs a JSON snapshot of open pull requests across two repositories.

```yaml
steps:
  - name: Collect cross-repo data
    run: |
      echo "## Repository Data" >> $GITHUB_STEP_SUMMARY
      gh pr list --repo "$REPO_A" --state open --json number,title,updatedAt \
        | jq '.' > /tmp/repo-a-prs.json
      gh pr list --repo "$REPO_B" --state open --json number,title,updatedAt \
        | jq '.' > /tmp/repo-b-prs.json
    env:
      REPO_A: your-org/repo-a
      REPO_B: your-org/repo-b
      GH_TOKEN: ${{ secrets.ORG_READ_PAT }}
```

Replace `your-org/repo-a` and `your-org/repo-b` with two real repository slugs you can read.

<details>
<summary>🔑 Setting up ORG_READ_PAT</summary>

1. Go to **Settings → Developer settings → Personal access tokens → Fine-grained tokens** on GitHub.
2. Click **Generate new token**. Give it a descriptive name like `cross-repo-aw-read`.
3. Set **Repository access** to the repositories you need to read and grant **Pull requests: Read** and **Issues: Read**.
4. Copy the token value.
5. In your practice repository, go to **Settings → Secrets and variables → Actions → New repository secret**.
6. Name it `ORG_READ_PAT` and paste the token value.

</details>

---

## Update the Task Brief to Synthesise Across Repos

Now update the Markdown body (the agent task brief) to tell the agent about the data it will receive and what to do with it.

Scroll below the frontmatter `---` closing fence and add or replace the brief with something like:

```text
You will receive JSON files at /tmp/repo-a-prs.json and /tmp/repo-b-prs.json.
Each file contains the open pull requests for one repository.

Read both files. Then write a short daily health report that:
- Lists the total open PR count per repository.
- Highlights any PR that has not been updated in more than 7 days.
- Identifies the repository with the highest PR backlog.
- Ends with one actionable recommendation for the team.

Post the report as a new issue comment on issue #1 in this repository.
```

> [!TIP]
> Keep the brief concrete and bounded. The agent should never need to decide _which_ repositories to inspect — the deterministic step already handles that. The brief only needs to interpret and present the collected data.

---

## Compile and Test

After editing, recompile the workflow:

```bash
gh aw compile
```

Push the updated `.md` and `.lock.yml` files:

```bash
git add .github/workflows/daily-report-status.md .github/workflows/daily-report-status.lock.yml
git commit -m "feat: add cross-repository data collection step"
git push
```

Then trigger a manual run from the **Actions** tab to confirm the step runs without errors and the agent receives both data files.

<details>
<summary>🖥️ GitHub UI alternative for editing the workflow</summary>

1. In your repository on GitHub, navigate to `.github/workflows/daily-report-status.md`.
2. Click the **pencil icon (✏️)** to open the inline editor.
3. Add the `steps:` block to the frontmatter and update the task brief.
4. Click **Commit changes**, choose a branch, and open a pull request if desired.
5. After merging, trigger a manual run from the **Actions** tab.

</details>

---

## ✅ Checkpoint

- [ ] Your workflow frontmatter has a `steps:` block with a `run:` command that calls `gh pr list` for at least two repositories
- [ ] `ORG_READ_PAT` is stored as a repository secret and referenced in the step's `env:` block
- [ ] Your task brief tells the agent where to find the collected data files and how to synthesise them
- [ ] `gh aw compile` completes without errors and the updated `.lock.yml` is committed
- [ ] A manual run completes and the agent posts a cross-repository health report
- [ ] You can explain the difference between deterministic data collection and agentic synthesis

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->
