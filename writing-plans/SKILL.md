---
name: writing-plans
model: opus
description: 'Write an implementation plan (PLAN-*.md) that a zero-context executor can follow to completion: exact file paths, complete code, test-first cycles, and verification steps. Use when asked to "write a plan", "make a plan", "draft a plan", or to turn a design into an implementation plan. Follows brainstorming; precedes executing-plans.'
argument-hint: "[feature] [--output-dir <path>]"
---

# Writing Plans

## What a plan is for

A plan is documentation for whoever implements it, and that implementer has zero context for this codebase. It might be a fresh session, a teammate, or you in a month. So everything they need goes on the page: which files to touch, the actual code, how to test it, and how to know it worked. If the plan says "add validation" instead of showing the validation, the plan has failed, because the executor now has to guess, and guessing is where plans go wrong.

Assume the executor is a capable developer who knows almost nothing about this project's tools, conventions, or domain, and isn't a strong test designer. Write for that person. Spell out the boundaries; stay quiet about internals they can figure out.

The habits that keep plans executable: DRY, YAGNI, test-first, small tasks, frequent commits.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

Think the approach through before you write, including the alternatives and edge cases, so the plan reflects a considered design rather than the first idea.

## Where the plan is saved

`--output-dir <path>` (parse with `--output-dir\s+(\S+)`):
- **Provided:** create the directory if missing, write to `{OUTPUT_DIR}/PLAN.md`.
- **Standalone:** write to `docs/plans/YYYY-MM-DD-PLAN-<feature>.md`.

## Task size

Each step is one action, roughly 2-5 minutes. That granularity is what lets an executor make steady, verifiable progress instead of taking a large leap and landing somewhere wrong. A typical test-first task breaks down as:

- Write the failing test
- Run it, confirm it fails for the right reason
- Write the minimal code to pass
- Run it, confirm it passes
- Commit

## Plan header

Every plan opens with this, so the executor knows the goal and can see progress at a glance:

```markdown
# [Feature] Implementation Plan

> **For the executor:** implement this task-by-task with the executing-plans skill.

**Goal:** [one sentence: what this builds]
**Architecture:** [2-3 sentences: the approach]
**Tech Stack:** [key technologies/libraries]

## Progress

| Status | Count |
|--------|-------|
| 🔴 NOT_STARTED | N |
| 🟡 IN_PROGRESS | 0 |
| 🟢 COMPLETED | 0 |
| ⚪ BLOCKED | 0 |
```

## Progress markers

Each task carries its status inline (updated in place, not tracked in a separate section):

| Emoji | Status | Meaning |
|-------|--------|---------|
| 🔴 | NOT_STARTED | Not yet claimed |
| 🟡 | IN_PROGRESS | Being implemented |
| 🟢 | COMPLETED | Verified and committed |
| ⚪ | BLOCKED | Can't proceed (dependency or issue) |

Keep the header's Progress counts in sync with the task totals.

## Task structure

```markdown
### Task N: [Component] 🔴 NOT_STARTED

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Behavioral contract:**
| Input | Expected output |
|-------|-----------------|
| `<value>` | `<concrete result, or error containing "phrase">` |

**Step 1: Write the failing test (RED)**
```python
def test_specific_behavior():
    assert function(input) == expected
```

**Step 2: Run it, confirm it fails (verify RED)**
Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL ("function not defined"). If it passes or errors, fix the test before moving on.

**Step 3: Minimal implementation (GREEN)**
```python
def function(input):
    return expected
```
Only enough to pass. No extra features.

**Step 4: Run it, confirm it passes (verify GREEN)**
Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS, no warnings.

**Step 5: Commit**
```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific behavior"
```

**Step 6: Refactor if needed**, remove duplication, improve names, keep tests green.
```

The test-first cycle (RED → verify RED → GREEN → verify GREEN → refactor) is the spine of every code task. Writing the test first is what forces the behavioral contract to be concrete before any implementation exists to rationalize.

## Behavioral contracts must be concrete

State behavior as `(input, expected output)` pairs, not prose. Prose like "handles invalid input gracefully" isn't verifiable; `(input: "", expected: error containing "required")` is. Every task's contract table covers the boundaries: empty input, invalid input, not-found, and the happy path.

