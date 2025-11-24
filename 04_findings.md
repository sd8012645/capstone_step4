# Findings from Reproducing Public Spam Classifiers and Next Steps

This document summarizes what I learned by reproducing existing spam classifiers on my
**Enron spam dataset (`enron_spam_data.csv`)** and how these insights will shape my capstone project.

The two main models I reproduced are:

- TF–IDF + Logistic Regression (with class weighting)
- TF–IDF + Multinomial Naive Bayes

All results below are on an 80/20 stratified train–test split.

---

## 1. Classical Machine Learning Baseline (TF–IDF + Logistic Regression)

Using the Enron spam dataset with a simple TF–IDF representation and a linear model, I trained a
Logistic Regression classifier with `class_weight='balanced'`.

### 1.1 Model Performance

- **Dataset:** Enron spam dataset (80/20 stratified train/test split)
- **Model:** Logistic Regression + TF–IDF (1–2 grams, max_features=20,000)

**Test metrics:**

- **Accuracy:** 1.00 (6744 / 6744 correct)
- **Per-class metrics:**
  - *Ham* — precision **1.00**, recall **1.00**, F1 **1.00**, support **3309**
  - *Spam* — precision **1.00**, recall **1.00**, F1 **1.00**, support **3435**

**Confusion matrix:**

[[3305   4]
 [   0 3435]]

- 4 ham messages misclassified as spam (false positives)
- 0 spam messages misclassified as ham (false negatives)

### 1.2 Key Learnings

- The **Logistic Regression baseline is extremely strong** on this dataset, achieving essentially
  perfect precision and recall on both classes.
- All remaining errors are **ham emails flagged as spam** (4 out of 3309), which is important
  from an operational standpoint because false positives can hide legitimate messages.
- Such high performance suggests that:
  - The dataset is relatively “clean” and separable with simple text features, **or**
  - There may be potential sources of data leakage or overlap (e.g., near-duplicate emails across
    train/test splits). This is something I should check more carefully in later EDA.

This model sets a **very high classical baseline** for my capstone. Any more complex model
(deep learning, transformers) will need to justify its extra complexity in terms of robustness,
interpretability, or performance under more realistic conditions (e.g., different splits, more noise,
or additional datasets).

---

## 2. Multinomial Naive Bayes Baseline

Next, I trained a Multinomial Naive Bayes classifier on the same TF–IDF features.

### 2.1 Model Performance

- **Model:** Multinomial Naive Bayes + TF–IDF

**Test metrics:**

- **Accuracy:** 0.99 (6677 / 6744 correct)
- **Per-class metrics:**
  - *Ham* — precision **1.00**, recall **0.98**, F1 **0.99**, support **3309**
  - *Spam* — precision **0.98**, recall **1.00**, F1 **0.99**, support **3435**

**Confusion matrix:**

[[3242   67]
 [   0 3435]]

- 67 ham messages misclassified as spam (false positives)
- 0 spam messages misclassified as ham (false negatives)

### 2.2 Key Learnings

- Naive Bayes is **competitive but weaker** than Logistic Regression on this dataset.
- NB achieves perfect recall on spam (no spam sneaks through), but at the cost of
  more ham messages being incorrectly flagged as spam.
- This trade-off may be acceptable in scenarios where missing spam is extremely costly,
  but it is problematic in user-facing email systems where false positives are very visible.

---

## 3. Comparative Insights from the Two Baselines

Comparing Logistic Regression and Naive Bayes on the same TF–IDF representation:

1. **Accuracy and F1**
   - Logistic Regression: 1.00 accuracy, perfect F1 for both classes.
   - Naive Bayes: 0.99 accuracy, F1 ≈ 0.99 for both classes.

2. **Error patterns**
   - Both models produce **no false negatives for spam** on this split (good from a security standpoint).
   - Naive Bayes produces many more **false positives on ham** (67 vs 4), which could be annoying or
     operationally costly in a real email system.

3. **Model choice implications**
   - For this dataset and split, **Logistic Regression clearly dominates NB** and is still easy to deploy,
     fast to train, and interpretable (via coefficients).
   - Naive Bayes remains a simple, fast baseline but is less attractive given its tendency to over-flag ham.

These findings are consistent with the literature, where Logistic Regression on TF–IDF often outperforms
Naive Bayes on modern spam datasets.

---

## 4. Implications for My Capstone

The reproduced baselines show that:

- Simple classical models can already reach **near-perfect performance** on the Enron dataset
  under a random 80/20 split.
- Because the metrics are so high, the main challenge in my capstone is **not just accuracy**,
  but **robustness and realism**:

  1. **Robustness to class imbalance**
     - I will explicitly vary the spam/ham ratio (e.g., downsample spam or ham) and study how
       each model’s precision/recall trade-offs change.
     - I will experiment with different decision thresholds (not just 0.5) to see how to tune the
       model for different operational preferences (e.g., minimize false positives vs false negatives).

  2. **Distribution shift over time**
     - I will create **time-based splits** (train on earlier emails, test on later ones) to mimic how
       spam tactics evolve.
     - This will help answer whether the current near-perfect performance holds under more realistic
       temporal splits, or whether performance degrades when the spam vocabulary changes.


  3. **Deeper models as robustness tools**
     - Given that classical baselines already hit ~100% on this split, deep learning or transformers
       might not dramatically increase raw accuracy, but they **might** be more robust to distribution
       shifts or more nuanced spam content .
     - If time allows I might therefore treat deep models as candidates for **robustness improvements**, not just
       accuracy boosts.





