# Evaluation 1 — Extraction

> ## Objectives
> By the end of this lesson you will be able to:
> - Load a set of records into the workflow canvas
> - Run a small language model to extract structured fields from text
> - Record a human "gold standard" answer for each record
> - Have an LLM judge score the model's extraction against your gold standard
> - Read the results in a comparison report
{: .objectives}

> ## Prerequisites
> - You have completed Workshop 1 (placing and connecting nodes)
> - The PoC canvas is open in your browser
> - You have access to the ARC platform
> - You have the file `uk_whs.csv` (see Stage 1)
{: .prereq}

In this evaluation you will test how well a small model (`arc:nano`) pulls **structured facts** out of a short description — the place, the period, the site type, and the nation. Because the answers sit plainly in the text, this is a task a non-expert can check. That makes it a good first evaluation: the "right answer" is not very contestable, so you can focus on learning the workflow.

We will build this pipeline, one node at a time:

```
Source  →  Inference (arc:nano)  →  QuickNote (gold standard)  →  Evaluator  →  Comparison Report
```

Don't worry if that looks like a lot. We add one node per stage and check it works before moving on.

---

## Stage 1 — Get the data

We use the official short descriptions of UK World Heritage Sites, published by UNESCO.

1. In a web browser, open: `https://whc.unesco.org/en/list/xml`
2. Save the page as a file called `whs.xml`.
3. Run the small filter script (provided separately in the repo as `make_whs_csv.py`) in the same folder. It keeps only the UK rows and writes `uk_whs.csv`.
4. Open `uk_whs.csv` and **delete the three "series" rows** if present — *Frontiers of the Roman Empire*, *The Great Spa Towns of Europe*, and *Moravian Church Settlements*. These describe multi-country groupings rather than single places, so they don't suit the task.

> ## Why not just fetch it in the tool?
> UNESCO blocks automated downloads, so we grab the file by hand once. This is normal — plenty of useful data needs a manual download. You only do this step once.
{: .callout}

> ## Checkpoint
> You should now have a file `uk_whs.csv` with about 32 rows, each having a `name` and a `description`.
{: .checkpoint}

---

## Stage 2 — Load the records onto the canvas

1. On the canvas, open the node sidebar.
2. Add a **Local Folder source** node (under *Source*).
3. Click its button to choose a folder, and select the folder containing `uk_whs.csv`.
4. The node should report how many records it has loaded.

> ## Tip — a quick alternative for a small demo
> If selecting a folder is fiddly, you can instead add a few **Param** nodes and paste one record's description into each. For learning the workflow, three or four records is plenty.
{: .callout}

5. Add a **Table Output** node (under *Output*).
6. Connect the source node's output handle to the Table Output's input handle (drag from the small circle on the right of the source to the circle on the left of the table).

> ## Connecting nodes
> Nodes are joined by dragging from an **output handle** (right side of a node) to an **input handle** (left side of the next). If a connection won't "take", check you started from the right-hand circle.
{: .callout}

> ## Checkpoint
> The Table Output should now show your records, with a `description` column containing the UNESCO text. If the table is empty, re-check the connection and that the source actually loaded the file.
{: .checkpoint}

---

## Stage 3 — Run the model (the extraction)

Now we ask `arc:nano` to extract four fields from each description.

1. Add an **Inference** node (the ARC/KCL inference node).
2. Connect the **source** node's output to the inference node's input.
3. Set the model to **`arc:nano`**.
4. Set the **temperature to 0**. This makes the model's output repeatable — run it twice and you get the same answer, which an evaluation needs.
5. In the prompt box, paste:

```
Extract the following four fields from the site description. Use ONLY the
text provided. If a field is not stated, write "not stated".

Description: {{description}}

Respond as JSON:
{"place":"","period_or_date":"","site_type":"","nation":""}
```

6. Set the **output field** to `inference_output`.
7. Click **Run**.

> ## What is `{{description}}`?
> The double-brace token is a placeholder. For each record, the tool swaps `{{description}}` for that record's actual description before sending it to the model. You'll use the same trick to point other nodes at other fields.
{: .callout}

> ## Checkpoint
> Connect a Table Output to the inference node (or reuse the one from Stage 2). Each record should now have an `inference_output` field containing a small JSON object like `{"place":"Wiltshire","period_or_date":"prehistoric",...}`. The exact values don't matter yet — we just need the column to appear.
{: .checkpoint}

---

## Stage 4 — Write the gold standard

