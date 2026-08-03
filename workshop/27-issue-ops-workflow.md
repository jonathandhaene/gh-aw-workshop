<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Automate Issue Triage with an Agentic Workflow

> _Every open repository collects unlabelled, unassigned issues — use an agentic workflow to triage them the moment they arrive._

## 🎯 What You'll Do

Create an event-driven workflow that fires whenever a new issue is opened. The workflow reads the issue title and body, decides which label best describes it (`bug`, `enhancement`, `question`, or `documentation`), and applies that label automatically. By the end, new issues in your practice repository will be classified within seconds of opening — no manual triage required.

## 📋 Before You Start

- You have completed [Build Your First Event-Driven Workflow: PR Auto-Reviewer](14b-pr-reviewer-workflow.md).
- Your practice repository has the following labels: `bug`, `enhancement`, `question`, and `documentation`. (GitHub creates the first three by default; you may need to add `documentation` manually.)

## Steps

### Confirm the labels exist

Before creating the workflow, make sure the labels it will apply already exist.

1. Go to your practice repository on GitHub.
2. Click **Issues** → **Labels**.
3. Verify that `bug`, `enhancement`, `question`, and `documentation` are listed.
4. If `documentation` is missing, click **New label**, type `documentation`, choose a colour, and click **Create label**.

### Create the workflow file

In your practice repository, create `.github/workflows/issue-triage.md` with the content below.

<details>
<summary>🖥️ GitHub UI path (recommended)</summary>

1. In your repository, click **Add file** → **Create new file**.
2. Type `.github/workflows/issue-triage.md` as the file name.
3. Paste the content below into the editor.
4. Click **Commit new file**.

</details>

```markdown
---
name: Issue Triage
on:
  issues:
    types: [opened]
permissions:
  issues: write
  contents: read
safe-outputs:
  add-labels:
    limit: 1
---

You are an issue triage assistant. When an issue is opened:

1. Read the issue title and body carefully.
2. Choose exactly one label from this list that best describes the issue:
   - `bug` — something is broken or not working as expected
   - `enhancement` — a request for a new feature or improvement
   - `question` — the author is asking for help or clarification
   - `documentation` — the issue is about docs, guides, or examples
3. Apply that single label to the issue.
4. Do not comment on the issue. Do not apply more than one label.

If the issue is ambiguous, choose the label that best matches the primary intent.
```

> [!TIP]
> The `safe-outputs.add-labels` key tells gh-aw that the only write operation this workflow is allowed to perform is adding labels. The agent cannot delete labels, close issues, or post comments — even if the task brief asked it to.

### Compile the workflow

Open a terminal in your practice repository and compile:

```bash
gh aw compile
```

This generates `.github/workflows/issue-triage.lock.yml`. Commit and push **both** files.

> [!NOTE]
> If you are working entirely in the GitHub UI, push the `.md` file first and let the workflow compile on its next run. You can also rely on the Actions run log to catch compile errors without installing `gh aw` locally.

### Test it

1. Go to **Issues** → **New issue** in your practice repository.
2. Write a title like "App crashes when I click Save" and a one-sentence body.
3. Click **Submit new issue**.
4. Wait about 30 seconds, then refresh the issue. You should see the `bug` label applied automatically.

![Issue with the bug label applied by the triage workflow](images/27-issue-triage-result.png)

Try a second issue with a title like "Add dark mode support" — the workflow should apply `enhancement` this time.

## ✅ Checkpoint

- [ ] Your practice repository has `bug`, `enhancement`, `question`, and `documentation` labels
- [ ] `.github/workflows/issue-triage.md` is committed and pushed
- [ ] `.github/workflows/issue-triage.lock.yml` is committed and pushed (or compiled via Actions)
- [ ] Opening a new issue with a clear title causes the correct label to appear within 60 seconds
- [ ] You can explain what `safe-outputs.add-labels` restricts the agent to doing
- [ ] You opened at least two test issues and both received the expected label

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->
