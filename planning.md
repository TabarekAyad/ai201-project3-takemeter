# Planning — TakeMeter

## Overview

**Community:** r/soccer (Reddit)

r/soccer is one of the largest sports communities on Reddit, with millions of members posting around matches, transfers, tactics, and player debates. Discourse quality varies enormously: the same thread can contain a stat-backed tactical breakdown, a confident but unsupported claim about a player's worth, and a pure emotional outburst after a last-minute goal. This variance makes it a strong fit for a classification task — the distinctions are real, they matter to community members, and they show up consistently in the text.

**Why this community works for classification:**
- Posts are text-heavy and mostly self-contained
- The analysis/hot_take/reaction distinction is one community members explicitly make ("this is just a hot take," "do you have stats for that?")
- Enough variance in style, length, and tone to produce a non-trivial boundary problem

---

## Label Taxonomy

| Label | Definition | Signals to look for |
|---|---|---|
| `analysis` | The post makes a structured argument backed by statistics, historical comparison, or tactical observation. Evidence is specific and verifiable. | Numbers with context, comparisons across players/teams/seasons, tactical reasoning ("because of their press triggers…") |
| `hot_take` | A bold, confident opinion stated without supporting evidence. The claim might be true, but the post asserts rather than argues. | Strong declarative claims, superlatives ("worst ever", "clearly better"), no citations or only decorative ones |
| `reaction` | An immediate emotional response to a specific event. Little to no argument — the post is expressing a feeling in the moment. | Event-anchored language ("just now", "that goal", "I can't believe"), exclamations, short posts tied to a live match |

### Example posts per label

**analysis:**
- "Over the last 3 seasons, Salah has averaged 0.71 non-penalty goals per 90 — higher than any other PL winger in that window. His off-ball movement has changed too: he now makes 40% more runs in behind compared to 2020/21, which is why Liverpool's transitions look different."
- "Comparing City's xGA under Guardiola with and without Rodri in the lineup (sample: 47 matches), the gap is 0.31 xGA/90. That's not incidental — it reflects how Rodri's positioning compresses opposition counter-attack lanes."

**hot_take:**
- "Bellingham is overrated and Madrid made a mistake. He's not a top-5 midfielder in the world."
- "English clubs will never win the Champions League consistently. The Premier League is just entertainment football, not serious."

**reaction:**
- "THAT GOAL. I'm shaking. Best night of my life as a football fan."
- "I can't watch anymore. Three nil down at half time. Season is done."

---

## Hard Edge Cases

### Edge Case 1: analysis vs. hot_take (hardest)

**Example post:**
> "Mbappe is clearly the best forward in the world right now. He had 44 goals and assists last season."

This post could be `analysis` (cites a stat) or `hot_take` (bold claim, one cherry-picked number).

**Decision rule:** Remove the opinion framing and ask — does the remaining evidence build a coherent argument, or does it just make the assertion sound credible? A single stat dropped without comparison, context, or causal connection is decorative evidence → **hot_take**. If the post compares across peers, explains why the metric is relevant to the claim, or traces a causal chain, the evidence is load-bearing → **analysis**.

Test: could you use the evidence to convince a skeptic, or is it just there to make the assertion feel less naked?

---

### Edge Case 2: hot_take vs. reaction (medium)

**Example post:**
> "After that performance, I'm convinced Maguire is genuinely one of the worst defenders in the Premier League."

This could be `reaction` (triggered by a specific match) or `hot_take` (general verdict on player quality).

**Decision rule:** If the claim extends beyond the specific event — a general verdict on a player or team that would still hold after the match is forgotten — label it **hot_take**, even if a recent event triggered it. If the post stays anchored to the specific event and expresses a feeling about that moment without making a broader claim, label it **reaction**. The word "convinced" signals a general position being formed → **hot_take**.

---

### Edge Case 3: analysis vs. reaction (easiest)

**Example post:**
> "Incredible today — City pressed 34 times in the final third, recovered 12 balls. That's exactly why they dominated."

This is event-triggered (sounds like reaction) but uses verifiable tactical data to explain why something happened (sounds like analysis).

**Decision rule:** The trigger doesn't determine the label — the structure does. If the post builds an evidence-backed causal explanation regardless of what prompted it, label it **analysis**. Reaction posts express feeling; analysis posts explain mechanism. "That was incredible!" → reaction. "Here's the tactical reason it happened, with numbers" → analysis.

---

### Summary table

| Boundary | Key question | → label |
|---|---|---|
| analysis vs. hot_take | Does the evidence genuinely support the claim, or just decorate it? | load-bearing → analysis, decorative → hot_take |
| hot_take vs. reaction | Is there a general verdict about player/team quality, or a feeling about this specific moment? | general verdict → hot_take, moment-anchored feeling → reaction |
| analysis vs. reaction | Is the post explaining a mechanism with evidence, or expressing an emotion? | mechanism + evidence → analysis, emotion → reaction |

---

