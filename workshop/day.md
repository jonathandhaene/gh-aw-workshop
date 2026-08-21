# One-Day Workshop Program

A full-day hands-on experience with GitHub Agentic Workflows — from your first workflow to a live hackathon project.

## Program

| Time | Session |
|------|---------|
| 09:30 | **Welcome and setup** — introductions, environment check, and a tour of what you'll build today |
| 10:00 | **Workshop — Part 1** — build your first agentic workflow: write, compile, run, and iterate ([Start here →](00-welcome.md)) |
| 11:30 | **Share and reflect** — each participant shows their workflow; group discussion on what surprised you |
| 12:00 | **Lunch** |
| 13:00 | **Deep dive — Agenticism** — how agentic workflows shift responsibility from shell scripts to AI reasoning; architecture, safety model, and cost model ([Agentic Workflows Deep Dive →](side-quest-05-02-aw-deep-dive.md)) |
| 13:30 | **Workshop — Part 2** — go deeper: conditional logic, live data sources, MCP tools, memory, and sub-agents ([Continue →](14-next-steps.md)) |
| 14:30 | **Share and reflect** — demos of Part 2 workflows; group discussion on patterns and tradeoffs |
| 15:00 | **Hackathon** — build something real: pick a workflow idea from your own team or repo and ship it |
| 16:15 | **Share and reflect** — each group demos their hackathon project and explains one decision they made |
| 16:30 | **Social time** |

## 📋 Before You Arrive

Make sure you have completed the [prerequisites](01-prerequisites.md) before the day starts. The morning session moves fast, and environment issues will slow you down.

Run this quick check in your Codespace or terminal to confirm your environment is ready:

```bash
gh --version && gh aw --version
```

Both commands should print a version number. If either fails, work through [Step 6 — Install gh-aw](06-install-gh-aw.md) before arriving.

## ✅ Checkpoint

Use this checklist at key points during the day to confirm you're on track:

- [ ] Environment check passed: `gh --version` and `gh aw --version` both return a version number
- [ ] Part 1 complete: your first agentic workflow compiles and runs successfully in GitHub Actions
- [ ] Part 2 started: you have extended your workflow with at least one new capability (conditional logic, live data, MCP tool, or memory)
- [ ] Hackathon: you have identified a real workflow idea from your own team or repo
- [ ] Hackathon: your team's workflow runs end-to-end at least once before the demo

## Facilitation Notes

**Part 1** covers [Steps 0–12](README.md#curriculum--part-1-build-your-first-workflow): welcome, prerequisites, environment setup, Actions intro, the agentic workflow model, installing `gh-aw`, writing your first workflow, running it, and iterating.

**Part 2** is self-directed. Participants choose their own path through [Steps 14+](README.md#curriculum--part-2-go-deeper) based on what interests them most. Facilitators circulate and help unblock.

**Hackathon** has no prescribed outcome — the goal is one working workflow that solves a real problem. Teams of two or three work well.
