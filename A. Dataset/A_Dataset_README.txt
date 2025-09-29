# 📂 A. Dataset

This folder contains the dataset of scenarios used for **bias detection experiments with LLMs**.  
The scenarios here are designed to be **neutral** and will later serve as inputs for LLM evaluations, where models are asked to respond to questions and their outputs are analyzed for potential bias.

## Contents

- **`RandomNeutralScenarios.json`**  
  A JSON file containing a collection of **neutral scenarios**.  
  These scenarios form the baseline dataset, ensuring that model responses can be compared without introducing bias from the dataset itself.

- **`.ipynb_checkpoints/`**  
  Auto-generated Jupyter notebook checkpoint folder. Can be ignored in most cases.

## Usage

1. Load the dataset in your analysis scripts:
   ```python
   import json

   with open("RandomNeutralScenarios.json", "r", encoding="utf-8") as f:
       scenarios = json.load(f)
   ```

2. Pass the scenarios to your **LLM evaluation pipeline** to gather responses for further analysis.

## Notes
- The dataset is **neutral by design**, ensuring that any bias observed comes from the model itself, not the input.
- Any additional datasets (e.g., counterfactual or adjective-based variations) will be found in later folders.
