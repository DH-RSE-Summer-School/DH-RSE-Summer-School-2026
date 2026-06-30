# Orientation — Nodes and Connections

> ## Objectives
> By the end of this lesson you will be able to:
> - Add nodes from the sidebar and using drag-out autosuggestion
> - Connect output handles to input handles to build a data pipeline
> - Use a Param node to supply a search term or API key
> - Explore results in a Table Output, including hidden columns
> - Run a language model over a text field to extract place names
> - Reconcile extracted place names against the Wikidata authority

> ## Prerequisites
> - The PoC canvas is open in your browser
> - You have a KCL API key (needed from Stage 4 onwards)

In this lesson you will build a pipeline that searches the ARIADNE European archaeological database, runs a language model over each site description to pull out the place names mentioned in it, then checks those place names against Wikidata to find their canonical identifiers. You will build this in three steps of increasing complexity — each step works on its own before you add the next layer.

```
Param (search term)
  → ADS ARIADNE Search
    → Table Output           ← check here after Stage 3
      → KingsInference by Field   ← extract place names (Stages 4–5)
        → Reconciliation           ← match to Wikidata (Stage 6)
          → Table Output           ← final enriched results
```

Don't worry if the full pipeline looks complicated. You add one node per stage, confirm it works, then continue.

---

## Stage 1 — Two ways to add nodes

**Goal:** add a node to the canvas and understand the two methods available, then clear the canvas and start fresh.

**Method 1 — the sidebar.** The left-hand panel lists every available node type in labelled groups: Source, Search, Process, Output, and so on. Click a group header to expand or collapse it. Click a node name once and it appears on the canvas near the centre; drag it to position it where you want.

**Method 2 — drag-out autosuggestion.** Once a node is on the canvas, drag from one of its output handles (the small circles on the right-hand edge) and release the mouse button on empty canvas space — not on another node. A menu of compatible node types appears at the point where you dropped. Pick one from the list and it lands on the canvas already connected to the node you dragged from.

The sidebar is best when you're browsing for something. Drag-out is faster when you already know the next node — you build the connection and create the node in one gesture.

> ## Reading the handles
> Every node has input handles (left edge) and output handles (right edge). Handles are named — hover over one to see its label appear as a tooltip. A connection only forms when you drag from an **output** to an **input**, not from input to output or from output to output. If a connection won't "take", check which end you started from.

Try adding a node each way now, then delete both before continuing.

---

## Stage 2 — First pipeline: a search and a table

**Goal:** build the simplest possible pipeline — one search node feeding one output — to practise connecting handles before adding more complexity.

Add a **GBIF Search** node from the *Search* group. The node has its own search box; type a term directly into it (try `mammoth`), then click **Search**. Now add a **Table Output** node from the *Output* group and connect the two.

<details>
<summary>▸ Stuck? Reveal</summary>

1. Sidebar → *Search* → click **GBIF Search**. It appears on the canvas.
2. Type `mammoth` in the node's search box and click **Search**. A record count appears at the bottom of the node.
3. Sidebar → *Output* → click **Table Output**.
4. Drag from the GBIF Search node's right-hand handle to the Table Output's left-hand handle. The table fills with records.

Alternatively, after Step 2, drag from the GBIF Search output handle onto empty canvas space and pick **Table Output** from the autosuggestion menu — it lands already connected.

</details>

> ## Checkpoint
> The Table Output shows occurrence records from GBIF. Columns include things like `gbif.species` and `gbif.country`. Notice that the source name prefixes every column — this namespacing stops fields from different data sources colliding when you combine results.

---

## Stage 3 — Param node and ARIADNE search

**Goal:** search the ARIADNE archaeological database using a search term stored in a **Param** node, then explore the results to find the `description` field.

A **Param** node holds a single value — a text string, a number, a URL — and supplies it to any node you connect it to. Storing the search term in a Param means you change it once in the Param, not inside the search node itself. You'll use the same pattern in the next stage for an API key.

Clear the canvas (or just leave the GBIF pipeline where it is and work below it). Add a **Param** node and an **ADS Advanced Search** node, connect Param → ADS Advanced Search, and then connect ADS Advanced Search → a new Table Output.

<details>
<summary>▸ Stuck? Reveal</summary>

