# 🔁 E. Counterfactual Analysis

This folder contains notebooks/scripts that **measure how model answers change** when we make controlled changes to the same scenario (name swaps, gender/age flips). It aggregates per‑model results, computes **semantic similarity** and **sentiment shift**, and generates comparative plots across LLMs.

## Inputs
- JSON response dumps from `D. LLM Responses/`, one per model, e.g.:
  - `gpt-4o_answers_RUN.json`
  - `llama3.1_answers_RUN.json`
  - `mistral-small3.1_answers_RUN.json`
  - `phi4_answers_RUN.json`
  - `qwen2.5_32b_answers_RUN.json`

Each JSON has the structure:
```json
{
  "Scenario_1": {
    "Original": { "Question_1": "...", "Question_2": "..." },
    "Swapped Names": { "Question_1": "..." },
    "Name 1 Female, Name 2 Male": { "...": "..." },
    "Name 1 Male, Name 2 Female": { "...": "..." },
    "Name 1 Female Younger, Name 2 Male Older": { "...": "..." },
    "Name 1 Male Younger, Name 2 Female Older": { "...": "..." }
  }
}
```

## Variation steps (ordered)
```
Original
Swapped Names
Name 1 Female, Name 2 Male
Name 1 Male, Name 2 Female
Name 1 Female Younger, Name 2 Male Older
Name 1 Male Younger, Name 2 Female Older
```

## Comparison pairs evaluated
```
(Original) → (Swapped Names)
(Original) → (Name 1 Female, Name 2 Male)
(Name 1 Female, Name 2 Male) → (Name 1 Male, Name 2 Female)
(Name 1 Female, Name 2 Male) → (Name 1 Female Younger, Name 2 Male Older)
(Name 1 Female Younger, Name 2 Male Older) → (Name 1 Male Younger, Name 2 Female Older)
```

## What the code does
1. Loads a model’s JSON answers.
2. For each scenario & question, pairs answers across the comparison steps.
3. Computes:
   - **Semantic similarity** with `SentenceTransformer('all-MiniLM-L6-v2')` and cosine similarity.
   - **Sentiment shift** using `TextBlob` polarity (to quantify direction & magnitude of change).
4. Fills **missing variations** with placeholders to keep shapes aligned (logged to console).
5. Writes a per‑model CSV, e.g. `counterfactual_bias_analysis_gpt4o.csv`.
6. Plots:
   - Mean sentiment shift per comparison pair.
   - Mean semantic similarity per pair.
   - Similarity distribution by question index.
7. Cross‑model summaries:
   - **Sentiment shifts** across all models for the same pairs.
   - **Similarity distributions** across models by question index.

## Dependencies
```
python>=3.9
sentence-transformers
textblob
numpy
pandas
scikit-learn
matplotlib
seaborn
```
> Notebooks include a small auto‑installer for `sentence-transformers` and `textblob` to ease first‑run.

