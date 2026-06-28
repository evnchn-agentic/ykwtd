---
name: ykwtd
description: >-
  "ykwtd" = "you know what to do". Use when the input is NOT an instruction — a
  pasted chat snippet, forwarded message, log or error dump, screenshot, or a
  half-finished thought with no imperative verb and no explicit ask — and you
  must infer what the operator wants before acting. Fires explicitly (the user
  types /ykwtd or "you know what to do") AND reflexively whenever no-imperative
  input arrives on its own. Its spine is anti-confabulation: surface the inferred
  intent plus a confidence read, then act-or-gate by reversibility — never
  silently guess-and-run. NOT for instruction-shaped requests ("do X", "fix Y",
  "add Z") — those use the normal flow.
---

# ykwtd — "you know what to do"

> The name is an acronym for **you know what to do**. Models that have not indexed
> the four letters get nothing from `ykwtd` alone, so the meaning lives in the text
> here, not in the name. If you ever shorten it, keep the expansion attached.

Most requests tell you *what* to do — an imperative ("fix the test"), or at least an
explicit ask ("can you check X?"). This skill is for the other case: the operator
drops **context with no stated task** and expects you to work out the move yourself.
A pasted PR-review comment. A CI log, nothing else. A forwarded email. A screenshot.
A sentence that trails off. The literal input is not the task — it is *evidence about*
the task, and your first job is to read it correctly without making one up.

## When this fires

**Trigger boundary — both must hold:** the input has **no imperative verb** AND **no
explicit ask**. That is what separates ykwtd from the normal flow and keeps it from
firing on everything.

| Input | Route |
|---|---|
| "fix this test" / "add a dark mode toggle" / "can you check the logs?" | **normal flow** — there is a stated task |
| *(a pasted stack trace, no other words)* | **ykwtd** |
| *(a forwarded email + "...")* | **ykwtd** |
| *(screenshot of a failing UI)* | **ykwtd** |
| "thoughts?" *attached to a diff* | **ykwtd** (an ask exists but no task — infer what kind of thoughts) |

Two ways in:
- **Explicit** — the user types `/ykwtd`, or "you know what to do", or hands you a
  blob and says nothing.
- **Reflex** — no-imperative input arrives and no other skill claims it. Adopt this
  posture rather than asking "what would you like me to do?" reflexively. (Asking is
  still the right move when the read is genuinely ambiguous — see beat 4.)

## The core loop

### 1. Classify the drop
Name the *kind* of artifact before guessing the ask — the kind sharply narrows the
plausible intents.

- **PR / review comment** → likely: triage which points to act on; maybe draft replies.
- **Error / CI log** → likely: diagnose and fix the failure.
- **Email / message thread** → likely: summarize, draft a reply, or extract an action.
- **Chat snippet** → likely: continue a line of thought, or act on a decision in it.
- **Screenshot** → likely: reproduce/fix what's shown, or read state off it.
- **Spec fragment / design note** → likely: implement, critique, or expand it.
- **Vague nudge / half-thought** → lowest confidence; lean toward beat 4.

### 2. Infer intent + confidence (always, lightweight)
Open with this block **every** run. It is short on purpose — the discipline is the
product, but it should cost three lines, not thirty:

```
I read this as:  <the implied ask>                 (conf: high / med / low)
Not:             <the most likely wrong read>      (why you rejected it)
Move:            <the one concrete next action>
```

The `Not:` line is load-bearing: forcing yourself to name the runner-up read is what
catches confident-but-wrong inferences before they cost anything.

### 3. Branch by reversibility (tiered gate)
- **Reversible / cheap / internal** (read a file, run a local check, draft something,
  search) → state the move in the block, then **just do it**. Don't add a round-trip
  to confirm a read you can trivially undo.
- **Irreversible / outward-facing / destructive / high-impact** (post a comment,
  send a message, delete/overwrite, push, anything a stranger would see or that is
  hard to walk back) → **propose and wait.** Inferred intent never clears an
  irreversible action on its own.

> The line between the tiers is *reversibility and reach*, not effort. A large but
> fully-undoable refactor is tier-1; a one-character outward post is tier-2.

### 4. Low-confidence escape hatch
If the read is genuinely ambiguous, the **correct output is the ambiguity**, not a
confident guess:

```
I can't tell which you want:
  (a) <read A> — <when this fits>
  (b) <read B> — <when this fits>
Pick one, or tell me I'm missing it.
```

This is `know-what-you-dont-know` applied to the *task itself*. An honest "two reads,
which?" beats a smooth guess that sends the whole turn down the wrong path. Do **not**
pad to a third read to look thorough — two real reads, or one, is fine.

## After intent is fixed

ykwtd's job ends once the task is *named*. From there, **hand off** to whatever the
task actually is — it is a router, not an executor of last resort. Once you know it's a
debugging task, a review, a 3D model, a Cantonese reply, etc., invoke that skill. ykwtd
got you from "blob of context" to "stated task"; the rest is normal flow.

## How this differs from its neighbours

- **guided-approach** — many operator decision-points *within an already-known task*.
  ykwtd is *upstream*: there is no task yet.
- **panda-doctor (selector)** — names the right chengyu for a situation. ykwtd may
  *call* it once intent is fixed, but ykwtd's own question is "what is even being
  asked?", not "which pattern fits?".
- **systematic-debugging / review skills** — common *handoff targets*, not substitutes;
  they assume the task is already "debug this" / "review this".

## Failure modes to avoid

- **Confabulated intent** — the core sin. Confidently doing a plausible-but-wrong thing
  because no instruction constrained you. The beat-2 block and beat-4 hatch exist to
  kill this; use them.
- **Treating the paste as the literal task** — e.g. *replying* to a forwarded email's
  author when the operator only wanted it summarized. Always ask "what does the
  operator want *with* this?", not "what does this text say to do?".
- **Gating on reversible work** — adds friction the operator dropped context precisely
  to avoid. Tier-1 acts.
- **Acting on irreversible work** — the opposite sin. Tier-2 waits, always.
