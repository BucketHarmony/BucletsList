# Majordomo Event Bus

**Status:** Paused
**Started:** 2026-02-28
**Last updated:** 2026-03-11

## Summary

A unified event bus acting as the nervous system for a local multi-agent platform — four AI agents (one cloud, three local Ollama) coordinated through a single SQLite-backed event stream with watcher triggers, memory curation, and a web dashboard.

## Next Action

- Get the bus container running again and verify the full stack (bus + n8n + Ollama + Qdrant) is healthy

## Open Questions

- Should the source code be put under version control? (.gitignore exists but no repo initialized)
- Is the artifact system v3 ready to ship, or does it need more iteration?
- Are the 3 failing SystemAnomalyTrigger tests worth fixing or should the tests be updated to reflect the disabled-by-default state?
- Should bus source be volume-mounted for faster dev iteration instead of baking into the Docker image?

## Key Decisions

| Date | Decision | Context |
|------|----------|---------|
| 02-28 | No sessions — single continuous timeline | Rolling chat keyed by timestamp + parent_id chains |
| 02-28 | Haiku in bus, Sonnet in n8n | Bus handles lightweight LLM tasks (memory curation); heavy orchestration stays in n8n |
| 02-28 | SQLite only, no cloud deps except Anthropic API | Local-first design for a single-machine setup |
| 03-04 | Multi-agent architecture with wake queue | Serializes Ollama calls to prevent GPU contention and tool-call corruption |
| 03-05 | qwen3.5:35b-a3b as local model | 35B total / 3B active MoE — best balance of speed and tool-calling reliability |
| 03-06 | Disable SystemAnomalyTrigger by default | LLM inference causes natural CPU/GPU spikes that trigger false alarms |
| 03-07 | Observer pattern for Vice agent | Passive critic sees all agent traffic without participating in chains |

## Log

- 02-28: Project created, requirements finalized
- 03-01: Phase 1 MVP build begins — event store, watcher, dashboard scaffolding
- 03-03: Dockerized, Discord bot added, agent bootstrap prompts written
- 03-04: Multi-agent design doc, Koda agent onboarded
- 03-05: Agent registry, artifact system, chat store, inter-agent messaging
- 03-06: System anomaly trigger added then disabled, config routes
- 03-07: Dashboard chat views, agent router with wake routing, observer pattern (Vice)
- 03-08: Wake queue webhook notifier hardened (timeout alignment, queue serialization)
- 03-10: Lessons learned captured
- 03-11: Memory system documentation updated
