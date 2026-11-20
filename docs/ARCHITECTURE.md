# Pomice — Architecture & Design

A small modeller for smooth shapes built from maths primitives (distance functions), in
two separate halves:

- **Model half:** builds static shapes and exports them as **smooth props** for
  Scalpellino (`.smooth`), as **low-poly models** for Mazzuola (`.mazz`), and as stills.
- **Animation half:** builds animated scenes and renders them as **videos** and stills.

Third tool next to Scalpellino (map editor) and Mazzuola (low-poly modeller); shares
their Bottega. Personal tool. Not distributed.

Status of each point (user decision, proposal, open) is tracked in [`PLAN.md`](PLAN.md).

---

## 1. Guiding rules

1. **Two halves, completely separate.** They share code (core, editing tools, views,
   renderer), never files. A smooth prop never holds animation; an animation project
   never uses a model project or an export.
2. **Projects are the truth; exports are one way.** Each half saves its own project
   file. Exports leave the app and are never opened again by Pomice.
3. **Scalpellino gets a result, not a recipe.** A smooth prop is one finished maths
   function. Scalpellino never sees pieces, groups or operations.
4. **Idle = zero work.** Redraw only on change. Playback and rendering run only while
   asked for.
5. **The core has no graphics.** Distance functions, the shape tree, the formula
   compiler, conversion to low poly, keyframes, file IO and undo live in a plain C++
   library, tested headless on CI.
6. **Everything is undoable.**
7. **Feels like Mazzuola.** Same layout, light look, shortcut patterns, typed dialogs,
   Return/Esc habits.

## 2. Platform

As Mazzuola: macOS, Intel first, Apple Silicon built on demand; Linux builds for CI
tests. Italian keyboard layout assumed. One window, mouse and keyboard.

## 3. Space and units

1 unit = 1 cm, Z up, the model's origin is its pivot: the same model space as Mazzuola
and Scalpellino, so a smooth prop lands at the right size with no conversion. 175 cm
scale figure, as in the other two.

## 4. Shapes

### Primitives
Scalpellino's seven: sphere, cube (rounded corners), cylinder, cone, torus, capsule,
disc. Each is a unit shape placed by position, rotation (degrees about X, then Y, then
Z) and size per axis, as in Scalpellino. More kinds later (ideas: ellipsoid, rounded
cylinder, hex prism, wedge).

### The tree
- Pieces and **groups inside groups**. Every piece and group has an operation
  (add / subtract / intersect) and a blend (units; 0 = sharp) for how it joins its
  siblings.
- Colour per piece; where pieces blend, their colours blend across the seam.

### Modifiers
Mirror, shell (hollow), round / inflate, twist, bend, repeat (line, circle), noise
displacement. On a piece or a group.

### Colour and materials
Full colours and materials; no textures. What a material holds is decided once
Scalpellino has materials.

## 5. Model half

### Project
The model project file (extension to confirm, §8) holds the tree and editor state
(names, hidden / locked pieces, view settings).

### Export: smooth prop for Scalpellino (`.smooth`)
- **One maths function for the shape** (point → distance to the surface, negative
  inside) and **one for colour and material** (point → colour, material), plus the
  bounding box. Written as plain arithmetic (`length`, `min`, `max`, `abs`, `sqrt`,
  `sin`, numbers...): primitives, blends and modifiers are expanded on export. Hidden
  pieces are left out.
- Because the vocabulary is plain arithmetic, Pomice can add primitives and modifiers
  without Scalpellino needing changes, and the format can be frozen early and versioned.
- Pomice may optimise on export: simplify the arithmetic, add "far from this region:
  skip it" checks. These are just more arithmetic.
- **What Scalpellino does with it** (to be agreed in Scalpellino's docs): a new prop
  type; the inside never changes, so the function is compiled to one shader per model
  once at load and every copy is drawn from it (one matrix per copy); precomputed once:
  tight bounds, a coarse distance grid used only to skip empty space, a rough mesh for
  the 2D views, picking and Snap to Land. Move, turn and scale only the whole prop;
  uneven scale uses a safe, slower distance estimate (as for stretched primitives).
  Scalpellino's own in-map primitives and combinations stay as they are.

### Export: low-poly model for Mazzuola (`.mazz`)
Traces the whole surface (no building from low-poly primitives: combined shapes come
out badly that way). The result is refined further in Mazzuola.
1. Trace finely, keeping sharp edges sharp.
2. Reduce to the target: more faces where the shape bends, fewer where it is flat;
   points are pulled back onto the exact surface after every step.
3. Tidy: even, well-shaped faces, edges following features and curvature.
4. Merge flat neighbours into quads (Mazzuola has triangles and quads only).
5. Colour per face from the colour function; snap to Mazzuola's clean numbers; recheck.

Problems fixed automatically: gaps in the surface, edges shared by more than two faces,
tiny loose specks, paper-thin bits, flipped and zero-area faces, faces cutting through
each other. A model with mirror is built half and mirrored, so it stays exactly
symmetric. Holes that are part of the shape (a torus, a cut-through window) always
stay; a hole too small for the detail level is kept with extra faces or listed, never
closed silently. Anything not fixed is listed.

Controls: detail / target faces, sharp-edge angle, prefer quads; a wireframe preview
over the smooth model before exporting.

### Export: stills
Images of the model.

## 6. Animation half

- **Project** (extension to confirm, §8): its own shapes (same tree, primitives,
  modifiers and colours as the model half), camera, lights, background, frame rate,
  length in frames, render settings.
- **Keyframes on any number**: position, rotation, size, blend, colour, modifier values,
  camera and light values. Keys sit on frames.
- **Rotation in Euler degrees**, so 0° → 720° is two full turns.
- **Interpolation per key:** linear, ease in/out, hold.
- **Only values change over time,** not the tree: a piece appearing is its size or
  blend animated from 0. (Agreed for now.)
- Groups move what is inside them, so orbits and stacked motion need nothing special.
- Later: drivers on any value (loop, ping-pong, wiggle, sine); a curve editor.
- Output: stills and frames, then video.

## 7. Rendering

- The views ray-march the distance function per pixel. The tree is compiled into
  shader code (not an interpreter over arrays of pieces); in the animation half,
  animated values become shader inputs and everything else stays constant.
- Render style of stills and videos: **open**.
- Resolution, anti-aliasing and outputs (PNG sequence first, then MP4 and GIF) are
  proposals.

## 8. Files

| File | What | Read by |
|---|---|---|
| model project (`.pomice`, to confirm) | tree + editor state | Pomice |
| animation project (`.pomanim`, to confirm) | shapes, keys, camera, lights, render settings | Pomice |
| `.smooth` (to confirm) | shape and colour/material functions, bounds, format version | Scalpellino |
| `.mazz` | low-poly model, Mazzuola's format | Mazzuola, Scalpellino |
| images, video | renders | — |

Two project extensions (user). Smooth props go in the Bottega's `models/` folder.

## 9. Look and layout

Mazzuola's layout (four views: 3D, Top, Front, Right; tool bar on the left; panels on
the right; status bar) and Mazzuola's light theme with a **teal** accent for UI chrome.
A panel listing the tree is needed: subtracted pieces can't be clicked in 3D.