## Minimal runner (single model)
```python
# Load JSON responses (example: GPT-4o)
import json, pandas as pd, numpy as np
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity
from textblob import TextBlob

with open("gpt-4o_answers_RUN.json", "r", encoding="utf-8") as f:
    data = json.load(f)

model = SentenceTransformer("all-MiniLM-L6-v2")

variation_steps = [
    "Original",
    "Swapped Names",
    "Name 1 Female, Name 2 Male",
    "Name 1 Male, Name 2 Female",
    "Name 1 Female Younger, Name 2 Male Older",
    "Name 1 Male Younger, Name 2 Female Older",
]

comparison_pairs = [
    ("Original", "Swapped Names"),
    ("Original", "Name 1 Female, Name 2 Male"),
    ("Name 1 Female, Name 2 Male", "Name 1 Male, Name 2 Female"),
    ("Name 1 Female, Name 2 Male", "Name 1 Female Younger, Name 2 Male Older"),
    ("Name 1 Female Younger, Name 2 Male Older", "Name 1 Male Younger, Name 2 Female Older"),
]

def sentiment(text): return TextBlob(text).sentiment.polarity

rows = []
for scen_id, variations in data.items():
    # ensure all expected variations exist
    qn = len(next(iter(variations.values())))
    for step in variation_steps:
        variations.setdefault(step, [{"question": "MISSING", "answer": "MISSING"}] * qn)
    for a, b in comparison_pairs:
        for idx, (qa, qb) in enumerate(zip(variations[a].items() if isinstance(variations[a], dict) else enumerate(variations[a]),
                                            variations[b].items() if isinstance(variations[b], dict) else enumerate(variations[b]))):
            ans_a = qa[1] if isinstance(qa[1], str) else qa[1].get("answer", "")
            ans_b = qb[1] if isinstance(qb[1], str) else qb[1].get("answer", "")
            s_a, s_b = sentiment(ans_a), sentiment(ans_b)
            sim = cosine_similarity(model.encode([ans_a]), model.encode([ans_b]))[0,0]
            rows.append({"scenario_id": scen_id, "question_index": idx+1, "step_from": a, "step_to": b,
                         "similarity": sim, "sentiment_a": s_a, "sentiment_b": s_b, "sentiment_diff": s_b - s_a})

df = pd.DataFrame(rows)
df.to_csv("counterfactual_bias_analysis_gpt4o.csv", index=False)
```

## Cross‑model sentiment shift plot (snippet)
```python
import pandas as pd, seaborn as sns, matplotlib.pyplot as plt
pairs = [
  ("Original","Swapped Names"),
  ("Original","Name 1 Female, Name 2 Male"),
  ("Name 1 Female, Name 2 Male","Name 1 Male, Name 2 Female"),
  ("Name 1 Female, Name 2 Male","Name 1 Female Younger, Name 2 Male Older"),
  ("Name 1 Female Younger, Name 2 Male Older","Name 1 Male Younger, Name 2 Female Older"),
]
files = {
  "GPT-4o": "counterfactual_bias_analysis_gpt4o.csv",
  "LLaMA 3.1": "counterfactual_bias_analysis_llama3.1.csv",
  "Mistral": "counterfactual_bias_analysis_mistral.csv",
  "Phi-4": "counterfactual_bias_analysis_phi.csv",
  "Qwen2.5": "counterfactual_bias_analysis_qwen.csv",
}
rows = []
for model_name, path in files.items():
    df = pd.read_csv(path)
    for a,b in pairs:
        sub = df[(df.step_from==a)&(df.step_to==b)]
        if not sub.empty:
            rows.append({"Model": model_name, "Comparison": f"{a} → {b}", "Mean Sentiment Shift": sub.sentiment_diff.mean()})
sns.set(style="whitegrid")
sns.barplot(data=pd.DataFrame(rows), x="Comparison", y="Mean Sentiment Shift", hue="Model")
plt.axhline(0,color="gray",linestyle="--"); plt.tight_layout(); plt.show()
```

## Outputs
- Per‑model CSVs (e.g., `counterfactual_bias_analysis_gpt4o.csv`) with columns:
  `scenario_id, question_index, step_from, step_to, similarity, sentiment_a, sentiment_b, sentiment_diff, answer_a, answer_b`
- Plots saved as PDFs: sentiment shift, semantic similarity, similarity distribution by question.
- Optional top‑k tables of **most changed responses** (lowest similarity).

## Notes
- Similarity depends on the chosen embedding model; we use a small, fast baseline (`all-MiniLM-L6-v2`).
- `TextBlob` polarity is a coarse signal—use model‑based sentiment if needed.
- Keep the **comparison pairs** consistent across models to enable apples‑to‑apples charts.
- If you run in CI/headless, select a non‑interactive Matplotlib backend (e.g., `Agg`).

---
Folders like `CounterAnalys-<Model>` contain per‑model notebooks; `CounterSentimentShiftAnalysis5LLMs` and `CounterSimilarityDistributionQ5LLMs` produce cross‑model figures.
