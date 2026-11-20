# Pomice — Working Plan

**Purpose:** let any future session (human or AI) pick up work without outside context.
Update this file at the end of every step: status, what's next, open questions. The
step-by-step log is in [`LOG.md`](LOG.md) (newest entries at the top).

Design: [`ARCHITECTURE.md`](ARCHITECTURE.md). Rules: [`../CLAUDE.md`](../CLAUDE.md).

---

## Current status

- **Design stage.** No code yet. The idea was discussed with the user on 2026-11-20 and
  written down here and in ARCHITECTURE.
- **Next:** agree the milestones with the user (proposal below), then agree milestone 1
  click by click before any code.

## Decided with the user (2026-11-20)

- Pomice is a third app beside Scalpellino and Mazzuola, for smooth shapes made of
  maths primitives. Uses the Bottega.
- It does two things: **smooth props for Scalpellino** and **animated videos**. The two
  halves are **completely separate**: shared code, no shared files.
- Projects are saved (each half its own project file, **two separate extensions**);
  props, images and videos are exports. Animation is never saved in the prop file.
- **No `.glb` export.**
- A smooth prop is a **maths function** for the whole shape: Scalpellino sees one
  frozen prop, not primitives or a tree. Scalpellino's in-map primitives stay,
  editable as today; Pomice is for the more complex ones.
- Space: cm, Z up, origin = pivot (as Mazzuola and Scalpellino).
- Shapes: Scalpellino's seven primitives, more kinds later; groups inside groups with
  their own operation and blend.
- Modifiers: mirror, shell, round/inflate, twist, bend, repeat, noise displacement.
- Colour: full colours and materials, no textures. Materials wait until Scalpellino
  has its own.
- Animation for now: keyframes on any value, Euler rotation, linear / ease / hold,
  only values change over time (not the tree).
- **Low-poly export to `.mazz`:** traces the whole surface (not built from low-poly
  primitives), spreads faces well, fixes problems automatically; intentional holes
  (torus, cut-throughs) stay. Refined further in Mazzuola.
- Layout: Mazzuola's. Look: Mazzuola's light theme with a **teal** accent.
- Code copied from the sibling repos and adapted, not linked.

## Proposed, not confirmed

- Extensions: `.pomice` (model project), `.pomanim` (animation project), `.smooth`
  (prop export).
- Stack as Mazzuola: GLFW + OpenGL 4.1 core + Dear ImGui (docking) with its own
  backends, CMake, same CI and release flow.
- Teal accent values (Mazzuola's `theme.hpp` style):
  `kAccent{0.10, 0.55, 0.55}`, hover `{0.18, 0.64, 0.63}`, active `{0.06, 0.44, 0.45}`,
  soft = accent at alpha 0.22 / 0.32.
- `.smooth` written as plain arithmetic (Pomice expands primitives and modifiers), so
  Scalpellino's reader never changes when Pomice grows.
- How Scalpellino draws smooth props (ARCHITECTURE §5): belongs in Scalpellino's docs
  once agreed there.
- A tree panel; subtracted pieces drawn as wireframe ghosts.
- Low-poly export controls: detail / target faces, sharp-edge angle, prefer quads,
  wireframe preview.
- Animation later: drivers (loop, ping-pong, wiggle, sine), curve editor.
- Outputs: PNG sequence first, then MP4 (macOS encoder) and GIF.
- Heavy work (exports, conversion, renders) on background threads.

## Open questions

- Render style of stills and videos (Scalpellino's Source / Y2K look, or Pomice's own).
- What a material holds (waits for Scalpellino's materials).

## Milestones (proposal, to agree with the user)

1. Shared core: distance functions, the tree, the formula compiler; the model half's
   editor (Mazzuola layout, four views, tree panel) with save and open.
2. `.smooth` export; the format spec frozen; smooth props in Scalpellino (its own
   milestone, planned in its docs).
3. `.mazz` low-poly export.
4. Animation project: camera, lights, still render.
5. Keyframes, timeline, PNG sequence.
6. Drivers; video and GIF export.

## Bottega

Shared asset home, built in Scalpellino first (see Scalpellino's PLAN). Pomice writes
`.smooth` files into its `models/` folder.
