---
name: executing-plans
model: opus
description: Use when handed a complete implementation plan (PLAN.md) to build. Loads the plan, reviews it critically, implements it in small reviewed batches with a test-first cycle, and reports between batches so an architect can catch drift early. Trigger on "execute the plan", "implement this plan", "run the plan", or "start building from this plan". Follows writing-plans.
---

# Executing Plans

## Overview

Take a finished plan and build it, in small batches with a review checkpoint after each one. The batching is the point: a checkpoint every few tasks lets a reviewer catch a wrong turn while it's cheap to fix, instead of after twenty tasks have been built on top of it.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## Commits

By default, hold commits until a batch has been reviewed, so the full diff is available to review. The reviewer gives feedback, then the approved work is committed. If you'd rather commit per task (useful for unattended runs), pass `--auto-commit`.

## The process

### 1. Load and review the plan

Read the plan and review it critically before touching code. If it has a blocking gap or ambiguity that would force you to guess, raise it with the partner first, guessing here is how execution silently diverges from intent. If it's clear, make a task list and start the first batch in the same response.

### 2. Execute a batch

Default to three tasks per batch. For each task:

1. Mark it in progress.
2. Follow the plan's steps exactly, they're already bite-sized.
3. For code, use the test-first cycle: write the test, watch it fail for the right reason, write the minimal code, watch it pass, refactor if needed.
4. Run the verifications the task specifies.
5. Prove the output is real: run the feature with representative input and confirm it produces actual computed data, not an empty or hardcoded value. A task that "passes" but returns nothing isn't done.
6. Mark it complete and update its marker in the plan.

### 3. Report

Before reporting, if earlier batches exist, re-run their tests as a regression check. If something now fails, run it once more to rule out flakiness, then surface it, don't quietly fix it; the reviewer decides what to do at the checkpoint.

Then report the batch:
- What you built.
- The verification output.
- The output-substance proof (real input, real output) for each task.
- "Ready for feedback."

### 4. Continue

Apply the feedback, run the next batch, repeat until every task is done.

### 5. Finish

When all tasks are complete and verified, say so, and recommend a review before merging: run the skeptic over what got built (and a code/security pass if you have one), address anything real, then review and merge. Don't merge straight off the last green test.

## Logging: assumptions and blockers

Keep one execution log, `EXECUTION-LOG.md`, next to the plan. Two kinds of entry:

**Assumption**, you hit a gap or a contradiction in the plan, made a call, and kept going. Log it *before* proceeding, so it can be reviewed rather than discovered later. The pre-decision state is the useful part.

```markdown
## A-003 | Task 4 | assumption | OPEN
Plan doesn't specify config format. Assumed YAML, matching the existing codebase.
(gap-fill, plan was silent)

## A-007 | Task 6 | assumption | OPEN
Plan says validate JWT on every request, but the contract schema has no auth field.
Validated at the handler layer and flagged. (conflict, two directives disagreed)
```

**Blocked**, a task genuinely can't proceed (missing dependency, impossible requirement, a failure with no viable fix). Log it, mark the task ⚪ BLOCKED in the plan, and stop. Don't invent your way past a blocker.

```markdown
## B-001 | Task 3 | blocked | OPEN
Can't proceed: `db.Connect()` needs package `internal/pgpool`, which doesn't exist yet.
Root cause: plan assumes it exists; nothing created it. Fix: add a task to create it before Task 3.
```

## Progress tracking

Update the plan as you go, it's the source of truth, and a stale marker sends the next executor to the wrong place. Move markers in place (🔴 → 🟡 → 🟢, or ⚪ when blocked), and write progress notes that are specific and evidence-based:

| Good | Weak |
|------|------|
| "Added JWT validation in `auth/handler.go:45-67`" | "Made progress" |
| "Implemented rate limiting, 100/min" | "Working on it" |

When a batch is done, make sure the markers match git state before reporting.

## When to stop and ask

Stop and ask the moment you hit a real blocker, a critical gap, an unclear instruction, or a verification that keeps failing. A quick question beats a confident wrong guess.

## When to revisit the plan

Go back to review (step 1) if the partner updates the plan, or if execution shows the fundamental approach needs rethinking.

## Quick reference

- Review the plan critically first.
- Test-first for all code changes.
- Follow the plan's steps exactly.
- Update the plan after each task.
- Report and wait for feedback between batches.
- Stop and ask when blocked.

## Related skills

| Skill | When |
|-------|------|
| `writing-plans` | If the plan needs revision or new tasks appear |
| `skeptic` | Before building, and again before merging, to challenge scope and necessity |
