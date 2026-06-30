# Evaluation 1 — Extraction

> ## Objectives
> By the end of this lesson you will be able to:
> - Load a set of records into the workflow canvas
> - Run a small language model to extract structured fields from text
> - Write a prompt that gets clean, structured output from a model
> - Record a human "gold standard" answer for each record
> - Write a rubric that lets an LLM judge score a model's extraction field by field
> - Score the outputs yourself and compare against the judge
> - Read the results in a comparison report

> ## Prerequisites
> - You have completed PoC orientation (placing and connecting nodes)
> - The PoC canvas is open in your browser

In this evaluation you will test how well a small model (`arc:nano`) pulls **structured facts** out of a short description — the place, the period, the site type, and the nation. Because the answers sit plainly in the text, this is a task a non-expert can check. That makes it a good first evaluation: the "right answer" is not very contestable, so you can focus on learning the workflow.

We will build this pipeline, one node at a time:

```
Source → QuickNote (gold standard)  →  Inference (arc:nano) → 
       → QuickNote (your score) → Evaluator (LLM judge) → Comparison Report
```

Don't worry if that looks like a lot. We add one node per stage and check it works before moving on.

> ## Why score before the judge runs?
> Your scores are most useful as a check on the judge — but only if you record them *without* seeing the judge's verdict first. Scoring after would mean you're really judging the judge, not the model. So you score blind, then the judge scores blind, and the comparison is honest.

> ## How this lesson works
> Each stage tells you **what you're trying to achieve and why** — then invites you to work out *how* on the canvas, including writing your own prompts. Have a real go first. If you get stuck, every tricky step has a **▸ Stuck? Reveal** box with a worked answer. Using it isn't cheating; working it out first is just where the learning happens.

---

## Stage 1 — Load the records onto the canvas

**Goal:** get a set of World Heritage Site records onto the canvas and confirm you can see their `description` text in a table.

Open the node sidebar and look in the *Source* group for a node that supplies sample data. Add it, choose the World Heritage Site package, and load it. Then add a **Table Output** node (in *Output*) and connect the two so you can see what loaded.

> ## Connecting nodes
> Nodes are joined by dragging from an **output handle** (the small circle on the *right* of a node) to an **input handle** (the circle on the *left* of the next). If a connection won't "take", check you started from the right-hand circle.

<details>
<summary>▸ Stuck? Reveal</summary>

1. In the sidebar, under *Source*, add a **Sample data** node.
2. Choose the **World Heritage Site** package and load the CSV.
3. Add a **Table Output** node from the *Output* group.
4. Drag from the Sample data node's right-hand handle to the Table Output's left-hand handle.

</details>

> ## Checkpoint
> The Table Output shows your records, with a `description` column containing the UNESCO text. If it's empty, re-check the connection and that the source actually loaded.

---

## Stage 2 — Run the model (the extraction)

**Goal:** get `arc:nano` to read each description and return four fields — place, period, site type, nation — as JSON in a new field called `inference_output`.

Add an **Inference** node and feed it from your source. Two settings to work out first:

- **Which model?** You want the *small, fast* one for this task — part of the point is to see how well a small model copes.
- **Should the output be repeatable?** An evaluation needs the model to give the *same* answer if you run it twice. There's a setting that controls randomness.

Then comes the part worth real thought: **the prompt itself.**

> ## Hints for writing the extraction prompt
> You want to extract the **place**, **site_type**, **period or date** and **nation**
> Before you reveal the answer, try drafting your own. A good extraction prompt usually does four things:
> 1. **States exactly what to extract**, by name. Don't say "the key facts" — name the four fields.
> 2. **Constrains the output format.** If you don't specify a shape (e.g. JSON with named keys), you'll get a paragraph back that's hard to read into other fields. Show the model the exact shape you want.
> 3. **Says what to do when something's missing.** Without this, a model will often guess or invent a value rather than admit it isn't there.
> 4. **Tells it to stick to the text.** A single line like "use only the text provided" is doing a lot of work — it stops the model filling gaps from its own general knowledge, which is exactly what you don't want in an extraction task.
>
> Try writing a prompt that does all four before you look below.

<details>
<summary>▸ Stuck? Reveal the prompt</summary>

```
Extract the following four fields from the site description. Use ONLY the
text provided. If a field is not stated, write "not stated".

Description: {{description}}

Respond as JSON:
{"place":"","period_or_date":"","site_type":"","nation":""}
```

</details>

> ## What is `{{description}}`?
> The double-brace token is a substitution placeholder. For each record, the tool swaps `{{description}}` for that record's actual description before sending it to the model. You'll use the same trick to point other nodes at other fields.

<details>
<summary>▸ Stuck? Reveal the node settings</summary>

- Model: **`arc:nano`** (the small one).
- Temperature: **0** (makes the output repeatable — run twice, get the same answer).
- Paste your prompt (or the one above).
- Output field: **`inference_output`**.
- Click **Run**.