## Mock topology (for tasks mocking 3+ external dependencies)

Executor difficulty tracks mock surface area far more than business-logic complexity, so when a task mocks three or more external dependencies, map the test boundary explicitly:

| Dependency | Mock entry point | Method chain | Side effects |
|------------|------------------|--------------|--------------|
| `pulsar.Client` | `mock_client` | `Client → subscribe → consumer` | `create_producer.side_effect = [prod, dlq]` |

## Operational logging (for service components)

When a task creates an HTTP/gRPC server, a consumer/producer, or a background worker, specify its logging so observability isn't silently dropped:
- Lifecycle: startup, ready, shutdown, shutdown-complete (with ports, counts, addresses)
- Error wrapping: exported functions wrap errors with call-site context (e.g. `fmt.Errorf("loading config: %w", err)`)
- State transitions: reload success/failure with before/after counts, circuit-breaker changes

## Language-specific rules

If **Tech Stack** contains Go (`\bgo\b`/`\bgolang\b`), read `${CLAUDE_SKILL_DIR}/patterns/execution-context-go.md` and embed it as a `**Language Rules:**` block in every task.

If it contains Python (`\bpython\b`/`\bpy\b`), do the same with `${CLAUDE_SKILL_DIR}/patterns/execution-context-python.md`.

## Production readiness (for service projects)

If the tech stack or description mentions a service (`API`, `server`, `HTTP`, `gRPC`, `worker`, `consumer`, `producer`), read `${CLAUDE_SKILL_DIR}/patterns/production-readiness-checklist.md` and give each of its six domains (Observability, Health & Lifecycle, Resilience, Security, Configuration, Deployment) a disposition: covered by task N, a new task added, or explicitly N/A. Don't skip a domain silently, a silent gap reads as "handled" when it wasn't. For a CLI, library, or script, skip this section.

## Operational fidelity annotations

When you write code examples in a plan, mark intent an executor would otherwise "clean away", because non-annotated complexity is invisible, and invisible complexity gets simplified out:

- **`(MEASURED, what)`** on any field whose name implies measurement (`*_ms`, `duration`, `elapsed`, `count`, `latency`). A hardcoded zero passes the type checker and every test while quietly destroying the metric.
- **`(WHY: ...)`** on a pattern more complex than the obvious alternative, so it survives an executor optimizing for "clean code" (e.g. a lazy import for an optional dependency, a fail-fast guard).
- **`[RFC NNNN §N.N]`** when a snippet touches an external protocol (HTTP headers, tokens, TLS, encoding), citing any compliance point that diverges from a naive reading (e.g. header lookups are case-insensitive).

## Requirement wording

When you write requirements with modal verbs (MUST/SHOULD/MAY), frame them positively, state what to do rather than what to avoid. Positive requirements are easier to verify and harder to misread.

## Planning assumptions (ASSUMPTIONS.md)

If any decision was made where the requirements were silent or contradictory, record it in `ASSUMPTIONS.md` next to the plan, one entry per assumption: what was assumed, what the alternatives were, and why this choice. This is what lets a reviewer catch a wrong assumption before it's built rather than after.

```markdown
## A-001 | gap-fill | OPEN
Requirements don't specify config format. Assumed YAML, 3 of 4 existing config files use it.
Alternatives: TOML, JSON, env-only.
```

If the requirements fully dictate every decision, skip the file, don't create it for its own sake.

## Before you finish

- Exact file paths, every time.
- Complete code in the plan, not "add validation."
- Exact commands with their expected output.
- All tasks start 🔴 NOT_STARTED, and the Progress counts match the task total.
- Batch independent tool calls (read all the files you need in one message, then make the edits), sequential round-trips waste time when the calls don't depend on each other.

## Next step

Once the plan is saved, ask what to do next with AskUserQuestion:
- **Execute now**, load executing-plans and implement it task-by-task in this session.
- **Fresh session**, open a new session and paste the plan path; a clean context often executes more reliably.
- **Stop here**, the plan is saved.

## Related skills

| Skill | When |
|-------|------|
| `brainstorming` | Before this, when the design isn't settled yet |
| `executing-plans` | After this, to implement the plan in reviewed batches |
| `skeptic` | To pressure-test the plan's scope before building it |
