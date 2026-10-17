# Review of all 200 CasualConversation rows / 全部200行的标签审阅

I read the title and body fields of all 200 rows and made a preliminary,
row-by-row labeling pass. These are AI annotations for you to review, not your
own unassisted reading or final ground truth. Three unavailable entries have now
been replaced with additional collected posts from the same community.
我逐行阅读了200行的标题和正文，并进行了初步标注。这些是供你审核的AI标注，
不能算作你独立完成的阅读或最终标准答案。三条正文不可用的记录已用同社区的
其他真实帖子替换。替换后的三条也已阅读并标注。

The annotations and an individual reason for every row are in
[data/casualconversation_label_review.csv](data/casualconversation_label_review.csv).
`row_number` is the one-based post number, not the physical CSV line number.
All 200 IDs, source URLs, and text fields match the current corrected CSV exactly.

## First pass: the example labels / 第一轮：尝试示例标签

I used the definitions and boundary rules in `data/staff_taxonomy.md`:

- `analysis`: an argument supported by a specific, checkable fact that carries
  the argument. A number or quotation appearing in a story is not enough.
  **分析**：用具体、可核查的事实支撑论点；出现数字或引语本身不够。
- `hot_take`: a confident general claim without support offered.
  **无依据的强烈观点**：自信地下结论，却没有提供支持。
- `reaction`: an immediate personal response to something that just happened.
  **即时反应**：对刚发生的事情表达个人感受。

| Attempted example label | Clear fit | Borderline | Combined |
|---|---:|---:|---:|
| `analysis` | 4 | 9 | 13 |
| `hot_take` | 2 | 1 | 3 |
| `reaction` | 38 | 31 | 69 |
| No suitable example label | — | — | 115 |
| Original body unavailable | — | — | 0 |
| **Total** | | | **200** |

The CSV leaves `example_label` blank for no-fit and unavailable rows.
`example_fit` distinguishes `fits`, `borderline`, `no_fit`, and `unusable`.
The last two are review statuses, not extra classifier labels.

**Conclusion:** the example taxonomy covers this sample poorly. Only 44 rows
clearly fit; another 41 are debatable, and 115 have no suitable example label. Questions, retrospective stories, and
reasoned opinions without specific empirical evidence often fall outside it.
“Immediate” is particularly difficult when the text says “yesterday,” “the
other day,” or describes a recurring feeling. These are interpretive counts,
not objectively measured ground truth.

**结论：**示例体系不太适合这批帖子。44行明显符合，41行有争议，115行没有合适
的示例标签。提问、回忆性故事，以及没有具体事实证据但确实给出了理由的观点，
不应该为了凑三个类别而硬塞进 `reaction` 或 `hot_take`。

Some concrete contrasts from the reading:

- **#7, burger:** “80/20” describes an ingredient. It does not establish an
  argument. This is mainly a cooking story, with a secondary closing question.
- **#71, grapes:** repeated comparisons of fruit placement support an
  explanation of food waste. That is a much stronger `analysis` candidate.
- **#31, grammar:** worked sentences demonstrate why a pronoun rule works.
- **#76, classmates:** a relationship history spanning years is not an
  immediate reaction simply because it is emotional.
- **#110, Friday the 13th:** a substantial request includes prior attempts,
  budget/time limits, and an allergy constraint. It is still a request.
- **#46, money and happiness:** the writer offers conceptual reasons, so
  calling it unsupported merely because it has no statistic is misleading.

## Three distinctions observed / 从原文观察到的三个区别

1. Some posts develop an account of what happened to the writer; others mainly
   ask readers to supply an answer, recommendation, story, or participation.
   有些帖子主要讲自己的经历；另一些主要向读者征求回答、建议、故事或参与。
2. Some generalize or explain a position using reasons; others stay with the
   writer's specific circumstances and feelings. Having a strong tone does not
   determine which one it is.
   有些帖子在解释一个观点，有些主要描述个人处境和感受；语气强烈不等于某种标签。
3. Questions vary in development: some are bare prompts, while others provide
   examples, constraints, and previous attempts. Stories also vary in detail.
   A question is not automatically low quality, and a story is not automatically
   substantial.
   提问也有内容丰富和简单空泛之分；不能把“问题”直接等同于低质量，把“故事”
   直接等同于有深度。

## Recommended taxonomy / 推荐的标签体系

These labels sort **communicative purpose**, not truth, quality, or sentiment.
这套标签区分**发帖的主要目的**，不直接评价真假、质量或情绪好坏。

| Label | Definition / 定义 | Count | Share of 200 available posts |
|---|---|---:|---:|
| `personal_experience` | Mainly shares the writer's experiences, personal circumstances, or feelings. / 主要分享个人经历、处境或感受。 | 96 | 48.0% |
| `discussion_request` | Mainly asks readers for answers, stories, advice, judgments, recommendations, or participation. / 主要征求回答、故事、建议、判断或参与。 | 81 | 40.5% |
| `viewpoint` | Mainly presents or explains a broader opinion, observation, recommendation, or idea. / 主要表达或解释较普遍的观点、观察、建议或想法。 | 23 | 11.5% |
| **Available total** | | **200** | **100.0%** |

