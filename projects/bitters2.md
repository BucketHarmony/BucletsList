# Bitters 2

**Status:** Active
**Started:** 2026-03-13
**Last updated:** 2026-03-14

## Summary

Comedic robot content system: a Petoi Bittle robot dog (Bitters) runs autonomous tick cycles — observe, act, log — orchestrated by Claude Code, with a storyboard dashboard for review.

## Next Action

- Run a live tick on hardware to validate multi-frame capture (frame_NNNN_SS.jpg) and filmstrip display end-to-end

## Open Questions

- walk_backward serial command: is kbk correct or does hardware need kwkL? (TODO marker in config.py)
- Video pipeline: how will raw .h264 segments get transcoded and surfaced in the dashboard?
- Deployment: will the dashboard run on the Pi alongside the bridge, or on a separate machine?

## Key Decisions

| Date | Decision | Context |
|------|----------|---------|
| 2026-03-13 | Flask single-threaded (threaded=False) for bridge | Serial port is not thread-safe; one request at a time matches hardware constraint |

## Log

- 2026-03-13: Project created. Phase 1 (Intake): bridge service, mock bridge, CLAUDE.md persona prompt
- 2026-03-14: Phase 2 (Dashboard): storyboard web UI, SQLite, tick API, tick parser, CLI importer
- 2026-03-14: Multi-frame support: updated capture instructions, watcher, API, importer, filmstrip UI
