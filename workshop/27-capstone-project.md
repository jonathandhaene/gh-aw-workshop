# Capstone: Build a Production-Ready Agentic Workflow

> _Put everything together — design, build, test, and ship one complete agentic workflow that you're proud to run in production._

## 🎯 What You'll Do

You will design a new agentic workflow from scratch, applying the full toolkit you've built across this workshop. By the end, you'll have a working, cost-guarded, observable workflow that solves a real problem in a GitHub repository.

## 📋 Before You Start

- You've completed the main workshop path through [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a practice repository where you can push workflow files.
- Optional: review [What's Next? Keep Exploring](14-next-steps.md) for inspiration.

## Steps

### Choose your mission

Pick a problem your workflow will solve. It should be something a teammate would genuinely benefit from. Some ideas:

- A **weekly health digest** — scans open issues, stale PRs, and failing checks, then posts a summary as a repository discussion.
- A **release notes generator** — triggered on a new tag, reads merged PRs since the last tag, and creates a formatted release draft.
- A **dependency risk reporter** — reads `package.json` or `requirements.txt` and flags packages with known CVEs using a connected MCP data source.
- **Your own idea** — any workflow that reads GitHub data, reasons over it, and writes a useful output.

Write one sentence describing what your workflow will do and who it helps. Keep it visible while you build.

### Draft the frontmatter

Start with the YAML frontmatter. Include:

- A meaningful `name`
- The correct `on:` trigger (schedule, pull_request, push, or manual `workflow_dispatch`)
- `permissions:` scoped to only what your workflow reads and writes
- `max-ai-credits:` and `max-daily-ai-credits:` limits
- `timeout-minutes:` appropriate for your expected runtime

```yaml
---
name: Weekly Repository Health Digest
on:
  schedule:
    - cron: "0 9 * * 1"
permissions:
  issues: read
  pull-requests: read
  discussions: write
max-ai-credits: 800
max-daily-ai-credits: 1600
timeout-minutes: 8
---
```

> [!TIP]
> Start with tighter credit limits than you think you need. You can raise them after a test run confirms real usage.

### Write the agent brief

After the closing `---`, write the Markdown brief. It should:

1. State the goal in one sentence.
2. List exactly what data the agent should collect (be specific — tool calls, filters, date ranges).
3. Describe the format of the expected output.
4. Specify where the output should be written (issue comment, discussion post, PR body, etc.).

Keep your brief under 200 words. Agents perform better with clear, concrete instructions than with long, vague ones.

### Add a safe-outputs declaration

Make sure your frontmatter includes a `safe-outputs:` block that allows only the write operations your workflow actually needs. If your workflow posts a discussion, allow `discussions: create` — nothing more.

### Commit and compile

Save your file to `.github/workflows/<your-workflow-name>.md` in your practice repository.

If you have a terminal available, compile:

```bash
gh aw compile
```

If you're working in the browser UI, commit the file directly. GitHub Actions will attempt to compile it on the next run trigger.

<details>
<summary>🖥️ GitHub UI path</summary>

1. Navigate to your repository on GitHub.
2. Click **Add file** → **Create new file**.
3. Set the path to `.github/workflows/<your-workflow-name>.md`.
4. Paste your frontmatter and brief into the editor.
5. Click **Commit new file**.

</details>

### Run and verify

Trigger a manual run using `workflow_dispatch` or wait for the schedule. Then:

- Open the Actions tab and watch the run complete.
- Read the output. Does it match your one-sentence goal from step one?
- If the agent missed something or wrote more than expected, refine the brief and run again.

Aim for at least one successful end-to-end run before marking this checkpoint complete.

### Apply at least two advanced techniques

Your capstone should demonstrate depth. Apply at least two of the following:

- Conditional logic ([Step 15](15-conditional-logic.md)) — route the agent differently based on data it reads
- A live data source ([Step 16](16-connect-data-source.md)) — fetch external content via a deterministic step
- An MCP tool ([Step 17](17-add-mcp-tools.md)) — give the agent a specialist capability
- Persistent memory ([Step 20](20-persistent-memory.md)) — carry state across runs
- An inline sub-agent ([Step 21](21-inline-sub-agents.md)) — split a complex task
- Error handling ([Step 22](22-error-handling-and-resilience.md)) — make the workflow resilient

Document which two techniques you used inside the agent brief as a comment, so future readers understand the design.

## ✅ Checkpoint

- [ ] You wrote one sentence describing your workflow's goal and audience
- [ ] Your frontmatter includes a trigger, scoped permissions, credit limits, and a timeout
- [ ] Your agent brief is under 200 words and specifies inputs, reasoning, and output format
- [ ] Your `safe-outputs:` block allows only the write operations your workflow needs
- [ ] You triggered at least one successful end-to-end run
- [ ] The output matches your stated goal
- [ ] You applied at least two advanced techniques from the workshop
- [ ] You can explain to a colleague what the workflow does, why it costs what it costs, and how you would debug it if it stopped working

**Congratulations — you've completed the Learning GitHub Agentic Workflows workshop.** 🎉

You now have a working agentic workflow in production and a solid foundation for building, debugging, securing, and scaling agentic automation. Keep the [What's Next? Keep Exploring](14-next-steps.md) hub bookmarked as a reference whenever you return to extend your skills.
