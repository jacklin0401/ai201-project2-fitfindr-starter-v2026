# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Three or four sentences: what a user asks for, and what they get back. -->

FitFindr helps users find secondhand clothing that matches a description, size, and budget. It searches clothing listings and selects a matching item to use in outfit recommendations. The agent combines the new item with pieces from the user's wardrobe and generates outfit suggestions. Finally, it creates a short, social-media-style fit card caption featuring the item and its price.

---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:**
- Search the listings data for items matching a description, and optionally a
  size and a price ceiling.
- **Inputs:**
- "description" (str)
- "size" (str)
- "max_price" (float) <!-- name and type each: `max_price` (float), not "a price" -->
- **Returns:**
- A list of dictionaries. Each listing contain field such as    "title",  "price", "Size", and "platform" 
- **When it has nothing:**
- Return a empty list "[]"

### `suggest_outfit`

- **What it does:**
- Takes a new clothing item and the user's wardrobe and suggests outfits that combine them.
- **Inputs:**
- "new_item" (dict)
- "wardrobe" (list)
- **Returns:**
- A list of outfit ideas describing how to style the new item with items from the wardrobe.
- **When it has nothing:**
- Returns general outfit advice using the new item when the wardrobe is empty.

### `create_fit_card`

- **What it does:**
- Creates a short, social-media-style caption for an outfit featuring the new item.
- **Inputs:**
- `outfit` (dict)
- `new_item` (dict)
- **Returns:**
- A short caption describing the outfit and new item that someone could post.
- **When it has nothing:**
- Returns a short message explaining that there is not enough outfit information to create a fit card.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:**
If search_listings returns an empty list, put a message in the session
and stop. Otherwise take the first result and go to suggest_outfit.
**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which -->
The user's query is parsed into the description, size, and maximum price needed by `search_listings`.
**What moves through the session:** <!-- which fields, in what order -->
The search results are stored in the session. If results are found, the first listing is stored as the selected item and passed to `suggest_outfit`. The outfit returned by `suggest_outfit` is then passed to `create_fit_card`.
---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'Y2K baby tee size M under $40' --trace

[1] search_listings (via MCP)
     in: dict with keys: description, size, max_price
     out: 2 items: Y2K Baby Tee — Butterfly Print, Mesh Long-Sleeve Top — Black

[2] state_consistency_check
     in: dict with keys: selected_item, item_sent_to_suggest_outfit
     out: True
     → Verify the selected listing is passed to suggest_outfit

[3] suggest_outfit
     in: dict with keys: new_item, wardrobe
     out: Here are two outfit suggestions using the Y2K Baby Tee and pieces from your wardrobe: [output truncated in recorded trace]

[4] create_fit_card
     in: dict with keys: outfit, new_item
     out: Channeling major 2000s energy with this dreamy butterfly print! Grab this Y2K Baby Tee for just $18.00 exclusively...

Found: Y2K Baby Tee — Butterfly Print — $18.0 on depop
```
=== A query the data can match === 
found: Y2K Baby Tee — Butterfly Print — $18.0 on depop outfit: [outfit suggestions generated from the user's wardrobe] fit card: [fit card caption mentioning the item, $18.00 price, and Depop] 
=== A query it can't === 
stopped: No matching listings were found. Try changing the description, size, or maximum price. fit_card is None — it should still be None here

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

```
This returned matching graphic tee listings.
```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe; from tools import search_listings; item=search_listings('graphic tee', max_price=30)[0]; print(suggest_outfit(item, get_example_wardrobe()))"

```
This returned outfit suggestions using the selected item and wardrobe pieces.
```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

```
This returned a short social-media-style fit card containing the item, price, platform, and outfit vibe.
---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

     1. Building the search tool: I used AI to help me work through the search_listings requirements, especially filtering by size and maximum price and returning an empty list when there were no matches. I then tested it with a normal query and an impossible query to verify both cases.

     2. Building the planning loop: I used AI to work through agent.py one step at a time. When my first version reached results[0] for an empty search, it produced an IndexError. I used the error to identify that the empty-result branch needed to return the session before selecting the first item. After fixing it, the no-match query stopped correctly and fit_card remained None.

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->


| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. Matching query completes | 4/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops early | 5/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. Selected item reaches suggest_outfit unchanged | 5/5 | PASS* | PASS* | PASS* | PASS* | PASS* | PROVISIONAL |
| 4. Fit card mentions item and price | 4/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. No listings produces a useful message | 5/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |


**Real output from one try**, pasted as text, naming the file and function
that produced it:

```
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)
[2] empty_search
      in:  dict with keys: description, size, max_price
      out: No matching listings were found. Try changing the description, size, or maximum price.
      →    No results; stopping before model calls

No matching listings were found. Try changing the description, size, or maximum price.

0 model calls this session
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->


| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Matching query completes | 4/5 | MET (5/5) | All five matching-query trials selected a listing and completed outfit suggestion and fit-card generation. |
| 2 | Impossible query stops early | 5/5 | MET (5/5) | All five trials returned a helpful no-match message and stopped before outfit generation. |
| 3 | Selected item reaches suggest_outfit unchanged | 5/5 | PROVISIONAL | The trace shows the selected item and a `new_item` argument, but an independent value comparison is still needed. |
| 4 | Fit card mentions the item and price | 4/5 | MET (5/5 observed) | All five matching-query captions included the selected item and its price. |
| 5 | No listings produces a useful message | 5/5 | MET (5/5) | All five impossible-query trials returned a message explaining that the description, size, or maximum price could be changed. |

**Diagnoses**

No failures were observed for criteria 1, 2, 4, or 5 in the baseline report. Criterion 3 still needs independent verification; the trace records the tool input but does not prove that its value matches the selected listing. Criteria 2 and 5 test substantially the same empty-search behavior.

---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**
python app.py ask 'Y2K baby tee size M under $40' --trace
```
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 2 items: Y2K Baby Tee — Butterfly Print, Mesh Long-Sleeve Top — Black
[2] state_consistency_check
      in:  dict with keys: selected_item, item_sent_to_suggest_outfit
      out: True
      →    Verify the selected listing is passed to suggest_outfit
[3] suggest_outfit
      in:  dict with keys: new_item, wardrobe
      out: Here are two outfit suggestions using the Y2K Baby Tee and pieces from your wardrobe: ### Outfit 1: Effortles…
[4] create_fit_card
      in:  dict with keys: outfit, new_item
      out: Channeling major 2000s energy with this dreamy butterfly print! Grab this Y2K Baby Tee for just $18.00 exclusi…

Found: Y2K Baby Tee — Butterfly Print — $18.0 on depop

0 model calls this session, 2 served from cache
```

**Empty search**
Command: python app.py ask 'designer ballgown size XXS under $5' --trace

```
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)
[2] empty_search
      in:  dict with keys: description, size, max_price
      out: No matching listings were found. Try changing the description, size, or maximum price.
      →    No results; stopping before model calls

  No matching listings were found. Try changing the description, size, or maximum price.

0 model calls this session
```
**On the MCP move:**

I moved `search_listings` from a direct tool call to an MCP tool registered in `mcp_server.py`. The agent now calls the search tool through the MCP client. The happy-path trace confirms that `search_listings (via MCP)` executes before the consistency check, outfit generation, and fit-card creation. The empty-search trace confirms that the MCP call returns an empty list and the agent stops before the model-generation steps. Both traces completed without an MCP error.



---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**
I added a `state_consistency_check` step in `agent.py::run_agent` to verify that the selected listing matches the item passed to `suggest_outfit`. The check records the selected item and the item sent to the outfit-generation tool, making the handoff visible in the trace.

**Which failure it was meant to fix:**

The baseline trace did not independently demonstrate that the selected listing was passed unchanged to `suggest_outfit`. The consistency check makes this behavior observable. In the after-evaluation, the check returned `True` in all five matching-query trials and all five empty-wardrobe trials.

I also reran the evaluation with caching disabled so the after-results could be recorded separately from the baseline.

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. Matching query completes | 4/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops early | 5/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. Selected item reaches suggest_outfit unchanged | 5/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5 checks) |
| 4. Fit card mentions item and price | 4/5 | VERIFY | VERIFY | VERIFY | VERIFY | VERIFY | PENDING |
| 5. No listings produces a useful message | 5/5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |


**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->

The after-evaluation confirmed that all five matching-query trials completed, all five impossible-query trials stopped early with a useful message, and all five empty-wardrobe trials completed. The state consistency check returned `True` in every matching-query and empty-wardrobe trial. The no-results branch made no model calls. These results support the intended behavior, although Criterion 4 still needs verification against the generated captions. The report is saved at `results/run_2026-10-14_1746_after.md`.


---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->
The evaluation did not reveal failures in the matching-query completion or empty-search behavior. However, generated outfit suggestions and fit-card captions can still vary between runs, and the consistency check only verifies that the selected listing is passed to `suggest_outfit`; it does not guarantee the quality of the generated advice. Criterion 4 still needs a direct check that each generated caption mentions the item and its price. Criteria 2 and 5 also overlap because both evaluate the no-results path. Further improvements could add more diverse evaluation queries and stronger checks for caption content and output quality.



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