The model has produced an answer. To judge it, we need to know what a **correct** answer looks like. You provide that — it's the "gold standard". We use the **QuickNote** node in **Structured** mode so you can type plain values into boxes instead of writing JSON yourself.

1. Add a **QuickNote** node.
2. Connect the **inference** node's output to it.
3. Set its mode to **Structured** (the mode selector reads *Note · Structured · Score* — choose the middle one).
4. Configure the fields once, to match the extraction schema:
   - `place` — label "Place"
   - `period_or_date` — label "Period / date"
   - `site_type` — label "Site type"
   - `nation` — label "Nation"
5. Set the **display field** to `description` (so you can read the source while you annotate).
6. Set the **target field** to `_note`.
7. Now go through each record and fill the four boxes from the description. Keep dates **as the text states them** — if it says "second half of the 19th century", write exactly that, *not* "1850".

> ## Why type it as the text says, not more precisely?
> If the description is vague, the correct answer is vague. Writing "1850" when the text only says "second half of the 19th century" invents precision. Later you'll see the model do exactly this — and your honest gold standard is what catches it.
{: .callout}

> ## You don't have to annotate every record
> Even a handful of annotated records is enough to learn from. Records you leave un-annotated simply won't get a quality score — that's fine.
{: .callout}

> ## Checkpoint
> Each record you annotated now has a `_note` field containing a clean JSON object with your four values. You never typed a brace or a quote — the node built the JSON for you.
{: .checkpoint}

---

## Stage 5 — Judge the model against the gold standard

Now an LLM judge compares the model's `inference_output` against your `_note` and scores it.

1. Add an **Evaluator** node.
2. Connect the **QuickNote** node's output to it.
3. Set the **Reference** field to `_note` (your gold standard).
4. Set the **Candidate** field to `inference_output` (the model's answer).
5. Set the **judge model** to **`arc:nexus`**.

> ## Why a different model for the judge?
> The judge should not be the same model that produced the answer — a model marking its own work is biased toward liking it. `arc:nano` answered, so `arc:nexus` judges.
{: .callout}

6. In the rubric box, paste:

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

7. Check the temperature is **0** (it should be fixed).
8. Click **Judge**.

> ## Save your work now
> Save the workflow as a JSON file (export/save). This preserves every node's settings — **including your pasted prompts** — so an accidental page reload won't lose them. Save again whenever you've edited a prompt.
{: .callout}

> ## Checkpoint
> Each annotated record now has evaluation scores (`eval_c1`, `eval_c2`, or similar). Records you didn't annotate are marked as not scored rather than getting a made-up score.
{: .checkpoint}

---

## Stage 6 — Read the results

1. Add a **Comparison Report** node.
2. Connect the **Evaluator** node's output to it.
3. Map the report's columns to your fields:
   - *original* → `description`
   - *note* → `_note`
   - *response* → `inference_output`
   - *judge score* → the evaluator's score field
4. The report shows one **card per record**: the source text, your gold standard, the model's answer, and the judge's score, laid out to read.
5. At the top, a **summary** shows how the model did across all your scored records.

> ## Checkpoint
> You can now read, per record, what the model extracted and how the judge scored it — and at a glance, how well `arc:nano` did overall.
{: .checkpoint}

---

## What to look for

> ## Discuss
> - **Did the model invent precision?** Look for records where the description was vague ("second half of the 19th century") but the model gave a specific year. The *fabrication* score should catch this.
> - **Stated vs inferred.** For Ironbridge, the text names Coalbrookdale but not the county. If the model added "Shropshire" — correct, but not *stated* — is that good extraction or outside knowledge creeping in? There's no single right answer; that's worth a conversation.
> - **Was the small model good enough?** For clear extraction from short text, `arc:nano` is often nearly as good as a much larger model. If so — that's a real and useful finding, not a boring one.
{: .discussion}

> ## Optional — compare two models
> Repeat Stage 3 with the model set to `arc:apex`, writing to a new field `inference_output_apex`, and add a second Evaluator and Comparison Report for it. Now compare: did the big model actually do better on this task? Often, for extraction, it barely does.
{: .challenge}

> ## Key points
> - An evaluation needs a **gold standard** — a human-made correct answer to compare against.
> - **Temperature 0** makes both the model run and the judge repeatable.
> - The **judge** should be a different model from the one being judged.
> - For clear extraction tasks, **small models are often good enough** — and proving that is the point.
{: .keypoints}
