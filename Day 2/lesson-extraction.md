# Evaluation 1 — Extraction

> ## Objectives
> By the end of this lesson you will be able to:
> - Load a set of records into the workflow canvas
> - Run a small language model to extract structured fields from text
> - Record a human "gold standard" answer for each record
> - Have an LLM judge score the model's extraction against your gold standard
> - Read the results in a comparison report

> ## Prerequisites
> - You have completed PoC orientation (placing and connecting nodes)
> - The PoC canvas is open in your browser

In this evaluation you will test how well a small model (`arc:nano`) pulls **structured facts** out of a short description — the place, the period, the site type, and the nation. Because the answers sit plainly in the text, this is a task a non-expert can check. That makes it a good first evaluation: the "right answer" is not very contestable, so you can focus on learning the workflow.

We will build this pipeline, one node at a time:

```
Source  →  Inference (arc:nano)  →  QuickNote (gold standard)  →  Evaluator  →  Comparison Report
```

Don't worry if that looks like a lot. We add one node per stage and check it works before moving on.

> ## How this lesson works
> Each stage tells you **what you're trying to achieve and why** — then invites you to work out *how* on the canvas. Have a real go first. If you get stuck, every tricky step has a **▸ Stuck? Reveal** box with the exact answer. Using it isn't cheating; working it out first is just where the learning happens.

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

**Goal:** get `arc:nano` to read each description and return the four fields — place, period, site type, nation — as JSON in a new field called `inference_output`.

Add an **Inference** node and feed it from your source. Two decisions matter here, and they're worth thinking about rather than being told:

- **Which model?** You want the *small, fast* one for this task — part of the point is to see how well a small model copes. Which of the ARC models is the small one?
- **Should the output be repeatable?** An evaluation needs the model to give the *same* answer if you run it twice. There's a setting that controls randomness — what should it be for repeatability?

Then give it the prompt below, and set the output field.

The prompt to paste:

```
Extract the following four fields from the site description. Use ONLY the
text provided. If a field is not stated, write "not stated".

Description: {{description}}

Respond as JSON:
{"place":"","period_or_date":"","site_type":"","nation":""}
```

> ## What is `{{description}}`?
> The double-brace token is a substitution placeholder. For each record, the tool swaps `{{description}}` for that record's actual description before sending it to the model. You'll use the same trick to point other nodes at other fields.

<details>
<summary>▸ Stuck? Reveal the settings</summary>

- Model: **`arc:nano`** (the small one).
- Temperature: **0** (makes the output repeatable — run twice, get the same answer).
- Paste the prompt above.
- Output field: **`inference_output`**.
- Click **Run**.

</details>

> ## Checkpoint
> Connect a Table Output to the inference node. Each record now has an `inference_output` field containing a small JSON object like `{"place":"Wiltshire","period_or_date":"prehistoric",...}`. The exact values don't matter yet — we just need the column to appear.

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
> Even a handful of annotated records is enough to learn from. Records you leave un-annotated simply won't get a quality score — that's fine.

> ## Checkpoint
> Each record you annotated has a `_note` field containing a clean JSON object with your four values. You never typed a brace or a quote — the node built the JSON for you.

---

## Stage 4 — Judge the model against the gold standard

**Goal:** have an LLM judge compare the model's `inference_output` against your `_note` gold standard and score it.

Add an **Evaluator** node after the QuickNote. You need to tell it which field is the *reference* (your gold standard) and which is the *candidate* (the thing being judged), and choose a judge model.

One decision worth pausing on: **which model should judge?**

> ## Which model should be the judge?
> Not the same one that produced the answer. A model marking its own work is biased toward liking it. `arc:nano` answered — so pick a *different* model to judge. `arc:nexus` is a good choice.

Then paste the rubric below.

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

<details>
<summary>▸ Stuck? Reveal the settings</summary>

- Reference field: **`_note`**.
- Candidate field: **`inference_output`**.
- Judge model: **`arc:nexus`** (different from the candidate).
- Paste the rubric above.
- Temperature: **0** (should be fixed).
- Click **Judge**.

</details>

> ## Save your work now
> Save the workflow as a JSON file. This preserves every node's settings — **including your pasted prompts** — so an accidental page reload won't lose them. Save again whenever you've edited a prompt. (Treat this as normal practice, not a chore — it's how you protect your work in any tool.)

> ## Checkpoint
> Each annotated record now has evaluation scores (`eval_c1`, `eval_c2`, or similar). Records you didn't annotate are marked as not scored, rather than getting a made-up score.

---

## Stage 5 — Read the results

**Goal:** see, per record and in summary, what the model extracted and how the judge scored it.

Add a **Comparison Report** node after the Evaluator. It needs to know which field plays which role in the report — map the source text, your note, the model's response, and the judge's score to the right fields.

<details>
<summary>▸ Stuck? Reveal the mapping</summary>

- *original* → `description`
- *note* → `_note`
- *response* → `inference_output`
- *judge score* → the evaluator's score field

</details>

The report shows one **card per record** — source text, your gold standard, the model's answer, the judge's score — and a **summary** at the top across all your scored records.

> ## Checkpoint
> You can read, per record, what the model extracted and how the judge scored it — and at a glance, how well `arc:nano` did overall.

---

## What to look for

> ## Discuss
> - **Did the model invent precision?** Look for records where the description was vague ("second half of the 19th century") but the model gave a specific year. The *fabrication* score should catch this.
> - **Stated vs inferred.** For Ironbridge, the text names Coalbrookdale but not the county. If the model added "Shropshire" — correct, but not *stated* — is that good extraction or outside knowledge creeping in? There's no single right answer; that's worth a conversation.
> - **Was the small model good enough?** For clear extraction from short text, `arc:nano` is often nearly as good as a much larger model.

> ## Stretch — try it on your own data
> Find another dataset on the canvas (or wire up a different source), and run the *same* pipeline on it. You'll need to adjust the fields to whatever suits the new records. What breaks? Does the model cope as well with messier or longer text? This is where you find out how robust the approach really is.

> ## Key points
> - An evaluation needs a **gold standard** — a human-made correct answer to compare against.
> - **Temperature 0** makes both the model run and the judge repeatable.
> - The **judge** should be a different model from the one being judged.
> - For clear extraction tasks, **small models are often good enough** — and proving that is the point.
