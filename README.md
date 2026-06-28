# ykwtd

**ykwtd = "you know what to do".** A skill for the case where the input is *evidence
about* a task, not the task itself — a pasted log, a forwarded email, a screenshot, a
half-finished thought with no imperative verb. See [`SKILL.md`](SKILL.md) for the actual
behaviour and [`DESIGN.md`](DESIGN.md) for the design rationale.

## Where the name comes from

The four letters are **r-906's track "YKWTD"**, surfaced via *maimai でらっくす*
(`https://www.youtube.com/watch?v=FOrineGipnw`). The skill is named after the song.

This was demonstrated, by accident, in the way the skill is *supposed* to work: someone
ran `/ykwtd` with that exact YouTube URL and nothing else. The agent had never indexed
the song, couldn't guess the etymology from the four letters, and — instead of
confabulating a backstory — went and *looked*, classified the drop, surfaced its
confidence, and named the joke rather than inventing a task. The skill passed its own
test on its own origin story. That self-reference is why the link is preserved here: when
the video eventually 404s, the name's meaning still survives in this text.

## The point this skill quietly makes

You do **not** need fancy scaffolding to make a good skill.

The whole of `ykwtd` is a tight spec: a clear trigger boundary, a three-line
intent-and-confidence block, a reversibility gate, and an honest "I can't tell, here are
two reads" escape hatch. No persona. No character. No imported vibe. The meaning lives in
the *text* — which is exactly why an un-indexed model still gets full value from it, and
why the opaque name (`ykwtd`) costs nothing: the H1, frontmatter, and first body line all
spell it out.

### Stop the `{FamousPerson}.md` trend

Naming a behaviour file after a real person — `Elon.md`, `<celebrity>.md`,
`<founder>.md` — is the opposite of this discipline, and it's bad practice:

- **It bundles an enormous, unscoped set of behaviours behind a name.** "Act like X"
  silently imports temperament, opinions, risk appetite, ethics, and a hundred unstated
  habits — none of them written down, none of them reviewable, none of them the thing you
  actually wanted. A skill should make its behaviour *explicit in the text*, not smuggle
  it in under a name.
- **You don't actually know the person well enough to be them.** You don't know Elon Musk
  well enough to drag him into a chat. What you have is a public caricature — so you're
  bundling *vibes*, not *behaviour*, and the model fills the gap with whatever stereotype
  it absorbed. That's confabulation by design — the exact failure mode `ykwtd` exists to
  prevent.
- **It conscripts a real person without consent or accuracy.** A named human becomes a
  load-bearing dependency you can neither verify nor update.

If you want a behaviour, *write the behaviour.* A skill earns its keep by spec, trigger,
and honesty — not by borrowing a name.
