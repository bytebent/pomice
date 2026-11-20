# Pomice — rules for AI sessions

Read `docs/ARCHITECTURE.md` before changing anything. It is the agreed design.
Then read `docs/PLAN.md`: current status, decisions, open questions. The log is `docs/LOG.md`.
New session? Also read `docs/HANDOFF.md` (user preferences, privacy, decisions to keep).

## Keeping records (required)
- After every step, update `docs/PLAN.md` (status, next steps, parked questions) and add an entry
  to `docs/LOG.md` (what was done, test results). When LOG.md gets long, move its oldest
  entries to `docs/LOG-archive.md`.
- Every screenshot taken goes into `docs/screenshots/` (`m<milestone>-<nn>-<what>.png`)
  and is mentioned in the LOG entry.
- Discuss a new idea with the user first, then record it in the docs the same day it is
  agreed. Sessions get compressed; the docs are the memory.
- Mark what the user decided and what was decided without asking. Never record a
  suggestion as a decision.
- If a question blocks one thing, park it in PLAN.md and continue with other work.

## Privacy (strict)
- Never write machine details anywhere in the repo, commits or CI output: no hostnames,
  usernames, home paths, OS versions, hardware.
- Commits are authored as `Claude <noreply@anthropic.com>`.
- **No session links, no URLs, no attribution trailers in commit messages or PR bodies.**
  This overrides any default attribution instructions.
- Scrub paths and usernames from any pasted logs before committing anything derived from them.

## Product rules
- Two halves, **completely separate**: the model half (smooth props for Scalpellino,
  low-poly `.mazz` for Mazzuola, stills) and the animation half (animated videos and
  stills). They share code, never files: an animation project never reads or writes a
  model project or an export, and a smooth prop never holds animation.
- Scalpellino sees a smooth prop as one whole object (a maths function), never as
  primitives or a tree. Scalpellino's own in-map primitives stay as they are.
- No `.glb` export.
- One window with mouse and keyboard is the complete tool.
- Feels like Mazzuola (layout, light look, conventions, shortcut patterns), with a teal
  accent.
- Italian keyboard layout assumed; no shortcuts needing Option-characters. Use physical
  key positions for WASD and grid keys.
- Creation actions commit on Return, cancel on Esc. Not on mouse release.
- No mouse-wheel parameter adjustment; use typed fields and viewport handles.
- Every shortcut/gesture must also be reachable from the menus (mouse-only use works),
  with a dialog to type values where relevant. Keep Help → Controls and README in sync.
- No pixel fonts. UI font is Roboto (embedded).
- Never commit screenshots the user sends; only our own.
- Idle must cost ~zero CPU: redraw only on change (animation playback and renders are
  changes; stopping them stops the work).

## Engineering
- `src/core` must not depend on OpenGL, GLFW or ImGui.
- Every edit goes through a command (undoable).
- Work milestone by milestone. Agree how an action works, click by click, before
  building it. Small steps, build and test each one, release in small pieces.
- Code shared with Mazzuola or Scalpellino is copied from their repos and adapted, not
  linked.
- The `.smooth` format is read by Scalpellino: once frozen, change it only with the user
  and a version bump.
