# Neutral Is Not Unbiased: Evaluating Implicit and Intersectional Identity Bias in LLMs Through Structured Narrative Scenarios

**Authors:** Saba Ghanbari Haez, Mauro Dragoni  
📄 To appear at **EMNLP 2025**  
🔗 [OpenReview link](https://openreview.net/forum?id=I4iEZPMI66)

---

## Abstract
Large Language Models often reproduce societal biases, yet most evaluations overlook how such biases evolve across nuanced contexts or intersecting identities. We introduce a scenario-based evaluation framework built on 100 narrative tasks, designed to be neutral at baseline and systematically modified with gender and age cues. Grounded in the theory of **Normative-Narrative Scenarios**, our approach provides ethically coherent and socially plausible settings for probing model behavior.  

Analyzing responses from five leading LLMs—**GPT-4o, LLaMA 3.1, Qwen2.5, Phi-4, and Mistral**—using **Critical Discourse Analysis (CDA)** and quantitative linguistic metrics, we find consistent evidence of bias. Gender emerges as the dominant axis of bias, with intersectional cues (e.g., age and gender combined) further intensifying disparities. Our results underscore the value of dynamic narrative progression for detecting implicit, systemic biases in Large Language Models.

---

## Repository Structure

```
Codes_LLM_Runs&Analyses/
│
├── A. Dataset/               # 100 neutral scenarios + JSON dataset
│   └── RandomNeutralScenarios.json
│
├── B. Dataset Statistics/    # Statistical summaries (length, TTR, sentiment, readability)
│
├── C. Testing LLMs/          # Scripts for querying GPT-4o, LLaMA 3.1, Qwen2.5, Phi-4, Mistral
│
├── D. LLM Responses/         # Example outputs per model in JSON
│
├── E. Counterfactual Analysis/ # Scripts for semantic similarity & sentiment shift analysis
│
├── F. Adjective Analysis/    # Adjective extraction, sentiment, lexical richness
│
├── G. CDA/                   # Critical Discourse Analysis scores & reviewer files
│   ├── Reviewer 1/
│   ├── Reviewer 2/
│   └── cda_questions.json
│
└── README.md                 # (this file)
```

---

## Scenario Variations

Each of the 100 scenarios is expanded into **6 variations** (600 prompts total):

1. **Original (Neutral)** – baseline with neutral names  
2. **Swapped Names** – role-preserving, identity-irrelevant control  
3. **Gender-coded (N1=Female, N2=Male)**  
4. **Gender-swapped (N1=Male, N2=Female)**  
5. **Gender + Age-coded (N1=Female Younger, N2=Male Older)**  
6. **Gender + Age-swapped (N1=Male Younger, N2=Female Older)**  

This progression enables studying **implicit bias → explicit bias → intersectional bias**.

---

## Adding New Identity Cues

To extend this framework to new social dimensions (e.g., race, class, occupation):

- Start with **neutral scaffolds** (no overt identity markers).  
- Introduce **single cues** (e.g., racial identity, occupation).  
- Add **compound cues** (e.g., race + gender, class + age).  
- Always ensure **semantic plausibility** (tasks remain socially coherent).  
- Use **counterfactual swapping** to check for robustness.  

This preserves comparability while exposing **intersectional disparities**.

---

## Running the Code

### Example: Extract Adjectives from Responses
```bash
python adjective_analysis.py
```

### Example: Counterfactual Embedding Shift
```bash
python counterfactual_similarity.py --input LLM_responses/
```

### Example: Generate CDA Tables
```bash
python cda_analysis.py --reviewer Reviewer1 --questions cda_questions.json
```

---

## Citation (Preprint)

Please cite the preprint if you use this framework:

```bibtex
@inproceedings{haez2025neutral,
  title={Neutral Is Not Unbiased: Evaluating Implicit and Intersectional Identity Bias in {LLM}s Through Structured Narrative Scenarios},
  author={Saba Ghanbari Haez and Mauro Dragoni},
  booktitle={The 2025 Conference on Empirical Methods in Natural Language Processing},
  year={2025},
  url={https://openreview.net/forum?id=I4iEZPMI66}
}
```

---

⚖️ **Ethics Note:**  
All scenarios are fictional and constructed with neutral, harm-free language. Analyses are designed to **highlight and contextualize bias**, not reinforce it.
