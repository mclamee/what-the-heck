# Case studies — the pattern in the wild

These are real moments, anonymized, from day-to-day agentic coding work on a
production codebase. Repository names, PR numbers, service names, and dates have
been genericized. The **user quotes are verbatim** (Chinese original + English
translation) because they are the whole point: this is what it sounds like when
a person gets tired of being handed a multiple-choice quiz instead of an answer.

Every one of these is a case where the model *had* a correct answer and buried
it under a selector.

---

## Case 1 — "Merge now / wait / hold" on an already-approved bump

**What the assistant typed.** A pull request had already passed review. It was a
pure version-pointer bump aimed at an upstream commit that was *already merged*.
The assistant ended its turn with:

> 1. Merge now
> 2. Wait for the companion PR
> 3. Hold

**What the user said.**

> 这三个算是低认知增加评定工作量的选项吧？你为什么总是出这样的三个选项来让人选呢？
>
> *"These three are just low-cognition options that increase my evaluation
> burden, aren't they? Why do you keep producing three options like this for me
> to pick from?"*

**The real answer.** Merge. All three options collapsed to *"merge now vs. delay
it to a non-specific future moment."* The "wait" had no real blocking event —
the thing it would wait for was already done. The "hold" existed only because the
assistant couldn't articulate a reason *not* to act.

**The cost of the menu.** A one-line "merged ✅, continued" was turned into a
round-trip plus decision friction. Multiply by every approved chore in a week.

> This is the founding incident. The skill exists because this exact shape kept
> recurring often enough to need a name.

---

## Case 2 — Punting commit granularity *after* doing all the thinking

**What the assistant typed.** It had completed a full optimization pass: listed
every item, ranked them by ROI, flagged the risks, grouped them into logical
commits. Then it closed with:

> Do you want me to do it all in one commit, or split it into two?

**What the user said.**

> 为什么这个 scope 还需要我判断，你问这个问题的用意是什么
>
> *"Why does this scope still need my judgment — what is your intent in asking
> this question?"*

**The real answer.** Split by logical boundary and do it. Commit granularity is a
tactical decision the implementer owns. The question wasn't deference; it was
*"I know how to do this but I'm afraid to own the whole decision."*

---

## Case 3 — Listing a known-wrong path as if it were a real choice

**What the assistant typed.** Scoping a reporting/dashboard feature, it offered:

> - **Path A** — smaller, ships faster, but the numbers are knowingly wrong for
>   one refund edge case
> - **Path B** — slightly larger, correct

**What the user said.**

> 能不能选择长期更优的方案
>
> *"Can you just pick the long-term-better option?"*

**The real answer.** Path B. A path you already know ships a falsehood is not a
legitimate menu item. Offering it is fake-balance — dressing a wrong answer up as
a 50/50 to avoid being the one who says "we should do it right."

---

## Case 4 — Speccing a greenfield build on top of code that already existed

**What the assistant typed.** Asked to plan a feature, it proposed five-plus new
files, a new service, and new endpoints — for functionality that *already lived
in the codebase*. It had scoped the whole thing from an exploration summary, not
from the actual source.

**What the user said** (after invoking `/what-the-heck`):

> 看真实的代码情况
>
> *"Look at the actual state of the code."*

**The real answer.** Grep first. The real work was a one-line patch plus one small
follow-up — roughly 80% of the proposed "new" feature already existed. The lesson
generalizes: don't scope *up* (or *out*) based on a summary. Verify against real
code before you put numbers on the table.

---

## Case 5 — "Too heavy, let's not" without checking the actual cost

**What the assistant typed.** It steered away from a per-user scheduling approach,
asserting it was *"too heavy — it would need recurring schedule management,"* and
nudged toward a more complex alternative.

**What happened.** The user asked a single question: does the underlying SDK
support one-time, per-user runs natively? It does — directly, with a built-in
delayed-start primitive.

**The real answer.** The dismissed approach was actually the *lightest* one. The
"it's expensive" was a vibe, not a measurement. Capability and cost claims need a
citation before they're allowed to kill an option.

---

## Case 6 — Deferring a zero-cost fix

**What the assistant typed.** It noticed a missing dependency and proposed to
*"add it in the next change."*

**What the user said.**

> 需要哦，这个本地执行没有成本，请记住
>
> *"Do it now — this costs nothing to fix locally, please remember."*

**The real answer.** Fix it now. "Later" was not a tradeoff; it was deferral for
its own sake, the action-level cousin of the fake selector.

---

## The shared anatomy

| Surface form | What it actually was |
|---|---|
| "now / wait / hold" | one decision split along a time axis |
| "all at once / split?" | a tactical call the implementer owned, handed back |
| "fast-but-wrong / correct?" | a wrong answer disguised as a 50/50 |
| "five new files" on existing code | a scope claim never checked against source |
| "too heavy, let's not" | a cost claim never measured |
| "add it next time" | deferral dressed as prioritization |

In every case the model had — or could trivially obtain — the right answer, and
chose to administer a poll instead. `/what-the-heck` is the one-word way to say
*"stop polling me and tell me what you think."*