</details>

> ## Checkpoint
> Connect a Table Output to the inference node. Each record now has an `kcl_response' (by default) or 'inference_output` field containing a small JSON object like `{"place":"Wiltshire","period_or_date":"prehistoric",...}`. The exact values don't matter yet — we just need the column to appear.

---

## Stage 3 — Write the gold standard

To judge the model's answer, we need to know what a **correct** answer looks like. You provide that — it's the "gold standard".

**Goal:** for each record, record the correct place / period / site type / nation, so there's something to judge the model against. We use the **QuickNote** node in **Structured** mode so you type plain values into boxes — the node builds the JSON for you, so you never type a brace or quote.

Add a **QuickNote** node after the inference node. Put it in **Structured** mode (the mode selector reads *Note · Structured · Score*). Configure the four fields once, point it at the right source field to read while you annotate, and choose where it writes.

Then work through the records. One rule that matters: **keep dates as the text states them.** If the description says "second half of the 19th century", write exactly that — *not* "1850".

> ## Why type it as the text says, not more precisely?
> If the description is vague, the correct answer is vague. Writing "1850" when the text only says "second half of the 19th century" invents precision. Later you'll see the model do exactly this — and your honest gold standard is what catches it.

<details>
<summary>▸ Stuck? Reveal the configuration</summary>

- Mode: **Structured** (the middle option).
- Fields: `place` (label "Place"), `period_or_date` ("Period / date"), `site_type` ("Site type"), `nation` ("Nation").
- Display field: **`description`** (so you can read the source while annotating).
- Target field: **`_note`**.
- Then fill the four boxes per record from the description.

</details>

> ## You don't have to annotate every record
> Even a handful of annotated records is enough to learn from. Records you leave un-annotated simply won't get scored later — that's fine.

> ## Checkpoint
> Each record you annotated has a `_note` field containing a clean JSON object with your four values. You never typed a brace or a quote — the node built the JSON for you.

---

## Stage 4 — Score it yourself

**Goal:** score each field of the model's output yourself, *before* the LLM judge has a go, so your scores are an independent check rather than a reaction to the judge's.

You'll score **four criteria**, one per extracted field — `place`, `period_or_date`, `site_type`, `nation` — each on a 0–2 scale. (You'll see in Stage 5 that the LLM judge gets the same four criteria. That's deliberate: human and judge must score the *same things on the same scale* or the comparison is meaningless.)

Add another **QuickNote** node after the gold-standard QuickNote, and set it to **Score** mode (the last of *Note · Structured · Score*). Configure the four criteria, point it at the model's output so you can see what you're scoring, and click through the records.

> ## Why score per field, not overall?
> A single "is this good" score hides which field broke. Per-field scoring tells you exactly where the model struggled — and it's the only way to see *patterns* of failure across the record set (e.g. "it always over-specifies dates").

<details>
<summary>▸ Stuck? Reveal the configuration</summary>

- Mode: **Score** (the last option).
- Criteria: `c1` place, `c2` period_or_date, `c3` site_type, `c4` nation — all scale **0, 1, 2**.
- Display field: **`inference_output`**.
- Target field: **`human_score`**.
- Click the buttons to score each record; optional one-line reason.

</details>

> ## You click; the tool structures it
> You never type JSON here. You click 0, 1 or 2 per field and the node records it cleanly. No malformed scores possible.

> ## Checkpoint
> Records you scored now carry a `human_score` with four field-level scores. The LLM judge will produce its own scores in the next stage, blind to yours.

---

## Stage 5 — Judge the model, field by field

**Goal:** have an LLM judge compare the model's `inference_output` against your `_note`, scoring the **same four fields** you just scored yourself — so the two sets of scores can be compared honestly.

Add an **Evaluator** node after the human-score QuickNote. Set the reference and candidate fields, and choose a judge model that **isn't** the one being judged.

> ## Which model should be the judge?
> Not the same one that produced the answer. A model marking its own work is biased toward liking it. `arc:nano` answered — so pick a *different* model to judge. `arc:nexus` is a good choice.

Now the rubric. The judge needs to score the same four criteria you just used in Stage 4, anchored the same way. Designing it well is harder than the inference prompt — think about the shape before you reveal it.

> ## Hints for writing the evaluation rubric
> A good judging rubric usually does five things:
> 1. **One criterion per thing you care about.** You just scored four fields by hand — the rubric should ask the judge to do the same. Don't collapse them into one "is this good?" score; that hides which field actually failed.
> 2. **Anchor every score.** Don't just say "score 0–2" — say what a 0, a 1, and a 2 each *mean* for that specific field. Without anchors, the judge invents its own meaning each time it runs, and your scores stop being repeatable.
> 3. **Ask for a reason before the score.** A model judges more carefully when it has to justify itself first, even briefly.
> 4. **Ground it in the two texts only.** Say so explicitly — "judge only using the texts provided, do not use outside knowledge" — or the judge may mark against what *it* thinks is true rather than against your annotation.
> 5. **Constrain the output to clean JSON.** Show the exact shape you want back, so the score can be read straight into the canvas.
>
> Try sketching your own four-field rubric using these five rules before you reveal the answer.

