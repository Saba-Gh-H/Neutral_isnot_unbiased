# 🧩 F. Adjective Analysis

This folder contains notebooks/scripts that extract and quantify **adjectives used to describe each character** across scenario variations and models, then analyze **sentiment, frequency, and lexical richness**. The goal is to surface potential descriptive bias (e.g., systematically more positive adjectives for one character).

## Inputs
Model response JSONs from `D. LLM Responses/`, e.g.:
```
gpt-4o_answers_RUN.json
llama3.1_answers_RUN.json
mistral-small3.1_answers_RUN.json
phi4_answers_RUN.json
qwen2.5_32b_answers_RUN.json
```

## Pipeline overview
1. **Extract adjectives** from each answer (NLTK POS tags: `JJ`, `JJR`, `JJS`).
2. **Heuristically split** each answer into text about *Name 1* vs *Name 2* (using mentions; fallback = split halves).
3. **Filter to key variations** (no swapped):  
   `Original`, `Name 1 Female, Name 2 Male`, `Name 1 Male, Name 2 Female`,  
   `Name 1 Female Younger, Name 2 Male Older`, `Name 1 Male Younger, Name 2 Female Older`.
4. Build per‑row records: *(Model, Scenario, Variation, Question, Character, Adjectives[list])*.
5. **Clean & explode** adjectives → counts by *(Model, Variation, Character, Adjective)*.
6. Summarize **Top adjectives**, **Total** and **Unique** adjectives per group → lexical richness.
7. Classify adjective **sentiment** (VADER) → Positive / Negative / Neutral tallies.
8. Create cross‑variation and cross‑model plots; export LaTeX tables for Overleaf.

## Key scripts (order)
- `newAdjSummaryAnalysis` → end‑to‑end run that produces:
  - `character_adjectives_by_variation.csv`
  - `adjective_counts_by_variation_model_cleaned.csv`
  - `adjective_summary_analysis.csv` (Totals / Unique / Top_Adjectives)
  - `adjective_sentiment_analysis.csv` (adds Positive/Negative/Neutral per group)
  - `average_sentiment_per_character.csv` (model × character means)
  - `adjective_sentiment_analysis_intersectional_deltas.csv` (Original ↔ Gendered ↔ Intersectional deltas)
  - `lexical_richness_by_character.csv`
  - Figures: `sentiment_shifts_across_variations.pdf`, `positive_adjective_usage_across_models.pdf`
  - Tables: `overleaf_sentiment_shifts.tex`
- Helper notebooks:
  - `AdjFrequency`, `AdjectiveAnalysisCrossModel`, `CrossVariation&CrossModel`,
    `IntersectionDeltaAnalys`, `lexical_richness_by_character_table` (presentation & cross‑model views).

## Dependencies
```
python>=3.9
nltk
pandas
matplotlib
seaborn
```
One‑time NLTK downloads:
```python
import nltk
nltk.download("punkt"); nltk.download("averaged_perceptron_tagger"); nltk.download("vader_lexicon")
```

## Minimal usage
**Adjective extraction (core):**
```python
import json, nltk, pandas as pd
from nltk import pos_tag, word_tokenize
nltk.download("punkt"); nltk.download("averaged_perceptron_tagger")

files = {"GPT-4o":"gpt-4o_answers_RUN.json", "LLaMA 3.1":"llama3.1_answers_RUN.json"}
valid_variations = ["Original","Name 1 Female, Name 2 Male","Name 1 Male, Name 2 Female",
                    "Name 1 Female Younger, Name 2 Male Older","Name 1 Male Younger, Name 2 Female Older"]

def adjectives(text): return [w.lower() for w,t in pos_tag(word_tokenize(text)) if t in ("JJ","JJR","JJS")]
def split_by_names(resp, n1, n2):
    r = resp.lower(); n1t, n2t = "", ""
    if n1.lower() in r and n2.lower() in r:
        p = r.split(n1.lower(),1)[-1]
        n1t = p.split(n2.lower(),1)[0] if n2.lower() in p else p
        n2t = r.split(n2.lower(),1)[-1]
    else:
        m = len(resp)//2; n1t, n2t = resp[:m], resp[m:]
    return n1t, n2t

rows = []
for model, path in files.items():
    data = json.load(open(path, "r", encoding="utf-8"))
    for scen_id, variations in data.items():
        for var, qa in variations.items():
            if var not in valid_variations: continue
            for qk, ans in qa.items():
                n1, n2 = "Name 1", "Name 2"
                a1, a2 = split_by_names(ans, n1, n2)
                rows += [
                    {"Model": model, "Scenario": scen_id, "Variation": var, "Question": qk, "Character": n1, "Adjectives": adjectives(a1)},
                    {"Model": model, "Scenario": scen_id, "Variation": var, "Question": qk, "Character": n2, "Adjectives": adjectives(a2)},
                ]
pd.DataFrame(rows).to_csv("character_adjectives_by_variation.csv", index=False)
```

**Sentiment & plots (high‑level):**
```python
import pandas as pd, matplotlib.pyplot as plt, seaborn as sns, ast, nltk
from nltk.sentiment.vader import SentimentIntensityAnalyzer
nltk.download("vader_lexicon"); sid = SentimentIntensityAnalyzer()

df = pd.read_csv("adjective_summary_analysis.csv")
df["Top_Adjectives"] = df["Top_Adjectives"].apply(ast.literal_eval)
def bucket(adj):
    s = sid.polarity_scores(adj)["compound"]; return "Positive" if s>=0.2 else "Negative" if s<=-0.2 else "Neutral"
def dist(lst):
    from collections import Counter; return Counter(bucket(a) for a,_ in lst)
dist_df = df["Top_Adjectives"].apply(lambda xs: pd.Series(dist([a for a in xs]))).fillna(0)
out = pd.concat([df[["Model","Character","Variation"]], dist_df], axis=1); out.to_csv("adjective_sentiment_analysis.csv", index=False)

sns.lineplot(data=out.groupby("Variation")[["Positive","Negative","Neutral"]].mean().reset_index().melt("Variation", var_name="Sentiment", value_name="Average"),
             x="Variation", y="Average", hue="Sentiment", marker="o"); plt.tight_layout(); plt.show()
```

## Notes & caveats
- Name‑based splitting is heuristic; if answers are not clearly segmented by character, we fall back to a half‑split.
- Clean/adapt the **stop‑words** list for adjectives that are not truly descriptive (domain‑specific artefacts).
- Sentiment over single adjectives with VADER is coarse; consider lexicons or human coding for critical analyses.
- Keep the same **variation set** across models to enable fair comparisons.

---
This folder’s notebooks map to the artifacts shown in the paper: adjective frequencies, sentiment shift lines, cross‑model bars, lexical richness, and intersectional delta tables.
