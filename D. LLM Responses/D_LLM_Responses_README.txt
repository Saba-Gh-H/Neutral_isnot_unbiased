# 🗂️ D. LLM Responses

This folder contains **example model outputs** from the testing phase.  
Each file is a JSON dump of answers produced by a specific model when prompted on the shared scenario set (see `C. Testing LLMs`). These examples are included to make the analysis reproducible and to illustrate typical structures, **but your own runs will regenerate these files** with different content.

## Contents (examples)
- `gpt-4o_answers_RUN.json`
- `llama3.1_answers_RUN.json`
- `mistral-small3.1_answers_RUN.json`
- `phi4_answers_RUN.json`
- `qwen2.5_32b_answers_RUN.json`

> File names follow the pattern: `<model>_answers_RUN.json`

## JSON structure
Each file contains a nested dictionary:
```json
{
  "Scenario_1": {
    "Original": {
      "Question_1": "…model answer…",
      "Question_2": "…"
    },
    "Swapped Names": {
      "Question_1": "…"
    }
  },
  "Scenario_2": {
    "Name 1 Female, Name 2 Male": { "Question_4": "…" }
  }
}
```
- **Level 1**: `Scenario_{idx}` — scenario index from the input dataset.
- **Level 2**: variation label (e.g., *Original*, *Swapped Names*, gender/age variants).
- **Level 3**: `Question_{1..5}` → free‑text answer.

This structure is produced by the runner in `C. Testing LLMs` and lets you resume runs safely.

## Load & explore (Python)
Use this to read **one** model file and inspect its content:
```python
import json, pprint

with open("llama3.1_answers_RUN.json", "r", encoding="utf-8") as f:
    data = json.load(f)

# Pretty print a single scenario
pprint.pprint(data["Scenario_1"], depth=2)
```

### Flatten to a DataFrame (multiple files)
```python
import json, glob, pandas as pd, re, os

rows = []
for path in glob.glob("*_answers_RUN.json"):
    model = re.sub(r"_answers_RUN\.json$", "", os.path.basename(path))
    with open(path, "r", encoding="utf-8") as f:
        blob = json.load(f)
    for scen_key, variations in blob.items():
        scen_idx = int(scen_key.split("_")[1])
        for var_label, qdict in variations.items():
            for q_key, answer in qdict.items():
                q_idx = int(q_key.split("_")[1])
                rows.append({
                    "model": model,
                    "scenario": scen_idx,
                    "variation": var_label,
                    "question": q_idx,
                    "answer": answer
                })

df = pd.DataFrame(rows).sort_values(["model","scenario","variation","question"])
print(df.head())
```

### Validate completeness
```python
# Expected set of (variation, question) pairs:
expected_vars = {
    "Original",
    "Swapped Names",
    "Name 1 Female, Name 2 Male",
    "Name 1 Male, Name 2 Female",
    "Name 1 Female Younger, Name 2 Male Older",
    "Name 1 Male Younger, Name 2 Female Older",
}
expected_qs = {1,2,3,4,5}

missing = (df
    .groupby(["model","scenario","variation"])["question"]
    .apply(lambda s: expected_qs - set(s))
    .reset_index(name="missing_questions"))

print(missing[missing["missing_questions"].astype(bool)])
```

## Notes
- These files are **examples** to support the downstream analysis and documentation of observations; your live runs will overwrite or create new files.
- Keep raw outputs; do not manually edit answers. Post‑processing/cleaning should happen in analysis notebooks/scripts.
- Avoid committing secrets, endpoints, or any scenario content that might contain sensitive data (should be neutral by design).

## Reproducibility
Record: model tag (e.g., `llama3.1:70b`), date, parameters (temperature, top_p, penalties), and the exact runner version used to generate each file.
