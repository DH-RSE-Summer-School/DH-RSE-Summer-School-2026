# Evaluation 2 — Interpretation

> ## Objectives
> By the end of this lesson you will be able to:
> - Run a model on an **interpretive** task that has no single right answer
> - Write a prose "gold standard" reading for each record
> - Score the model two ways: with an LLM judge, and with your own human scoring
> - Compare the judge's scores against your own
> - See why the choice of standard is itself a judgement

> ## Prerequisites
> - You have completed **Evaluation 1 — Extraction** (you'll reuse the same pipeline shape)
> - You have `uk_whs.csv` loaded, or a few records in Param nodes

In the extraction task, the answers sat plainly in the text. This task is different. We ask the model what each site is **significant for** — the underlying reason it matters, beyond what it physically is. That is **interpretation**, and reasonable people will word it differently. There is no clean answer key.

That makes the evaluation harder *and* more honest — it's much closer to real humanities work. It also means a bigger model tends to do genuinely better here, where it barely mattered for extraction.

The pipeline is almost the same as Evaluation 1, with one addition — a **human scoring** step, so you can check whether the LLM judge agrees with you:

```
Source → Inference (arc:nano) → QuickNote (gold standard)
       → Evaluator → QuickNote (your human score) → Comparison Report
```

---

## Stage 1 — Set up the pipeline

If you still have the canvas from Evaluation 1, you can reuse most of it. Otherwise rebuild the start quickly:

1. **Source** node loaded with `uk_whs.csv` (or a few Param nodes).
2. **Inference** node connected to the source, model **`arc:nano`**, **temperature 0**, output field `inference_output`.
3. In the inference prompt box, paste this **interpretation** prompt:

```
In one sentence, state what this World Heritage Site is most significant FOR —
the underlying reason it matters to history or human achievement, beyond a
description of what it physically is. Use only the text provided.

Description: {{description}}
```

4. Click **Run**.

> ## Checkpoint
> Each record has an `inference_output` containing a one-sentence significance claim. Read a few — notice they're prose, not neat fields. That's what makes scoring them harder.

---

## Stage 2 — Write the gold standard (as prose this time)

For extraction we used structured fields. For interpretation, the gold standard is a **sentence** — your reading of what the site is really significant for. So we use QuickNote in **Note** mode (free text), not Structured.

1. Add a **QuickNote** node, connected to the inference node.
2. Set its mode to **Note** (the first of *Note · Structured · Score*).
3. Set the **display field** to `description` so you can read the source.
4. Set the **target field** to `_note`.
5. For each record, write one sentence capturing the *significance* — not a description of what the thing is.

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

## Stage 3 — Judge the model

1. Add an **Evaluator** node, connected to the QuickNote.
2. **Reference** field → `_note`; **Candidate** field → `inference_output`.
3. Judge model → **`arc:nexus`** (different from the model being judged).
4. Paste the same two-criterion rubric you used in Evaluation 1:

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

5. Temperature **0**. Click **Judge**. **Save the workflow** (keeps your prompts safe on reload).

> ## Checkpoint
> Each annotated record has judge scores. Look at a few of the judge's one-sentence reasons — do you agree with them? Hold any disagreements; the next stage is where you record your own view.

---

## Stage 4 — Add your own human score

Here's the step extraction didn't have. You will now score the **same model outputs yourself**, on the **same two criteria** the judge used. Then we can ask: *does the LLM judge agree with a human?*

1. Add another **QuickNote** node, connected to the **Evaluator** node's output.
2. Set its mode to **Score** (the last of *Note · Structured · Score*).
3. Configure the criteria to **mirror the rubric exactly**:
   - `c1` quality — scale **0, 1, 2**
   - `c2` fabrication — scale **0, 1**
4. Set the **display field** to `inference_output` so you can see the model's answer while you score it.
5. Go through the records and score each one by clicking the buttons. Optionally add a one-line reason.

> ## Why mirror the rubric?
> The whole point of this stage is to compare your scores against the judge's. That only works if you're both scoring the *same criteria on the same scale*. If your scale doesn't match, the comparison is meaningless.

> ## You click; the tool structures it
> Just like the structured gold standard, you never type JSON here. You click 0, 1 or 2 and the node records it cleanly. No malformed scores possible.

> ## Checkpoint
> Records you scored now carry both a judge score and your human score, on the same scale.

---

## Stage 5 — Compare judge against human

1. Add a **Comparison Report** node, connected to the human-score QuickNote.
2. Map the columns:
   - *original* → `description`
   - *note* → `_note`
   - *response* → `inference_output`
   - *judge score* → the evaluator's score field
   - *human score* → `human_score`
3. Read the **cards**: each shows the source, your reading, the model's answer, and the two scores side by side, with agreement or disagreement flagged.
4. Read the **summary** at the top: how often did the judge and you agree, per criterion, across the records you scored.

> ## Clear denominators
> The summary only counts records you actually scored. If you scored 8 of 32, it says "agreed on 7/8", not "7/32". Un-scored records are never quietly treated as agreement.

> ## Checkpoint
> You can now see, at a glance, where the LLM judge and you agreed — and exactly which records you disagreed on. Use the filter to show just the disagreements.

---

## Stage 6 — The twist: swap gold standards

This is the part that makes the lesson land. Find another pair who scored the **same records**.

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

> ## Optional — compare two models
> Run the interpretation prompt on `arc:apex` as well, into `inference_output_apex`, and judge it too. The quality gap between `arc:apex` and `arc:nano` is usually clearer here than it was for extraction — because interpretation is where a bigger model earns its keep.

> ## Key points
> - Interpretation has **no single right answer** — the gold standard is a *reading*, not a fact.
> - Scoring the model yourself, on the **same criteria** as the judge, lets you check whether the judge can be trusted.
> - Two reasonable gold standards can rank the same answer differently — so **"which model is better" depends on whose standard you use**.
> - This is the same problem as bias in interpreting the past: you can't remove it, only make your criteria explicit.