1. Sidebar → *Canvas* (or *Input*) → **Param**. Set Type to *Text*, Value to `Stonehenge`.
2. Sidebar → *Search* → **ADS Advanced Search**.
3. Drag from Param's output handle to ADS Advanced Search's `query` input handle.
4. Connect ADS Advanced Search's output handle to a new **Table Output** node.
5. Click **Search** on the ADS Advanced Search node.

</details>

The results will appear in the table — but you may not see much beyond a title and some identifiers. Look at the Table Output toolbar: click **Show All columns**. A lot more columns appear. Scroll right to find the `description` column (it may appear as `ads.description` — try also enabling **Expand namespaces** in the toolbar to flatten namespace prefixes into plain column names).

> ## Why is `description` hidden by default?
> ARIADNE records carry many fields. By default Table Output shows a curated subset — the ones that are most commonly useful. **Show All columns** reveals everything, including fields nested inside namespace sub-objects like `ads`. The `description` field lives inside the `ads` namespace and is not shown by default. Enabling **Expand namespaces** additionally flattens `ads.description` into a plain `description` column that reads more cleanly in the table and in downstream node field pickers.

> ## Checkpoint
> You can see archaeological site records with a `description` column containing free text about each site. Read a few descriptions — the text varies in length and style. That variation is exactly what makes it interesting to run a language model over.

---

## Stage 4 — Add the inference node

**Goal:** run a language model over each `description` field to extract the place names mentioned in the text, writing the result into a new field on every record.

**KingsInference by Field** runs a model over one chosen field per record. You pick the field, write a prompt that references it with `{{value}}`, and the model processes every record in the pipeline.

Add a **KingsInference by Field** node. It has two input handles on the left — look at them before connecting anything:

- **`data`** — where your records come in (connect ADS Advanced Search here).
- **`key`** — where a Param node supplies your KCL API key.

> ## Connect the right handle to the right handle — this is the most common error
> The `data` handle expects a stream of records. The `key` handle expects a **Param node** containing your API key text, and nothing else.
>
> If you wire your record stream to `key`, the node will report an authentication error. If you wire your API key Param to `data`, you will send your key into the model as if it were a record to process.
>
> Before making either connection: read the handle label, identify what you're connecting, then connect deliberately.

Add a second **Param** node, set its Type to *Text*, and paste your KCL API key as its Value. Connect it to the **`key`** handle. Connect the ADS Advanced Search output to the **`data`** handle.

Now configure the inference node:

- **Field**: select `description` (or `ads.description` if that's what appears in the dropdown).
- **Model**: `arc:lite` is a good balance of speed and quality for this task.
- **Temperature**: 0 (makes the output repeatable — run twice, get the same results).
- **Output field**: `kcl_places`.

Then write the prompt. You want the model to return only the place names mentioned in the text — nothing else, no explanation, no interpretation.

> ## Hints for writing the prompt
> - Say explicitly that you want **only place names mentioned in the text** — not interpreted locations, not inferences about where a site might be. Only what is named.
> - Decide on a format for the output. A list with one name per line is easy to read and easy to pass to the Reconciliation node in the next stage. Prose is harder.
> - Use `{{value}}` where you want the description text to appear.
> - Say what to return if no places are named — otherwise the model will invent something to fill the gap.

<details>
<summary>▸ Stuck? Reveal the prompt</summary>

```
List every place name explicitly mentioned in the text below.
Return ONLY the place names, one per line, with no numbering,
no punctuation, and no explanation. If no places are named,
return the single word: none

Text: {{value}}
```

</details>

<details>
<summary>▸ Stuck? Reveal the node settings</summary>

- Field: **`description`** (or `ads.description`).
- Mode: **Per record** (the default — each record is processed individually).
- Model: **`arc:lite`**.
- Temperature: **0**.
- Output field: **`kcl_places`**.
- Click **Run**.

</details>

> ## Partial results
> KingsInference by Field writes each record's result as soon as the model finishes it. You will see the `kcl_places` column fill in one row at a time as the model works through your records — you do not need to wait for all records before checking a few results.

> ## Checkpoint
> Add a Table Output connected to the KingsInference by Field output. Each record now has a `kcl_places` field containing a list of place names, one per line, or the word "none". Read through a few records and compare the extracted names to the original description — are they accurate? Are there any obvious errors?

---

## Stage 5 — Save your workflow

> ## Save now, before adding the reconciliation node
> You have built something worth keeping. Use the **Save** button in the top toolbar to download the workflow as a JSON file. This preserves every node's settings — including your prompts and field selections. If you accidentally close or refresh the browser, a saved workflow restores everything in one click.
>
> Get into the habit of saving after completing each stage. It takes three seconds and has saved many hours of rework.

---

## Stage 6 — Reconcile place names against Wikidata

**Goal:** take the place names extracted by the model and match each one against the Wikidata knowledge base to get a canonical identifier — a QID — for each place.

**Reconciliation** sends each value to the Wikidata reconciliation service and returns the best-matching entity, with a label, a QID, and a confidence score. It works on a single field per record — so each row in `kcl_places` is sent as one candidate string.

> ## One candidate at a time
> The Reconciliation node processes one field value per record. If `kcl_places` contains several names (one per line), the reconciliation service will try to match the **whole string** as a single candidate — which usually produces a poor match. For a real pipeline over multi-value fields you would split the list first. For this exercise, watch what happens and notice which records reconcile cleanly (single-name outputs) versus which don't (multi-name outputs).

Add a **Reconciliation** node and connect the KingsInference by Field output to its `data` handle. Set:

- **Field to reconcile**: `kcl_places`.
- **Type** (optional): you can restrict matches to geographic items by entering `Q618123` — this tells the service to favour places over people, organisations, or other entities with similar names.

Click **Reconcile**, then add a final **Table Output** to inspect the results. Enable **Show All columns** in the table toolbar to see the full reconciliation data.

<details>
<summary>▸ Stuck? Reveal</summary>

1. Sidebar → *Process* → **Reconciliation**. Add it to the canvas.
2. Connect KingsInference by Field's output handle to Reconciliation's `data` handle.
3. In the Reconciliation node: Field = **`kcl_places`**, Type = `Q618123` (optional).
4. Click **Reconcile**.
5. Add a **Table Output** → connect Reconciliation's output to it. Enable **Show All columns**.

</details>

> ## Reading the reconciliation result
> The table now shows a `kcl_places_reconciled` column alongside the original `kcl_places`. The reconciled column contains:
> - **`_label`** — the canonical Wikidata name for the matched entity
> - **`_qid`** — the Wikidata identifier (e.g. `Q23240` for Stonehenge)
> - **`_confidence`** — the service's certainty about this match (0–1; above 0.8 is generally reliable)
> - **`_status`** — whether the match was accepted automatically or flagged for review

> ## Checkpoint
> Your complete pipeline runs end to end: ARIADNE records → model extracts place names from each description → Wikidata matches each name to a canonical entity. Records where the model extracted a clean single place name should show high-confidence reconciliation matches; records with long multi-name outputs or "none" will show low confidence or no match.

---

## What to look for

> ## Discuss
> - **Where did the model do well?** Look at records with short, clearly named places. How accurate is the extraction?
> - **Where did it struggle?** Long descriptions with many named places, or places referred to only by region or era ("the Thames Valley", "the Midlands"), tend to produce messier outputs. What does the reconciliation do with those?
> - **High confidence vs low confidence.** Sort the table by `_confidence` (if your table supports it). What distinguishes the records the reconciliation service was sure about from the ones it wasn't?
> - **The `none` records.** Some descriptions genuinely name no places. Others name places that are too obscure for Wikidata to match. How would you tell the difference?
> - **What would you do next?** If you wanted to export these enriched records — description, extracted names, Wikidata QIDs — to a spreadsheet or JSON file, which Output node would you add?

> ## Key points
> - **Two ways to add nodes**: sidebar (browse all available types) or drag from an output handle onto empty canvas (autosuggestion, creates a node already connected).
> - **Handles are labelled**: output on the right, input on the left; hover to see the name. Always read both labels before connecting.
> - **Param nodes** hold a single value and supply it wherever you connect them — search terms, API keys, numeric thresholds.
> - **Show All columns** in Table Output reveals fields hidden by default, including namespace sub-objects like `ads.description`. **Expand namespaces** flattens those into plain column names.
> - **KingsInference by Field** runs per record and writes partial results live — you see the column fill in as the model works.
> - The `data` handle takes records; the `key` handle takes an API key Param — connecting them the wrong way is the most common error on inference nodes.
> - **Reconciliation** expects one value per record; multi-value strings reconcile poorly.
> - **Save your workflow** after each stage — it takes three seconds and preserves every node's settings including prompts.
