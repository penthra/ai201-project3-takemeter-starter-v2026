# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

This project aims to classify public text posts from
[r/CasualConversation](https://www.reddit.com/r/CasualConversation/), a community
where people discuss everyday experiences, interests, and opinions. It uses
200 original posts, with each title and body treated as one example and replies
excluded. The three categories are `personal_experience` for sharing personal
experiences or feelings, `discussion_request` for seeking answers or
participation, and `viewpoint` for presenting or explaining a broader idea.
The goal is to identify the main purpose of a post, including difficult cases
such as a personal story that ends with a question.

---

## Label Taxonomy

### `personal_experience`

**Definition:** Mainly shares the writer's experiences, personal circumstances,
or feelings; a generic closing invitation does not override a self-contained
personal account.

**Example 1:** [Making a burger for the first time](https://www.reddit.com/r/CasualConversation/comments/1x3r7ar/i_just_made_a_burger_for_the_first_time_and_it/)
> It was super juicy and flavorful and the crust was impeccable.

The post describes buying ingredients, cooking, and enjoying the result.

**Example 2:** [Hearing a cat purr after getting hearing aids](https://www.reddit.com/r/CasualConversation/comments/fkkdz5/got_hearing_aids_today_and_cried_because_my_cat/)
> purring isn't just vibrating! It's a sound! I can hear it!

The writer shares a new experience and the emotions it caused.

### `discussion_request`

**Definition:** Mainly asks readers for answers, stories, advice, judgments,
recommendations, or participation; a specific unresolved request can outweigh
a long background story.

**Example 1:** [Planning something for coworkers on Friday the 13th](https://www.reddit.com/r/CasualConversation/comments/1wsx86x/november_has_a_friday_the_13th_this_year_whats/)
> I'm looking for something fun (but not too expensive/time intensive) I can bring in.

The writer wants suggestions that fit practical constraints, including a
coworker's allergy.

**Example 2:** [Looking for a new hobby](https://www.reddit.com/r/CasualConversation/comments/1t4as9v/i_have_gotta_find_a_new_hobby_any_suggestions/)
> I have gotta find a new hobby, any suggestions?

The writer describes existing hobbies and asks for affordable alternatives.

### `viewpoint`

**Definition:** Mainly presents or explains a broader opinion, observation,
recommendation, or idea; a brief moral at the end of a personal story is not
enough.

**Example 1:** [Explaining when to use “and me” or “and I”](https://www.reddit.com/r/CasualConversation/comments/1wjox0q/a_simple_trick_for_knowing_when_to_say_and_me/)
> Just remove the other person's name from the sentence in your mind.

The writer develops an explanation with worked examples.

**Example 2:** [Using a wrong-train metaphor for life decisions](https://www.reddit.com/r/CasualConversation/comments/1wsv6wh/even_if_you_paid_a_lot_for_the_ticket_once_you/)
> But staying longer doesn't make the destination any better.

The writer explains a broader idea about staying in situations because of
past investment.

### The hardest boundary

**Which two labels:** `personal_experience` and `discussion_request`.

**The decision rule for this labeling pass:** A specific unresolved request for
advice, judgment, recommendations, or participation is `discussion_request`,
even when the background story is long. A completed personal account stays
`personal_experience` when “anyone else?” is mainly a closing invitation.
A question mark alone does not determine the label.


---

## The Dataset

**Where the posts came from:** The public
[r/CasualConversation community](https://www.reddit.com/r/CasualConversation/).
The dataset contains 200 original submissions, with each complete collected
title and body combined into one example. Replies are excluded. Public pages
were found through recent and ranked feeds and related-post links within the
same community. This is a convenience sample with popularity, recommendation,
and accessibility bias, not a random sample of the whole community. Some pages
were cached, and the text was collected in rendered form rather than raw
Markdown. Three entries containing removal or moderation notices were replaced
with other public posts from the same community.

**How the posts were labeled:** I labeled 20 posts myself. For the remaining
180 posts, artificial intelligence helped summarize each post's meaning and
main focus. I then assigned the labels based on those summaries and the label
definitions. All 200 rows in `labels.csv` have a label and a labeling reason
in the `note` column. Notes explain the category decision; this section
records the assistance used.

**Dataset file:** `labels.csv` contains one unsplit collection with exactly the
columns `text`, `label`, and `note`. Complete post texts are preserved. The
notebook creates the training, validation, and test groups and saves the
held-out test posts in `test_split.csv` for later evaluation. The source
dataset remains in one unsplit file, `labels.csv`.

**Current label counts:**

| Label | Count | Share |
|---|---|---|
| `personal_experience` | 96 | 48.0% |
| `discussion_request` | 81 | 40.5% |
| `viewpoint` | 23 | 11.5% |
| **Total** | **200** | **100%** |

All 200 rows have labels. No category exceeds 70%, and all source post
identifiers and normalized texts are unique. The viewpoint category has only
23 examples, so its test results depend heavily on a few posts.

**Three hard cases and the reasons for their labels**

**1. A burger story ending with a question**
> *The post:* [I just made a burger for the first time and it was the best I ever had](https://www.reddit.com/r/CasualConversation/comments/1x3r7ar/i_just_made_a_burger_for_the_first_time_and_it/).
>
> *Could have been:* `personal_experience` or `discussion_request`.
>
> *Current decision:* `personal_experience`, because the writer develops a
> complete account of buying ingredients, cooking, and enjoying the result.
> The closing question invites conversation after the main story.

**2. A long story asking whether refusing help was wrong**
> *The post:* [Did I do the wrong thing?](https://www.reddit.com/r/CasualConversation/comments/1x04s65/did_i_do_the_wrong_thing/).
>
> *Could have been:* `personal_experience` or `discussion_request`.
>
> *Current decision:* `discussion_request`, because the writer wants readers
> to judge an unresolved decision. The detailed account of refusing a jump-start
> explains the situation readers are being asked to assess.

**3. Returning a piece of coral after learning about its role**
> *The post:* [Learned a lesson about beachcombing today—taking my ocean find back to where it belongs](https://www.reddit.com/r/CasualConversation/comments/1wt8nya/learned_a_lesson_about_beachcombing_todaytaking/).
>
> *Could have been:* `personal_experience` or `viewpoint`.
>
> *Current decision:* `personal_experience`, because the main structure follows
> finding coral, wanting to keep it, learning more, and deciding to return it.
> The ecological explanation supports a personal change of mind rather than
> being developed as a separate general argument.


---

## The Training Run

**Base model:** `distilbert-base-uncased`, fine-tuned to classify posts as
`personal_experience`, `discussion_request`, or `viewpoint`.

**Settings:** 3 epochs, learning rate `2e-5`, batch size 16, random seed 42,
and maximum input length 128 tokens. Training ran on CPU.

**Anything I changed from the defaults, and why:** I kept the default base
model and training settings for this initial run. I set the notebook's label
list to match the three categories in my dataset.

**Split sizes:** The notebook used a stratified split targeting 70% training,
15% validation, and 15% test. The actual split contained 139 training posts,
31 validation posts, and 30 test posts.

| Label | Train | Validation | Test |
|---|---|---|---|
| `personal_experience` | 67 | 15 | 14 |
| `discussion_request` | 56 | 13 | 12 |
| `viewpoint` | 16 | 3 | 4 |
| **Total** | **139** | **31** | **30** |

The `viewpoint` category has only four test posts, fewer than eight. A single
mistake can therefore change its score substantially, and its results may
vary considerably across random seeds.



---

## How I Used AI

**Moment 1**

- *What I asked for:* I chose r/CasualConversation as the source and asked AI
  to write code to collect posts and save them in a file. I also asked for
  summaries of the posts to help me understand their content.
- *What came back:* Code for collecting and saving the posts, a dataset file,
  and summaries of the posts' main ideas.
- *What I changed:* I determined the source and directed the collection task.
  I used AI mainly to help with data gathering and organizing the collected
  text, with the summaries supporting my understanding of the posts.

**Moment 2**

- *What I asked for:* I proposed labels and asked AI to check whether they
  fit the posts. When a post was difficult to distinguish between categories,
  I asked AI to analyze its meaning and main focus.
- *What came back:* Feedback on how well the proposed labels fit the posts
  and explanations of why difficult cases could fit more than one category.
- *What I changed:* I used that feedback to make my labeling decisions,
  considering the post's main purpose and the category definitions when
  choosing between labels.

**Pre-labeling disclosure:**

I labeled 20 posts myself. For the remaining 180 posts, I used AI summaries
of their meaning and main focus to support my labeling decisions. I also
asked AI to check the fit of proposed labels and analyze posts that could
fit multiple categories. I assigned the labels using the category definitions
and each post's main purpose. AI assistance was used for the collection,
summaries, and label analysis described above.

<!-- UNIT 6: Do not fill in results below until the relevant experiments are run. -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook writes them;
           I commit them myself)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
