---
name: ykwtd
description: >-
  "ykwtd" = "you know what to do". Use when the input is NOT an instruction — a
  pasted chat snippet, forwarded message, log or error dump, screenshot, or a
  half-finished thought with no imperative verb and no explicit ask — and you
  must infer what the operator wants before acting. Fires explicitly (the user
  types /ykwtd or "you know what to do") AND reflexively whenever no-imperative
  input arrives on its own — AND on an UNDERSPECIFIED imperative (a verb is
  present but the deliverable/quality bar is not — especially affect/service
  framings like "keep him entertained", "make her happy", "take care of it")
  when the downstream work is outward or irreversible. Its spine is
  anti-confabulation: surface the inferred intent plus a confidence read, then
  act-or-gate by reversibility — never silently guess-and-run. NOT for
  fully-specified requests ("do X", "fix Y", "add Z") — those use the normal flow.
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

**Trigger boundary — two gates, either one fires it:**

- **Gate A (classic):** the input has **no imperative verb** AND **no explicit ask**.
- **Gate B (underspecified imperative):** a verb IS present but names an *affect or
  service state*, not a deliverable ("keep him entertained", "make her happy",
  "take care of it", "keep the ball moving") — AND the work it spawns is **outward
  or irreversible**. Reversible-and-inward terse imperatives stay normal-flow;
  that is what keeps Gate B from firing on everything.

Gate B exists because a frame-setting verb doesn't just under-specify — it *selects
the task* at a layer below deliberation, and procedures outside the selected frame
(review gates, quality bars) may never surface for consideration at all. Running the
loop regenerates **goal + checker + pitfalls** into recent context, which is a
targeted refresh of exactly the early-loaded instructions that attention decay and
the frame would otherwise suppress (measured: adherence to distant instructions
decays within ~8 turns; regeneration partially restores it). Empirical anchor:
"keep him entertained and served" (2026-08-08, session 32dce382) spawned four
outward posts in 7 minutes with every meta-gate silently skipped — the frame primed
latency over rigor, and nothing re-armed the gates.

| Input | Route |
|---|---|
| "fix this test" / "add a dark mode toggle" / "can you check the logs?" | **normal flow** — there is a stated task |
| *(a pasted stack trace, no other words)* | **ykwtd** (Gate A) |
| *(a forwarded email + "...")* | **ykwtd** (Gate A) |
| *(screenshot of a failing UI)* | **ykwtd** (Gate A) |
| "thoughts?" *attached to a diff* | **ykwtd** (Gate A — an ask exists but no task) |
| "keep him entertained" *(work will go outward: posts, PRs, emails)* | **ykwtd** (Gate B — surface the decoded intent AND the assumed quality bar, e.g. "reading this as: continue servicing X *with the same rigor gates as before*") |
| "tidy this up" *(local file, reversible)* | **normal flow** — terse but inward + reversible |

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
  *Copy-pasted threads lose attribution: a trailing fragment before a compose-box
  placeholder (`Reply…`, `Message #channel`, `Type a message`) is the operator's OWN
  unsent draft, not the last poster's words. Resolve who-said-what before acting on it.*
- **Screenshot** → likely: reproduce/fix what's shown, or read state off it.
- **Spec fragment / design note** → likely: implement, critique, or expand it.
- **Voice memo / audio / mixed-media archive** (`.m4a` / `.opus` / a chat-export `.zip`) →
  **transcribe first** (local Whisper; force the Cantonese language code `yue` when it
  mis-detects as English — flag name varies by CLI, e.g. `whisper-cli -l yue`),
  unzip and split text / audio / images, *then* classify the recovered content. The drop isn't
  legible until transcribed.
- **Vague nudge / half-thought** → lowest confidence; lean toward beat 4.

A drop can be a **collection, not one item** — a whole inbox / milestone / PR-list
("notifications-wide ykwtd"). Classify and digest *across* N; don't force one intent onto the set.

**A collapsed drop is not a cheap drop.** A harness may render a long paste compactly — Claude Code
(2.1.x) shows a `[Pasted text #N +M lines]` chip — but that is presentation: the full content is
substituted back before the message is built, so the whole blob enters context and no display-side
setting shortens it. Don't assume any harness digests a drop on your behalf. When a large drop is
only partly relevant, ask for a **path instead of a paste**, then read it selectively or hand the
file to a subagent — beat 4's higher-fidelity ask, on the cost axis rather than the fidelity axis.

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

**Before you gate a tier-2 action, try to downgrade it.** This 2-tier split is the fast
version; when an action *looks* irreversible, the `hats-haircuts-tattoos` skill adds the
middle "haircut" tier and — more importantly — the **downgrade move**: cheaply buy back
reversibility (backup / snapshot / dry-run) so a tier-2 tattoo becomes a tier-1 hat and
you can just act. Reach for it whenever this gate would otherwise stop you.

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

**When the low confidence traces to *lossy evidence* — not genuine ambiguity** — a
mangled chat paste, a blurry screenshot, a truncated log — the best output is often a
request for a **higher-fidelity artifact**, not a disambiguating question: "send that as
a screenshot", "paste the full tail", "can you zoom in?". A screenshot of a chat recovers
the alignment / compose-box / speaker structure that text-copy destroys, resolving every
attribution boundary at once. Fixing the input beats inferring across garbage (GIGO).
**Gate it on task-changing loss:** a clean-enough paste, or one resolvable from a tell
(compose placeholder → operator's own draft), does NOT earn a round-trip — proceed on a
stated assumption and let the operator correct. ykwtd exists to *save* a round-trip; spend
one only when the loss actually changes what you'd do.

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
