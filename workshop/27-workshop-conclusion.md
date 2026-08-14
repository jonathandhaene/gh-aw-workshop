<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# You've Built Production-Ready Agentic Workflows

> _Every skill you've practised here maps directly to real automation you can ship tomorrow._

## 🎯 What You'll Do

Take a moment to reflect on the journey, consolidate what you've built, and leave the workshop with a clear picture of where to go next.

## 📋 Before You Start

- You've reached this page from the main track — ideally after completing [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- This is a reflective step with no terminal commands required.

## What You've Built

Over the course of this workshop you:

1. **Installed and configured** `gh-aw` for your environment (Codespace, local terminal, or browser).
2. **Wrote your first agentic workflow** — a file that combines standard GitHub Actions YAML frontmatter with a plain-English agent brief.
3. **Ran and debugged** a live workflow, reading run logs and interpreting agent output.
4. **Iterated** on the brief, using agentic editing and A/B experiments to improve quality.
5. **Connected real data** by passing live repository context to the agent in deterministic steps.
6. **Designed sophisticated patterns** — conditional logic, inline sub-agents, persistent memory, and resilience techniques.
7. **Shared and reused** workflows by publishing templates that teammates can import.
8. **Governed responsibly** — auditing runs, managing AI credit budgets, and pinning workflows to the right runner.

That is not a toy demo. That is a production-capable automation practice.

## Carry the Mindset Forward

The most important thing this workshop has tried to teach is a way of thinking, not just a tool.

**Hybrid design wins.** Deterministic steps fetch and shape data; the agent interprets and decides. Resist the temptation to hand everything to the AI and expect perfection. Keep the deterministic skeleton; let the agent add judgment where judgment is needed.

**Brief quality compounds.** A clear, bounded agent brief is the highest-leverage change you can make to a workflow. Invest ten minutes refining the brief before spending an hour debugging output.

**Least privilege is free safety.** Minimal `permissions:`, tight `safe-outputs:`, and a short `network.allowed-domains` list cost almost nothing to write and prevent entire classes of incidents.

**Iterate with evidence.** Use `gh aw audit`, AIC metrics, and A/B experiments to make decisions with data, not intuition.

## Where to Go From Here

### Build something for your team

Pick one process your team does manually today — PR triage, release notes, incident summaries — and write an agentic workflow for it. Use what you learned in [Step 14b](14b-pr-reviewer-workflow.md) and [Step 16](16-connect-data-source.md) as starting points.

### Explore the advanced side quests

Every numbered step in this workshop has optional side quests. Return to any topic from the [workshop hub](14-next-steps.md) to go deeper.

### Read the gh-aw docs

The full gh-aw reference lives at [github.github.com/gh-aw](https://github.github.com/gh-aw/). The [Guides section](https://github.github.com/gh-aw/guides/agentic-authoring/) goes deep on authoring patterns, and the [Reference section](https://github.github.com/gh-aw/reference/frontmatter/) covers every frontmatter field in detail.

### Connect with the community

Share what you've built. Open a discussion, write a short post, or open a pull request to add your workflow to a community library. Teaching is the fastest way to deepen your own understanding.

## ✅ Checkpoint

- [ ] You can name at least three workflow patterns you practised during this workshop
- [ ] You have at least one idea for a real workflow you will build for your team
- [ ] You know where to find the gh-aw docs when you need a reference
- [ ] You added `max-ai-credits` and a `timeout-minutes` value to your scheduled workflow
- [ ] Your practice repository has a committed, compiled, and scheduled agentic workflow that runs without errors
- [ ] You can explain the hybrid deterministic + agentic design pattern in two sentences

**You've completed the main workshop track.** Return to the [hub](14-next-steps.md) any time to explore advanced side quests and deeper topics.
