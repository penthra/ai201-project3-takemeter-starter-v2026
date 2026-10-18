# Acceptance criteria for TakeMeter

These criteria define what I would consider useful performance when sorting
public CasualConversation posts into personal experiences, discussion requests,
and viewpoints. They are targets to check later, not results already achieved.

The current dataset contains 200 posts:

- 96 personal experiences: 48% of the dataset.
- 81 discussion requests: 40.5% of the dataset.
- 23 viewpoints: 11.5% of the dataset.

I labeled 20 posts myself. For the remaining 180 posts, I used AI summaries
and analysis to support my labeling decisions, applying the category definitions
to choose the labels.

## How the model will be tested

Use 70% of the posts for training, 15% for choosing training settings, and 15%
for the final test. Keep approximately the same category proportions in each
group. Do not use the final test posts to train the model or choose its settings.

Repeat the training and testing with the random-number seeds 42, 7, and 2024.
These numbers control the random choices used to divide the data and train the
model. Criteria 1, 2, and 3 must pass in each of the three runs. A high score
in one run cannot compensate for a failed target in another run.

## 1. The model must label at least 70% of the test posts correctly

Divide the number of correct predictions by the total number of test posts.
The result must be at least 70% in every run. With 30 test posts, the model
must label at least 21 correctly.

**Why this target:** The largest category contains 48% of the full dataset.
A model that always chooses that category would therefore achieve only 48%
on the full dataset. A 70% target asks for a clear improvement and would make
the model useful for suggesting labels that a person checks afterward.

## 2. Each larger category must achieve a score of at least 0.65

Check personal experiences and discussion requests separately. For each
category, calculate:

**Score = (2 × correctly identified posts in that category) ÷
(actual posts in that category + posts the model assigned to that category).**

Count only posts in the final test group. Each category's score must be at
least 0.65 in every run. If the model assigns no posts to a category that is
present in the test group, its score for that category is zero.

For example, suppose the test group contains 10 discussion requests. If the
model assigns that label to 8 posts and 6 of those assignments are correct,
the score is 12 divided by 18, or approximately 0.67.

**Why this target:** Personal experiences and discussion requests have 96 and
81 examples, so the model has more examples of these categories to learn from.
Stories that end with a question can be difficult to distinguish from requests.
This calculation penalizes both missing a category's posts and assigning its
label to posts that belong elsewhere.

## 3. The viewpoint category must achieve a score of at least 0.50

Use the same calculation from criterion 2, applied only to viewpoints in the
final test group. The score must be at least 0.50 in every run. Report how
many actual viewpoints were in each test group alongside the score.

**Why this target:** The dataset contains only 23 viewpoints. A test group
containing 15% of the dataset will have approximately three or four of them,
so even one mistake can change the score substantially. I chose a lower target
than for the two larger categories, while still requiring the model to identify
viewpoints instead of assigning all of them another label.

## 4. Every category must contain between 20 and 140 of the 200 posts

After a person has reviewed the labels and any unsuitable posts have been
replaced, count the posts in each category. Each category must contain at
least 20 posts and no more than 140 posts. These limits equal 10% and 70% of
the complete dataset.

**Why this target:** The current counts are 96, 81, and 23. These limits allow
the unequal category sizes found in this sample, while preventing a category
from becoming extremely small or dominating the dataset. Twenty examples is
a minimum safeguard; the viewpoint category's small test group will still
make its score less reliable. The upper limit follows the project's rule
that no category may exceed 70%.

## 5. Test accuracy must vary by no more than 10 percentage points across three seeds

Use the test accuracies from seeds 42, 7, and 2024. Subtract the lowest
accuracy from the highest accuracy. The difference must be no greater than
0.10. Check this across all three runs together, using the saved three-seed
results JSON file.

**Why this target:** With only 200 posts and four viewpoints in the initial
test split, performance may depend on which posts enter each split and on
training randomness. A useful model should perform reasonably consistently
rather than depend heavily on a favorable random split.

This criterion measures run stability, not agreement between human labelers.
The criteria above cover overall accuracy, per-label performance, and balance.

---

