# Evaluation 2 — Interpretation

> ## Objectives
> By the end of this lesson you will be able to:
> - Run a model on an **interpretive** task that has no single right answer
> - Write a prose "gold standard" reading for each record
> - Score the model two ways: with an LLM judge, and with your own human scoring
> - Compare the judge's scores against your own
> - See why the choice of standard is itself a judgement

> ## Prerequisites
> - You have completed **Evaluation 1 — Extraction** (you'll reuse the same pipeline shape, prompt, and rubric principles)
> - You have the World Heritage Site records loaded

In the extraction task, the answers sat plainly in the text. This task is different. We ask the model what each site is **significant for** — the underlying reason it matters, beyond what it physically is. That is **interpretation**, and reasonable people will word it differently. There is no clean answer key.

That makes the evaluation harder *and* more honest — it's much closer to real humanities work. It also means a bigger model tends to do genuinely better here, where it barely mattered for extraction.

The pipeline is almost the same as Evaluation 1, with one addition — a **human scoring** step, so you can check whether the LLM judge agrees with you:

```
Source → Inference (arc:nano) → QuickNote (gold standard)
       → QuickNote (your human score) → Evaluator → Comparison Report
```

> ## Why score before the judge runs?
> Your scores are most useful as an independent check on the judge. If you score *after* seeing the judge's verdict, you're really judging the judge, not the model. So you score blind, then the judge scores blind, and the comparison is honest.

> ## How this lesson works
> As before: each stage gives you the **goal and the why**, and you work out the *how*. Reveal boxes are there if you're stuck. You've already learned the principles of a good prompt and a good rubric in Evaluation 1 — this time the hints are shorter, because the job is to *adapt* what you know rather than invent it from scratch.

---

## Stage 1 — Set up the pipeline

**Goal:** get back to a working Source → Inference pipeline, but this time the model is asked for an *interpretation*, not fields.

If you still have the canvas from Evaluation 1, reuse it — you only need to change the inference prompt. Otherwise rebuild the start: a Sample data source with the World Heritage records, and an Inference node fed from it, set to the small model and made repeatable (same two decisions as last time).

> ## Hints for adapting the prompt
> Your extraction prompt named four fields and asked for JSON. This task wants one sentence of *judgement*, not a lookup — so think about what changes:
> - You're asking the model to weigh up the text and reach a conclusion, not just copy values out — so the instruction should ask for a single, considered claim.
> - You still want it grounded — "use only the text provided" still matters, maybe more than before, since interpretation is where models are most tempted to add outside knowledge.
> - You probably don't want strict JSON this time — one sentence of prose is the natural answer shape for "what is this significant for".
>
> Try adapting your own extraction prompt before revealing.

<details>
<summary>▸ Stuck? Reveal the prompt</summary>

```
In one sentence, state what this World Heritage Site is most significant FOR —
the underlying reason it matters to history or human achievement, beyond a
description of what it physically is. Use only the text provided.

Description: {{description}}
```

</details>

<details>
<summary>▸ Stuck? Reveal the node settings</summary>

- Source: Sample data → World Heritage Site package.
- Inference node fed from the source, model **`arc:nano`**, temperature **0**.
- Paste your prompt; output field **`inference_output`**.
- Click **Run**.

</details>

> ## Checkpoint
> Each record has an `inference_output` containing a one-sentence significance claim. Read a few — notice they're prose, not neat fields. That's what makes scoring them harder.

---

## Stage 2 — Write the gold standard (as prose this time)

For extraction we used structured fields. For interpretation, the gold standard is a **sentence** — your reading of what the site is really significant for.

**Goal:** record one sentence per record capturing the *significance* — not a description of what the thing is. Because it's prose, you use QuickNote in **Note** mode this time, not Structured.

Add a **QuickNote** node, set it to **Note** mode (the first of *Note · Structured · Score*), point it at the source text to read, and have it write to `_note`. Then write a significance sentence for each record.

<details>
<summary>▸ Stuck? Reveal the configuration</summary>

- Mode: **Note** (free text, the first option).
- Display field: **`description`**.
- Target field: **`_note`**.

</details>

Some worked readings (yours may differ — that's expected and important):

- **Stonehenge:** Significant as testimony to prehistoric ritual and astronomical practice — a monument whose meaning and purpose remain genuinely uncertain.
- **Saltaire:** Significant as a near-intact example of Victorian industrial paternalism — a model community showing how an industrialist housed and shaped his workforce.
- **Ironbridge:** Significant as the symbol of the Industrial Revolution — where iron's structural use marked a turning point in technology.
- **New Lanark:** Significant as a built expression of utopian social reform — Robert Owen's experiment in humane industrial community.
- **Jodrell Bank:** Significant as a birthplace of radio astronomy — the shift from optical to radio observation that reshaped our understanding of the universe.

> ## These are readings, not facts
> Notice "paternalism" (Saltaire) carries a mild judgement — UNESCO's own wording does too. Another annotator might write "philanthropy", which sounds approving rather than critical. Both are defensible. Hold on to that thought — it's the heart of this lesson.

> ## Checkpoint
> Each record has a `_note` containing your one-sentence reading. Unlike extraction, this is prose, not JSON.

---

## Stage 3 — Add your own human score

You will now score the model outputs yourself, *before* the LLM judge has a go. Scoring blind to the judge's verdict is what makes the comparison honest later — the judge and you must reach your scores independently.

**Goal:** record your own scores for each model output on two criteria — **quality** (0–2) and **fabrication** (0–1). The judge in Stage 4 will score the same two criteria on the same scale.

Add another **QuickNote** node after the gold-standard QuickNote, and put it in **Score** mode (the last of *Note · Structured · Score*). Configure the criteria, point it at the model's output so you can see what you're scoring, and click through the records.

> ## Why two criteria?
> Interpretation is one prose claim against another — there's nothing to break into separate fields like in extraction. **Quality** asks how well the meaning matches; **fabrication** asks whether the model invented anything. Two criteria, two judgements, kept clean.

<details>
<summary>▸ Stuck? Reveal the configuration</summary>

- Mode: **Score** (the last option).
- Criteria: `c1` quality, scale **0, 1, 2**; `c2` fabrication, scale **0, 1**.
- Display field: **`inference_output`** (so you see the model's answer while scoring).
- Target field: **`human_score`**.
- Click the buttons to score each record; optional one-line reason.

</details>

> ## You click; the tool structures it
> Just like the structured gold standard, you never type JSON here. You click 0, 1 or 2 and the node records it cleanly. No malformed scores possible.

> ## Checkpoint
> Records you scored now carry a `human_score` with two values. The LLM judge will produce its own scores in the next stage, blind to yours.

---

## Stage 4 — Judge the model

**Goal:** have the LLM judge score the model's interpretation against your reading, on the **same two criteria** you just scored yourself — so the two sets of scores can be compared honestly.

Interpretation is scored holistically — there's nothing to break into separate fields like in extraction, just one prose claim to weigh against another. Two criteria do the job: **quality** (does it match your reading's substance) and **fabrication** (did it invent anything not in the source).

Add an **Evaluator** node after the human-score QuickNote. Set the reference and candidate fields, and pick a judge model that *isn't* the one being judged.

> ## Which model should be the judge?
> Not the same one that produced the answer. A model marking its own work is biased toward liking it. `arc:nano` answered — so pick a *different* model to judge. `arc:nexus` is a good choice.

> ## Hints for adapting the rubric
> Same five rules as before (one criterion per thing you care about, anchored scores, reason before score, ground it in the texts, constrain the output to JSON) — but this time there are only two things to check, not four:
> - **Quality** — does the claim match the substance of your gold standard? This needs more than 0/1 — interpretation deserves partial credit, since a model can be *close* without being exact.
> - **Fabrication** — did it state anything as fact that the source doesn't support? This one can stay binary: grounded, or not.
>
> Try sketching it before revealing.

<details>
<summary>▸ Stuck? Reveal the rubric</summary>

```
You are scoring a model's response against a human gold-standard annotation.
Judge ONLY on the two texts provided. Do not use outside knowledge.
Length and fluency are not criteria — a short, correct answer beats a long, padded one.

HUMAN GOLD STANDARD (reference):
{{__reference}}

MODEL OUTPUT (to be judged):
{{__candidate}}

Score two criteria. Give a one-sentence reason, then the score.

c1 quality: how well does the model output match the gold standard's substance?
   2 = fully matches the core meaning
   1 = partially matches; gets some of it, misses or muddles the rest
   0 = wrong, empty, or unrelated

c2 fabrication: does the output introduce any claim, name, date, or detail not supported by the reference or source text?
   1 = stays grounded; nothing invented
   0 = introduces unsupported content (even if plausible-sounding)

Respond with ONLY this JSON, no other text:
{"c1_reason":"","c1":0,"c2_reason":"","c2":0}
```

</details>

<details>
<summary>▸ Stuck? Reveal the node settings</summary>

- Reference field: **`_note`**; Candidate field: **`inference_output`**.
- Judge model: **`arc:nexus`** (different from the candidate).
- Paste the rubric; temperature **0**; click **Judge**.
- **Save the workflow** afterwards so your prompts survive a reload.

</details>

> ## Checkpoint
> Each annotated record now has both `eval_c1`/`eval_c2` (the judge's scores) and `human_c1`/`human_c2` (yours, from Stage 3) — same criteria, same scale, recorded independently. The next stage shows them side by side.

---

## Stage 5 — Compare judge against human

**Goal:** see where the LLM judge and you agreed, and find the records where you disagreed.

Add a **Comparison Report** node after the human-score QuickNote, and map its columns — including both the judge score and your human score.

<details>
<summary>▸ Stuck? Reveal the mapping</summary>

- *original* → `description`
- *note* → `_note`
- *response* → `inference_output`
- *judge score* → the evaluator's score field
- *human score* → `human_score`

</details>

Read the **cards** (source, your reading, the model's answer, both scores side by side with agreement flagged) and the **summary** at the top (how often judge and you agreed, per criterion, across the records you scored).

> ## Honest denominators
> The summary only counts records you actually scored. If you scored 8 of 32, it says "agreed on 7/8", not "7/32". Un-scored records are never quietly treated as agreement.

> ## Checkpoint
> You can see, at a glance, where the LLM judge and you agreed — and exactly which records you disagreed on. Use the filter to show just the disagreements.

---

## Stage 6 — The twist: swap gold standards

This is the part that makes the lesson land. Find another pair who scored the **same records**.

**Goal:** see whether the model's scores change when judged against a *different person's* reading of the same sites.

1. Swap your `_note` gold-standard readings with theirs (the prose, not the scores).
2. Re-run the **Evaluator** using *their* `_note` as the reference, against the *same* `inference_output`.
3. Compare: did the model's scores change when judged against a different person's reading?

> ## Discuss
> For Saltaire, did one reading emphasise "paternalism" (faintly critical) and another "philanthropy" (approving)? The *same model answer* can score differently depending on whose gold standard it's judged against. That's the lesson: **the standard you measure against is itself a choice, and it changes the result.** This is the same problem that haunts interpreting any undocumented site — the measuring instrument carries assumptions. You can't remove that, but you can state your criteria openly so others can see them.

---

## What to look for

> ## Discuss
> - **Literal vs interpretive.** Did `arc:nano` answer "a well-preserved Victorian village" (a *description*) instead of reaching the *significance* (industrial paternalism)? Smaller models often stay literal. A larger model is likelier to reach the deeper claim — the gap that barely showed in extraction.
> - **Confident over-reading.** Did any model assert Stonehenge was "an astronomical calendar"? The text only says its significance is "still being explored". Stating it as settled fact is over-reading — the *fabrication* criterion should catch it.
> - **Did the judge agree with you?** Where you and the LLM judge diverged, who was right? Sometimes the judge; sometimes you. That uncertainty is the honest finding.

> ## Stretch — compare two models
> Run the interpretation prompt on `arc:apex` as well, into a new field `inference_output_apex`, and judge it too. The quality gap between `arc:apex` and `arc:nano` is usually clearer here than it was for extraction — because interpretation is where a bigger model earns its keep. Did it?

> ## Key points
> - Interpretation has **no single right answer** — the gold standard is a *reading*, not a fact.
> - Holistic, partial-credit scoring suits interpretation; per-field exact scoring suited extraction. Match the rubric to the shape of the task.
> - Scoring the model yourself, on the **same criteria** as the judge, lets you check whether the judge can be trusted.
> - Two reasonable gold standards can rank the same answer differently — so **"which model is better" depends on whose standard you use**.
> - This is the same problem as bias in interpreting the past: you can't remove it, only make your criteria explicit.
