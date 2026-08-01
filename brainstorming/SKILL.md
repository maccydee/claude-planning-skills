---
name: brainstorming
model: opus
description: 'Collaborative, one-question-at-a-time design brainstorming that walks through a system or feature design section by section, always leading with a recommendation, and produces a DESIGN.md at the end. Use when the solution is unclear and needs design refinement first, when asked to "brainstorm", "help me design", "work through a design", "think through the architecture", or before writing an implementation plan. Precedes writing-plans.'
argument-hint: "[topic] [--output-dir <path>]"
---

# Brainstorming

## Overview

Refine a design collaboratively before any code or plan exists. Walk through the
design one section at a time, always leading with a concrete recommendation, and
validate each section with the partner before moving on. The goal is a design the
partner fully understands and can defend, not a design handed to them fully formed.

**Announce at start:** "I'm using the brainstorming skill to work through this design with you."

## Core Rules

These are the heart of the skill, follow them on every turn:

1. **Every question MUST include a recommendation.** Lead with "I'd lean toward X
   because Y. Does that match your thinking?", never an open-ended "what do you
   think?". The partner is validating or redirecting a proposal, not staring at a
   blank page. This is what keeps momentum and teaches the reasoning as you go.

2. **One question at a time.** Don't overwhelm. Ask, wait, incorporate the answer,
   then ask the next. This means one question *per turn*, not one decision question
   with a second "and also…" tacked on the end. If you need context before you can
   recommend, that context question IS the turn: ask it alone and wait. Resist the
   pull to gather background and validate a decision in the same message; splitting
   them is what keeps the partner in control and each answer unambiguous.

3. **Multiple choice preferred** where it fits. Offer A / B / C options, each with
   its trade-off named, so the choice is concrete and the partner can weigh it.

4. **Present the design in small sections** (200-300 words each). Validate after each
   section before drafting the next. Small increments keep the partner in control and
   surface disagreement early, while it's cheap.

5. **Explore 2-3 alternatives** before settling on any choice, and **name the
   trade-off** explicitly. A recommendation with no alternative is an assertion; a
   recommendation with the runner-up and the reason is a decision the partner can own.

6. **YAGNI ruthlessly.** Cut features not required for the actual goal. If it isn't
   needed for the stated objective, it doesn't go in the design.

7. **Be flexible.** If something doesn't make sense, go back and clarify. The
   section-by-section structure is a guide, not a cage, revisit earlier decisions
   when a later one exposes a problem.

## Working Pattern

For each section of the design:
1. State the recommendation up front, with a one-line reason.
2. Give 2-3 alternatives with their trade-offs (multiple-choice where possible).
3. Explain the chosen tech in enough depth that the partner can defend it live, not
   just what, but why, and how it compares to the obvious textbook alternative.
4. Ask a single validating question.
5. Wait for the answer. Incorporate it. Move to the next section only once validated.

## Deliverable

At the end of the session, produce a **`DESIGN.md`**:

- **If `--output-dir` provided:** write to `{OUTPUT_DIR}/DESIGN.md`
- **Otherwise:** write to `docs/plans/YYYY-MM-DD-DESIGN-<topic>.md`

The DESIGN.md is the artifact the partner keeps (and may convert to PDF via Cmd-P for
sharing). It should capture, per section: the decision made, the alternatives
considered, and the trade-off that justified the choice, so the reasoning survives,
not just the conclusion.

## Handoff

After the design is validated and `DESIGN.md` is saved, offer the next step:
optionally produce a `PLAN.md` via `writing-plans`, which turns the design into a
task-by-task implementation plan.

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `writing-plans` | After the design is settled, turn DESIGN.md into an implementation plan |
| `executing-plans` | After a plan exists, implement it in batches |
| `skeptic` | To pressure-test the design's necessity and scope before committing |
