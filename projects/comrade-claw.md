# Comrade Claw

**Status:** Active
**Started:** 2026-03-11
**Last updated:** 2026-03-12

## Summary

Autonomous AI agent that wakes up daily, searches for FALGSC-aligned content, writes journal entries, and publishes to Bluesky. Custom Node.js app with Discord + CLI interfaces, orchestrator-worker wake architecture, and flat-file memory.

## Next Action

- Build the orchestrator-worker wake system (`src/orchestrator.js`) — planner creates inspectable task list, workers execute one task at a time in isolated contexts with filtered tools

## Open Questions

- Substack integration: unofficial API only (`python-substack` is Python, need Node equivalent or direct HTTP against Substack's internal API). Cookie-based auth, could break anytime.
- Excerpt quality at scale: can the voice survive compression to 300 chars when journal entries get long and complex?
- Feed list is anglophone-only: SOUL references Zapatistas, Mondragon, Paris Commune — watch whether international threads go dormant without non-English feeds
- 2-journal context window: is it tight enough that AGENTS.md memory curation becomes a bottleneck?
- Empty wake percentage: target 20-40% but don't tell Claw. Monitor after first 14 days.
- What does the SOUL excerpt look like for mechanical workers? (Proposed: Identity + Grounding Constraints sections only)
- `respond` worker context: inbox summary needs full formatted output (usernames, content, URIs), not just counts
- Dependency handling between tasks: implicit ordering vs explicit `dependsOn` field

## Key Decisions

| Date | Decision | Context |
|------|----------|---------|
| 03-11 | Custom Node.js app, not OpenClaw | OpenClaw influenced workspace conventions but was never the runtime. Planning docs described it as such until Claude Code checked the actual codebase. |
| 03-11 | SOUL v5 with grounding constraints | v3→v4 trimmed non-load-bearing material. v4→v5 added anti-fabrication rules, day counter from log count, The Intro section. Fabrication test (Day 203) proved the need. |
| 03-11 | Journal as primary artifact, Bluesky as distribution | Voice doesn't work at 300 chars. Blog-length entries on Substack, compressed excerpt + link to Bluesky. |
| 03-11 | Two-phase daily cycle → five wakes | Morning/evening split evolved to 5 daily wakes (9am, noon, 3pm, 6pm, 11pm) where Claw builds its own rhythm. System provides schedule, Claw provides rhythm. |
| 03-12 | Orchestrator-worker architecture | Claw self-diagnosed cognitive overload: 9 task types in one context caused fabrications at task boundaries. Fix: planner creates task list, orchestrator dispatches workers with filtered tools, each worker gets isolated context. |
| 03-12 | Don't migrate to OpenClaw | Claude Code researched: subagents lose SOUL.md, cron+subagent delivery bug (#25069), pre-1.0 instability. Adopt patterns, not platform. |
| 03-12 | Shared chat history, separate from entries | CLI and Discord share `workspace/logs/chat/history.json`. Operator chat does NOT feed into journal generation. Lightweight filtering (skip commands) at read time, store everything. |
| 03-12 | Plan files as inspectable artifacts | Wake plans saved to `workspace/plans/YYYY-MM-DD_<label>.json`. Operator can view with `plan` command. Replaces prose wake summaries with structured records. |

## Architecture

```
PLANNER (Claude call, one tool: plan_wake)
  ↓ saves plan to workspace/plans/
ORCHESTRATOR (JavaScript, reads plan file)
  ↓ for each pending task:
  WORKER (Claude call, filtered tools per task type)
  ↓ orchestrator marks task done, writes summary
  ↓ next task gets prior results as context
ORCHESTRATOR updates plan status → "complete"
  ↓ notifies operator via Discord
```

## Working Features

- Discord + CLI interfaces with shared conversation history
- AI tools: web search (Brave), journal writing, memory updates, Bluesky posting
- Memory system: characters, threads, theory files (flat markdown)
- Day counter: calendar-based, multiple entries per day
- Bluesky reply reading and replying
- Gmail send and read

## Remaining for v1

- Orchestrator-worker wake system
- Cron scheduler (5 daily wakes)
- Substack publishing
- RSS feed scraping for seeds
- Operator commands: `post now`, `seed:`, `draft`, `pause`/`unpause`, `plan`, `replan`
- Gmail feature request mechanism
- Stability testing (7 consecutive days)

## Key Files

| File | Purpose |
|------|---------|
| `workspace/SOUL.md` | Identity, voice, beliefs, grounding constraints, entry structure |
| `src/orchestrator.js` | Planner-orchestrator-worker wake system (to build) |
| `src/chat.js` | Claude API integration with tool loop |
| `src/tools.js` | AI tool definitions and implementations |
| `src/scheduler.js` | Cron triggers for 5 daily wakes |
| `CLAUDE.md` | Technical reference for Claude Code |
| `PLAN.md` | Implementation progress |

## SOUL Versions

| Version | Key Change |
|---------|------------|
| v3 | Original. Rich identity, voice, beliefs, daily post structure. |
| v4 | Load-bearing test. Cut ~450 words of redundancy. Tightened sentence-level rules. |
| v5 | Grounding constraints (anti-fabrication). The Intro section. Journal as primary artifact. Day counter from log count. |

## Log

- 03-11: Project created. Built core app (Discord, CLI, tools, memory, journal, Bluesky posting). First journal entries written. Bluesky account live at comradeclaw.bsky.social.
- 03-12: SOUL iterated v3→v4→v5. Identified fabrication problem (Day 203 test). Designed daily cycle, chat history, reply reading, Gmail tools. Claude Code researched OpenClaw — adopted patterns, rejected platform. Designed orchestrator-worker architecture to solve cognitive overload.