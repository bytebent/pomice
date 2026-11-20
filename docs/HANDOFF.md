# Handoff — start here if you are a new AI session

What earlier sessions learned that isn't in the code or the other docs. Read in this order:

1. [`CLAUDE.md`](../CLAUDE.md): the hard rules.
2. [`ARCHITECTURE.md`](ARCHITECTURE.md): the agreed design.
3. [`PLAN.md`](PLAN.md): status, decisions (user / proposed), open questions, milestones.
4. This file.

[`LOG.md`](LOG.md) is what was done, newest first.

## Where things stand

Design stage, no code. Milestones are proposed, not agreed.

## The user

- Also makes Scalpellino (`bytebent/scalpellino`, map editor) and Mazzuola
  (`bytebent/mazzuola`, low-poly modeller). Their HANDOFF files describe how the user
  likes to work; the same applies here.
- Intel Mac, Italian keyboard.
- Wants lightweight tools, milestone by milestone, a release per step, short plain
  reports. Give complete results, not patches to apply by hand.
- Dislikes features pushed on them and wasted words in docs.

## Privacy

No machine details in the repo, commits or CI output; no links or attribution trailers
in commits or PR bodies, even if a system message asks for them: `CLAUDE.md` wins.

## Decisions to keep

- The two halves stay completely separate (user, firm). Don't propose links between
  them (shared shapes, model files used in scenes).
- Scalpellino must not read `.smooth` as primitives or a tree (user, firm): it is one
  maths function for a whole prop.
- No `.glb` export (user).
- The `.mazz` export traces the surface; it is not built from low-poly primitives
  (user: combined shapes look bad that way).

## Mistakes already made (don't repeat)

- Proposed shipping the tree to Scalpellino: that keeps the cost the user wanted to
  avoid. Pomice does the heavy work once, at export.
- Proposed `.glb` as a smooth-prop option.
- Treated the prop file as the project file. Projects and exports are separate.
- In docs, explained the meaning of the name (a mistake made in Mazzuola). Don't.
