# ykwtd — design

**ykwtd = "you know what to do".** The acronym is opaque to un-indexed models, so the
full expansion is carried in the SKILL.md frontmatter, H1, and first body line — never
rely on the four letters alone.

## Problem
Intent-inference is the spine of an agentic system, but it is usually *distributed*
across many skills — and all of those assume a task *exists* and infer intent **within**
it. The unserved gap: **zero-instruction input** — a paste/log/email/screenshot/
half-thought with no imperative and no explicit ask, where the agent must first answer
"what is even being asked?" before anything else fires.

## Decisions
- **Positioning:** standalone skill — a *router*, not an executor. Whatever owned the
  "drop a blob, infer the task" behaviour before becomes a *consumer* that invokes ykwtd.
- **Trigger:** both explicit (`/ykwtd`, "you know what to do", a silent blob) and reflex
  (no-imperative input with no other claimant).
- **Act vs gate:** tiered — reversible/internal acts immediately; irreversible/outward
  proposes-and-waits.
- **Confidence surface:** always, lightweight — a 3-line `read / not / move` block.
- **Naming:** spell out "you know what to do" in full wherever the name appears.

## Core loop
1. **Classify the drop** (PR comment / log / email / chat / screenshot / spec / nudge) —
   the kind narrows plausible asks.
2. **Infer intent + confidence** — always-on 3-line block; the `Not:` runner-up line is
   the anti-confabulation catch.
3. **Tiered gate** — reversible→act, irreversible/outward→propose-and-wait. Boundary is
   reversibility+reach, not effort.
4. **Low-confidence escape hatch** — output the ambiguity ("two reads, which?"), never a
   confident guess. No padding to a third read.

Then **hand off** to the real task's skill. ykwtd is a router, not an executor.

## Boundary
Fires only when input has **no imperative AND no explicit ask**. Instruction-shaped
requests stay on the normal flow. This is what stops it firing on everything and keeps
it distinct from neighbours that decide *within* a known task (e.g. a guided-decision
skill) or that name a pattern for an already-known situation.

## Anti-confabulation
The whole point. The failure mode of "you know what to do" is confidently doing the
wrong thing because no instruction constrained it — `know-what-you-dont-know` applied to
the task itself. The beat-2 block + beat-4 hatch are the teeth.
