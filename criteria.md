# Acceptance criteria for TakeMeter

These criteria define what I would consider useful performance when sorting
public CasualConversation posts into personal experiences, discussion requests,
and viewpoints. They are targets to check later, not results already achieved.

The current dataset contains 200 posts:

- 96 personal experiences: 48% of the dataset.
- 81 discussion requests: 40.5% of the dataset.
- 23 viewpoints: 11.5% of the dataset.

The current labels were suggested by artificial intelligence. A person must
review and correct them before they are used as reference answers for testing.

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

## 5. Two people must independently agree on at least 24 of the same 30 posts

Randomly select 30 posts from this community and record their post identifiers.
I will label these posts using my written category definitions. A second person
will label the same posts using the same definitions without seeing my choices.
We must choose the same label for at least 24 posts, giving agreement of at
least 80%. Calculate agreement before discussing or correcting disagreements.

**Why this target:** A story with a closing question can reasonably confuse
people as well as the model. Allowing up to six disagreements recognizes this
difficulty. More disagreements would suggest that the definitions or their
application need improvement before I judge the model against those labels.
This check uses my project's categories; it is separate from the staff's
labeling exercise.

---

These targets have not yet been checked against training results. If a target
is missed, keep it and explain the likely cause. Do not lower a target merely
because the model failed to reach it.