No class exceeds the project's 70% cap. The 23-post `viewpoint` class is small:
its test-set F1 may be unstable, particularly across random seeds. Do not move
posts between labels just to make the counts equal. These counts are from this
specific sample, which includes popularity and recommendation bias; they do
not estimate the entire community's proportions.

If a simpler two-class task is preferable, merge `personal_experience` and
`viewpoint` into `sharing` (119 posts), against `seeking_response` (81 posts).
Do not use that merge if distinguishing stories from viewpoints is central to
your intended classifier.

### Decision rules / 判断规则

1. Read title **and** body. A question mark, number, emotional word, or post
   flair does not decide the label by itself.
   阅读标题和正文，不凭问号、数字、情绪词或论坛自带分类直接决定标签。
2. A specific unresolved request for advice, judgment, recommendations, or
   participation is `discussion_request`, even when a long story provides
   necessary context. Example: **#40**, asking whether refusing help was wrong;
   **#200**, seeking better free-time activities.
   明确希望读者解决问题或提供判断时，背景故事很长也可以标为“征求回应”。
3. A self-contained account of an event or personal situation stays
   `personal_experience` when a generic “anyone else?” is mainly a closing
   invitation. Example: **#7**, the burger; **#108**, a pleasant conversation.
   如果主体是完整的经历分享，结尾附带一句“有人也这样吗”不自动变成提问类。
4. Use `viewpoint` when explaining or defending a broader idea is the main
   purpose. Example: **#31**, the grammar explanation; **#80**, the criticism of
   connected appliances. A brief moral at the end of a story does not, by
   itself, turn that story into `viewpoint`: see **#178**.
   主体在解释或论证较普遍的想法时标为“观点与解释”；故事结尾的一句感悟不够。
5. For remaining mixed posts, compare what the body develops most: an account,
   a request, or an idea. Record the competing label and reason in the notes.
   对真正混合的帖子，比较正文主要展开哪一部分，并记录有争议的边界。

The hardest boundary is **personal_experience vs discussion_request**.
Review flagged cases such as #18, #44, #55, #103, #120, #144, and #148 before
accepting this taxonomy. The annotations make a provisional choice for every
available row, but a human second pass could reasonably revise some choices.

### Examples / 真实例子

- **`personal_experience`: #7**, making a burger; **#87**, hearing a cat purr
  after getting hearing aids. Both primarily communicate what happened and how
  it felt.
- **`discussion_request`: #110**, coworker celebration ideas with constraints;
  **#164**, affordable hobby suggestions. Both seek useful responses.
- **`viewpoint`: #31**, explaining a grammar test; **#102**, explaining a
  wrong-train metaphor for sunk-cost decisions. Both develop an idea rather
  than merely recounting one event.

## Data issues found during full reading / 通读时发现的数据问题

| Post # | Reddit ID | Issue |
|---|---|---|
| 5 | `1x3r95x` | Body is “Sorry, this post was removed by Reddit’s filters.” |
| 22 | `1x3gl4h` | Body is “Post is awaiting moderator approval.” |
| 81 | `fle7gb` | Random replacement words and an explicit Redact deletion notice. |

The three unavailable rows above were replaced in place. The old IDs in this
table document the removed entries; they are not present in the current CSV.

| Post # | Replacement ID | Replacement title |
|---|---|---|
| 5 | `1taqxq8` | Favorite little things about life? |
| 22 | `1sd097x` | Whats your simple joys in life? |
| 81 | `1q9zvex` | Does anyone else enjoy doing small things alone more than with others? |

All three replacements are public r/CasualConversation posts with available
bodies and source URLs. They were read and preliminarily labeled
`discussion_request`; none fits the example taxonomy well. The corrected CSV
has 200 available posts and 200 unique IDs, URLs, normalized texts, and
normalized bodies. The other 197 rows are unchanged. The generator embeds the
corrected snapshot and rejects removal/moderation placeholders.

三条不可用记录已经原位替换。当前CSV有200条正文可用、去重后的帖子，其余197条
没有修改。三个替换帖的建议标签均为“征求回应”，仍需人工审核。生成脚本也已同步。

Additional review flags in the CSV:

- #71 and #72 contain a trailing locked-post interface notice.
- #88 includes closed-poll interface text; keep the actual question/options.
- #27 has an orphan Markdown heading.
- #168 contains a long third-party README quotation. Decide how quoted material
  should count before applying word-count or model-length rules.
- #191 and #192 have very similar paid-coffee storylines. Different IDs and
  wording do not prove independent content. Inspect the pair; similarity alone
  does not prove either is copied or fabricated.
- #65 contains numerical and attribution claims that were not fact-checked.
  An `analysis` annotation means an evidential structure, not verified truth.

## How to use this review / 如何使用本次审阅

Review the annotations, finalize your definitions and difficult-boundary rules,
and correct the AI suggestions. The three unavailable entries have been replaced.
Disclose AI pre-labeling in your README; do not describe
these annotations as cold or unassisted labels. The source CSV's `label` column now contains the proposed three-category labels.
Its `note` column contains the AI-prelabeling disclosure and a row-specific reason.
Human review is still pending. The notebook and staff taxonomy have not been changed.

Source CSV SHA-256:
`610a40989eacd211a63953ecd140a23bfb12ec32be4feefd61248b6820c317a1`
