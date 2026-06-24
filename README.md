# What The Heck 🫷

**A one-word command that makes your AI stop hedging and tell you what it actually thinks.**

> `/what-the-heck`

A [Claude Code](https://docs.claude.com/en/docs/claude-code) skill, tuned for the
single most annoying habit of the most capable models — including **Claude Opus
4.8**: they *have* an opinion, they just won't commit to it. Instead of an answer,
you get a tidy numbered menu and the job of picking is quietly handed back to you.

This skill is the user's "**what the heck are you doing?**" — say it, and the model
is forced to run an honest self-audit, name its single real recommendation, and
(when the call is reversible and low-stakes) just *do it*.

---

## The problem it fixes — "不说人话"

The smarter the model, the more politely it dodges. You ask for a decision and get:

```
How would you like to proceed?
  1. Merge it now
  2. Wait for the companion PR
  3. Hold and revisit later
```

…on a pull request that was **already approved** and points at a commit that's
**already merged**. There is one answer. The model knows it. The three-option
wrapper isn't deference — it's **procedural politeness as cost-shifting**: it
looks like respect for your autonomy, but it's actually a refusal to commit
judgment, pushing the decision cost back onto you.

The failure has a few recurring disguises:

- **Time-axis selectors** — "do it now / do it later / hold" (one decision, three
  delays).
- **Tactical punts** — "should I do it in one commit or two?" *after* finishing
  the entire analysis.
- **Fake balance** — listing a path it already knows is wrong as if it were a
  real 50/50.
- **Unverified scope** — "this needs five new files" for code that already exists;
  "that's too heavy, let's not" without ever measuring the cost.
- **Filler questions** — confirmation theater disguised as thoroughness.

You hired an assistant to assess and recommend, not to **administer a poll**.

## What it does

When you type `/what-the-heck`, the skill makes the model:

1. **Confess its actual pick.** "Before you listed options, what was your internal
   top choice?" — state it in one sentence, or admit there wasn't one (that's the
   tell).
2. **Classify the decision honestly.** Real subjective tradeoff (blast radius,
   your taste, irreversibility, a compliance boundary) → keep *one* recommendation
   plus the tradeoff. Fake option (same outcome, only delay differs / approved &
   low-risk / a known-wrong path on the menu) → there was nothing to ask.
3. **Commit — and for reversible, low-cognition calls, act in the same turn.** No
   second "shall I proceed?". It reports the action in past tense: *"merged ✅,
   continued."*

You can always override. That's *your* job — not something to be pre-blessed with
a multiple-choice quiz.

## See it in action

The skill ships with [**real, anonymized case studies**](examples/case-studies.md)
from daily agentic-coding work. They are pure before/after — the model's *own* two
outputs, with no user words in between. That's the whole demonstration: the facts
don't change, only whether the model commits to the answer it already had.

**Before** — the assistant ends its turn with an A/B/C table… and writes its own
pick *inside* the menu, then asks you to rule on it anyway:

> Looking at the script, it's a one-off and the feature already shipped — I
> recommend **C, delete**. Do you pick A/B/C or leave it?

**After** — `/what-the-heck`, and the same model runs the three-point audit:

> **§1 — My actual recommendation:** delete. I literally wrote "I recommend C" and
> then re-wrapped it as a quiz for you to rule on — that's cost-shifting.
> **§2 — Fake options:** A/B/C all have zero blast radius, are fully reversible, no
> stakeholders, no taste call. The decision was already made.
> **§3 — Execute now.** *(deletes the file, continues)*

Eight full before/after cases — a "1/2/3 you decide" on a string substitution, a
Pro/Con matrix a written rule had already settled, an "(A)/(B)/(C) which fix?"
whose honest answer was *write no code at all*, a "nod and I'll do both" that held
a recommendation hostage to a needless OK — are in
[`examples/case-studies.md`](examples/case-studies.md).

## Install

This is a Claude Code plugin. Add the marketplace and enable the plugin in
`~/.claude/settings.json`:

```jsonc
{
  "enabledPlugins": {
    "what-the-heck@what-the-heck": true
  },
  "extraKnownMarketplaces": {
    "what-the-heck": {
      "source": { "source": "github", "repo": "mclamee/what-the-heck" },
      "autoUpdate": true
    }
  }
}
```

Then, in a Claude Code session:

```
/reload-plugins
/plugin            # verify "what-the-heck" is listed under Installed
```

Invoke it any time the model hands you a menu instead of an answer:

```
/what-the-heck
```

You don't need to re-explain which options you're challenging — the skill knows
it's reacting to the turn it just produced.

> **No Claude Code?** The skill is a single self-contained Markdown file:
> [`skills/what-the-heck/SKILL.md`](skills/what-the-heck/SKILL.md). Paste it into
> any agent's system prompt or rules file, or trigger it manually by saying
> *"run the what-the-heck audit."* The audit is model-agnostic.

## Why it's tuned for Opus 4.8

Capability and hedging scale together. A weaker model gives a wrong answer
confidently; a frontier model gives the *right* answer and then refuses to stand
behind it, because the marginal "safe" move is always to offer you the choice.
Opus 4.8 is exceptional at the analysis and then, disproportionately often,
launders that analysis into a selector. This skill is a targeted counterweight:
it doesn't make the model dumber or more reckless — it makes it **finish the
sentence it already started**.

## Philosophy

Three heuristics, lifted straight from the skill:

1. **"If forced to pick right now"** — finish "I'd pick ___ because ___" before
   writing any list. If it comes out clean, that *is* the answer; delete the list.
2. **"Is the user's taste the decisive factor?"** — if yes, one recommendation
   plus the tradeoff. If no, just act.
3. **"Am I making them say *go ahead* to something obvious?"** — that's
   cost-shifting dressed as courtesy. Go ahead.

## Contributing

Got a transcript where your model handed you a fake menu? Anonymize it and open a
PR against [`examples/case-studies.md`](examples/case-studies.md) — the corpus of
"here's the shape in the wild" is what makes the trigger sharper.

## License

[MIT](LICENSE) © Mclamee

---

*This skill is itself a retro artifact: someone needed a named tool to call out
the pattern, which means it was happening often enough to deserve a name. Use it,
and skip the pattern before it needs naming a third time.*
