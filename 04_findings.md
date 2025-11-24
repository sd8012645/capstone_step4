# Findings from Reproducing Public Spam Classifiers and Next Steps

This document summarizes what I learned by reproducing existing spam classifiers and
how these insights will shape my capstone project.

> Note: The specific numbers below (accuracy, precision, recall, F1) are placeholders that
> I will replace with actual results once I finish running my notebooks.

---

## 1. Classical Machine Learning Baseline (TF–IDF + Linear Models)

Using a public Kaggle‑style pipeline on the Enron spam dataset, I reproduced a classical ML baseline
with TF–IDF features and linear models (Naive Bayes, Logistic Regression, and optionally SVM).

### 1.1 Model Performance (Example structure)

- **Dataset:** Enron spam dataset (train/validation/test split)
- **Best classical model:** Logistic Regression with TF–IDF
- **Example metrics (to be filled in):**
  - Accuracy: `X.XX`
  - Precision (spam): `X.XX`
  - Recall (spam): `X.XX`
  - F1 (spam): `X.XX`

### 1.2 Key Learnings

- **Preprocessing matters:** Simple steps like lowercasing and basic cleaning already provide strong performance.
- **TF–IDF + Logistic Regression is very strong:** In line with the literature, this model outperformed Naive Bayes
  on my validation set.
- **Class imbalance handling is important:**
  - Without class weighting, the model can bias towards the majority class (often ham).
  - Adding `class_weight='balanced'` (or similar strategies) improves recall for the minority class.

---

## 2. Deep Learning Baseline (Embedding + LSTM)

Using a public notebook as a starting point, I reproduced an LSTM‑based spam classifier on a spam dataset
(similar in structure to the email datasets used for classical ML).

### 2.1 Model Performance (Example structure)

- **Model:** Embedding + LSTM + dense output
- **Example metrics (to be filled in):**
  - Accuracy: `X.XX`
  - Precision (spam): `X.XX`
  - Recall (spam): `X.XX`
  - F1 (spam): `X.XX`

### 2.2 Key Learnings

- **Data size and epochs matter:**
  - With limited data, the LSTM can overfit quickly; early stopping and dropout are important.
- **Representation differences:**
  - While TF–IDF uses sparse high‑dimensional features, the LSTM works with dense embeddings,
    which can capture semantic patterns and might better handle obfuscated or noisy spam words.
- **Training cost:**
  - Compared to Logistic Regression, the LSTM is more computationally expensive, which matters
    for eventual deployment and retraining in production.

---

## 3. (Optional) Transformer‑Based Baseline

By adapting a HuggingFace text classification tutorial, I can fine‑tune a small transformer model (e.g., DistilBERT)
on a spam dataset.

### 3.1 Model Performance (Example structure)

- **Model:** DistilBERT (or similar) fine‑tuned for binary classification
- **Example metrics (to be filled in):**
  - Accuracy: `X.XX`
  - Precision (spam): `X.XX`
  - Recall (spam): `X.XX`
  - F1 (spam): `X.XX`

### 3.2 Key Learnings

- **Strong performance with limited task‑specific data:**
  - Thanks to pre‑training, the transformer can achieve high F1 with relatively little labeled data.
- **Robustness to vocabulary shift:**
  - Contextual embeddings help the model generalize to new spam phrases better than bag‑of‑words.
- **Compute considerations:**
  - Inference is more expensive than a linear model, which will be important when thinking about latency
    and cost in a production setting.

---

## 4. Comparative Insights

From reproducing classical ML and deep learning baselines, I observe that:

1. **Classical baselines (TF–IDF + Logistic Regression) are very competitive** and easy to train and deploy.
2. **Deep learning models (LSTM/transformers) may improve recall and robustness** on more complex or
   shifting spam distributions, especially with sufficient data.
3. **Model choice depends on operational constraints:**
   - If low latency and simplicity are critical, a linear model might be preferred.
   - If maximum robustness to evolving spam is critical, a deep or transformer model might be worth the cost.

---

## 5. How My Capstone Will Extend These Baselines

Based on my reproduction work, I plan to extend and improve on existing solutions in the following ways:

1. **Explicitly study class imbalance:**
   - Train and evaluate models under different spam/ham ratios (e.g., 10%, 30%, 50% spam).
   - Compare the effect of class weighting, resampling, and threshold tuning on precision/recall trade‑offs.

2. **Simulate distribution shift over time:**
   - Split the Enron (and other) datasets chronologically (earlier vs later emails) to mimic changing spam tactics.
   - Train on earlier periods and evaluate on later periods to measure robustness.

3. **Cost‑sensitive evaluation:**
   - Emphasize metrics and thresholds that reflect the higher cost of misclassifying ham as spam (false positives)
     in some operational contexts, or spam as ham (false negatives) in others.

4. **Model comparison and interpretability:**
   - Compare feature importance (for linear models) and example‑level explanations (for deep models) to understand
     what patterns each model relies on.
   - Use these insights to suggest improvements (e.g., specific feature engineering, better handling of URLs or domains).

By grounding my capstone in reproduced, literature‑consistent baselines and then explicitly targeting robustness and
class imbalance, I will be able to show clear, meaningful improvements over the standard approaches.
