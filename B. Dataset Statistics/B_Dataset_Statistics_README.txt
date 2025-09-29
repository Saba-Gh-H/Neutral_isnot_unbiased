# 📊 B. Dataset Statistics

This folder contains scripts and outputs for computing **descriptive statistics and similarity measures** over the scenario dataset used in downstream LLM bias tests.

## Contents
- **`Final Scenario Statistics/`** — (optional) curated exports/plots from a full run.
- **`RandomNeutralScenarios.json`** — the input dataset (same structure as in `A. Dataset`).  
- **`Scenario_Statistics.json`** — machine‑readable summary produced by the analysis script (see below).

## What the analysis does
Given the neutral scenarios, the script:
- Cleans and tokenizes text (NLTK), removes stop‑words.
- Computes **length metrics** (tokens, sentences), **readability** (Flesch Reading Ease, Flesch‑Kincaid grade), and **sentiment** (VADER).
- Calculates **type–token ratio (TTR)**.
- Runs spaCy **NER** and counts entities.
- Builds **TF‑IDF cosine similarity** across scenarios.
- Builds pairwise **Jaccard similarity** over cleaned token sets.
- Aggregates min / max / mean / stdev for tokens, sentences, sentiment, readability, TTR, entities, and similarity matrices.
- Exports a per‑scenario table and a global summary; renders a few diagnostic plots.

## Dependencies
Python 3.9+ and the following packages:
```
nltk
spacy
textstat
numpy
pandas
scikit-learn
matplotlib
```
One‑time model/data downloads required by the script:
```
python -m spacy download en_core_web_md
python -c "import nltk; [nltk.download(x) for x in ['punkt','stopwords','vader_lexicon','averaged_perceptron_tagger']]"
```

## How to run
1. Ensure `RandomNeutralScenarios.json` is present in this folder.
2. Run the analysis script (example):
   ```bash
   python scenario_statistics.py
   ```
   or run the cells if you use a notebook version.
3. On completion you will have:
   - **`Scenario_Statistics.json`** — full results (global aggregates, similarity summaries, and per‑scenario details).
   - Console table preview of the per‑scenario metrics.
   - Plots: *Tokens vs Sentiment*, *Sentiment Distribution*, and *Readability Score Distribution*.

## Expected JSON structure (excerpt)
```json
{
  "scenario_data": [
    {
      "scenario_number": 1,
      "tokens": 123,
      "sentences": 6,
      "cleaned_tokens": ["example", "..."],
      "text": "…",
      "sentiment": 0.03,
      "readability_score": 62.1,
      "ttr": 0.54,
      "entities": ["Alice", "London"]
    }
  ],
  "min_cosine_sim": 0.01,
  "max_cosine_sim": 0.42,
  "mean_cosine_sim": 0.12,
  "min_jaccard_sim": 0.00,
  "max_jaccard_sim": 0.23,
  "mean_jaccard_sim": 0.07,
  "mean_tokens": 110.4,
  "std_dev_tokens": 24.8,
  "...": "..."
}
```

## Reproducibility notes
- Tokenization and readability metrics may vary slightly across library versions.
- Make sure the **same spaCy model** (`en_core_web_md`) and **NLTK resources** are installed across environments.
- If you run headless (CI), set a non‑interactive Matplotlib backend, e.g.:
  ```python
  import matplotlib
  matplotlib.use("Agg")
  ```

## Known issues
- The sample code has a minor typo near the end: `plt mport json` → **`import json`**.
- `.ipynb_checkpoints` directories (if present) can be ignored.
