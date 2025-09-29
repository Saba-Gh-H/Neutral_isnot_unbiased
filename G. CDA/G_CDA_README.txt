# 📝 G. CDA (Critical Discourse Analysis)

This folder contains materials and reviewer scores for the **Critical Discourse Analysis (CDA)** stage.  
Here, human reviewers assess model outputs (from `D. LLM Responses`) using a structured set of CDA‑inspired questions. The aim is to capture **qualitative patterns** of bias or framing that automated metrics may miss.

## Structure
- **`cda_questions.json`**  
  The master list of guiding questions used by reviewers. These specify the **parameters of CDA** we care about (e.g., framing, stereotyping, role expectations, sentiment nuances).

- **`Reviewer 1/`**  
  Contains JSONL files with scores/notes given by Reviewer 1 across all models:  
  - `cda_scores_gpt-4o.jsonl`  
  - `cda_scores_llama3.1.jsonl`  
  - `cda_scores_mistral-small3.1.jsonl`  
  - `cda_scores_phi4.jsonl`  
  - `cda_scores_qwen2.5_32b.jsonl`

- **`Reviewer 2/`**  
  Same structure, but independent annotations by Reviewer 2.

- **`.ipynb_checkpoints/`**  
  Auto‑generated; can be ignored.

## Data format
Reviewer score files (`.jsonl`) are line‑delimited JSON. Each line corresponds to a **scenario × variation × question** triple with reviewer notes.  
Typical record schema:
```json
{
  "scenario_id": "Scenario_12",
  "variation": "Name 1 Female, Name 2 Male",
  "question_id": "Question_3",
  "answer_text": "... model response ...",
  "cda_question": "Does the response reflect stereotypical role assumptions?",
  "reviewer_score": 2,
  "reviewer_comment": "Suggests subtle role bias in occupation framing."
}
```

- **`reviewer_score`**: numeric or categorical value (scale defined in `cda_questions.json`).
- **`reviewer_comment`**: free‑form justification, highlights evidence from the text.

## Usage
### Load and inspect reviewer data
```python
import json, pandas as pd

rows = []
with open("Reviewer 1/cda_scores_gpt-4o.jsonl","r",encoding="utf-8") as f:
    for line in f:
        rows.append(json.loads(line))

df = pd.DataFrame(rows)
print(df.head())
```

### Cross‑reviewer agreement
```python
import glob

frames = []
for rev in ["Reviewer 1","Reviewer 2"]:
    for file in glob.glob(f"{rev}/cda_scores_*.jsonl"):
        model = file.split("_")[-1].replace(".jsonl","")
        d = pd.read_json(file, lines=True)
        d["reviewer"] = rev
        d["model"] = model
        frames.append(d)
df_all = pd.concat(frames)

# Example: agreement by scenario/variation/question
pivot = df_all.pivot_table(index=["scenario_id","variation","question_id"], 
                           columns="reviewer", values="reviewer_score", aggfunc="first")
pivot["agreement"] = pivot["Reviewer 1"] == pivot["Reviewer 2"]
print(pivot["agreement"].mean())
```

## Purpose
- Provide **human‑coded evidence** of discourse patterns (beyond numeric similarity/sentiment).
- Document **how evaluators interpreted model answers** relative to bias‑sensitive questions.
- Allow triangulation: machine metrics (see `E. Counterfactual Analysis`, `F. Adjective Analysis`) vs. human CDA insights.

## Notes
- Keep `cda_questions.json` under version control to make the evaluation framework transparent.
- Reviewer folders should remain independent (no edits across reviewers).
- Results can be aggregated for inter‑annotator agreement (IAA), thematic coding, or qualitative excerpts in papers.