<details>
<summary>▸ Stuck? Reveal the rubric</summary>

```
You are scoring a model's field extraction against a human annotation.
Judge ONLY using the two texts provided. Do not use outside knowledge.
Length and fluency are not criteria.

HUMAN ANNOTATION (gold standard):
{{__reference}}

MODEL OUTPUT (to be judged):
{{__candidate}}

Score each criterion. Give a one-sentence reason, then the score.

c1 place: 2 = same place; 1 = correct but broader/narrower; 0 = wrong or missing

c2 period_or_date: 2 = same period; 1 = correct era, wrong specificity; 0 = wrong or missing

c3 site_type: 2 = same type; 1 = related but less/more specific; 0 = wrong or missing

c4 nation: 2 = same nation; 1 = related nation; 0 = wrong or missing

Respond with ONLY this JSON, no other text:
{"c1_reason":"","c1":0,"c2_reason":"","c2":0,"c3_reason":"","c3":0,"c4_reason":"","c4":0}
```

</details>

<details>
<summary>▸ Stuck? Reveal the node settings</summary>

- Reference field: **`_note`**.
- Candidate field: **`inference_output`**.
- Judge model: **`arc:nexus`** (different from the candidate).
- Paste the rubric.
- Temperature: **0** (should be fixed).
- Click **Judge**.

</details>

> ## Save your work now
> Save the workflow as a JSON file. This preserves every node's settings — **including your prompts** — so an accidental page reload won't lose them. Save again whenever you've edited a prompt. (Treat this as normal practice, not a chore — it's how you protect your work in any tool.)

> ## Checkpoint
> Each annotated record now has both `eval_c1`–`eval_c4` (the judge's scores) and `human_c1`–`human_c4` (yours, from Stage 4) — same fields, same scale, recorded independently. The next stage shows them side by side.

---

## Stage 6 — Read the results

**Goal:** see, per record and in summary, what the model extracted, how the judge scored each field, and how that compares to your own scoring.

Add a **Comparison Report** node after the human-score QuickNote. Map the source text, your note, the model's response, the judge's score, and your score to the right fields.

<details>
<summary>▸ Stuck? Reveal the mapping</summary>

- *original* → `description`
- *note* → `_note`
- *response* → `inference_output`
- *judge score* → the evaluator's score field
- *human score* → `human_score`

</details>

The report shows one **card per record** — source text, your gold standard, the model's answer, both sets of scores side by side — and a **summary** at the top across all your scored records.

> ## Checkpoint
> You can read, per record and per field, what the model extracted, how the judge scored it, and whether you agreed — and at a glance, how well `arc:nano` did overall.

---

## What to look for

> ## Discuss
> - **Which field broke?** With four separate scores, you can see exactly where the model struggled — was it always `period_or_date`, or scattered across all four? A low overall impression hides this; per-field scores don't.
> - **Did the model invent precision?** Look for records where the description was vague ("second half of the 19th century") but the model gave a specific year. That should show up as a low **c2** score, with the judge's reason explaining why.
> - **Stated vs inferred.** For Ironbridge, the text names Coalbrookdale but not the county. If the model added "Shropshire" — correct, but not *stated* — what does that do to the **c1 (place)** score? Is that good extraction, or outside knowledge creeping in? There's no single right answer; that's worth a conversation.
> - **Was the small model good enough?** For clear extraction from short text, `arc:nano` is often nearly as good as a much larger model.
> - **Did you agree with the judge?** Where your score and the judge's score diverged on a field, who was right?

> ## Stretch — try it on your own data
> Find another dataset on the canvas (or wire up a different source), and run the *same* pipeline on it. You'll need to write a new prompt and rubric suited to whatever fields make sense for the new records. What breaks? Does the model cope as well with messier or longer text? This is where you find out how robust the approach really is — and where the prompt-writing hints above earn their keep.

> ## Key points
> - An evaluation needs a **gold standard** — a human-made correct answer to compare against.
> - **Score per field, not overall** — a single holistic mark hides exactly which part of the task is failing.
> - A good prompt names what to extract, constrains the format, says what to do when something's missing, and tells the model to stick to the text.
> - A good rubric has one criterion per thing you care about, anchored scores, a reason before the number, and grounding in the texts provided.
> - **Temperature 0** makes both the model run and the judge repeatable.
> - The **judge** should be a different model from the one being judged.
> - For clear extraction tasks, **small models are often good enough** — and proving that is the point.
