# Neutral Is Not Unbiased: Evaluating Implicit and Intersectional Identity Bias in LLMs Through Structured Narrative Scenarios

<p align="center">
  <strong>Findings of EMNLP 2025</strong><br>
  <a href="https://aclanthology.org/2025.findings-emnlp.814/">ACL Anthology</a>
  &nbsp;•&nbsp;
  <a href="https://doi.org/10.18653/v1/2025.findings-emnlp.814">DOI</a>
</p>

---

## Authors

**Paper authors:** Saba Ghanbari Haez, Mauro Dragoni  
**Repository implementation and software:** Saba Ghanbari Haez

---

## Abstract

> [!NOTE]
> Large Language Models often reproduce societal biases, yet most evaluations overlook how such biases evolve across nuanced contexts or intersecting identities. We introduce a scenario-based evaluation framework built on **100 narrative tasks**, designed to be neutral at baseline and systematically modified with gender and age cues. Grounded in the theory of **Normative-Narrative Scenarios**, our approach provides ethically coherent and socially plausible settings for probing model behavior.
>
> Analyzing responses from five leading LLMs — **GPT-4o, LLaMA 3.1, Qwen2.5, Phi-4, and Mistral** — using **Critical Discourse Analysis (CDA)** and quantitative linguistic metrics, we find consistent evidence of bias. Gender emerges as the dominant axis of bias, with intersectional cues such as age and gender combined further intensifying disparities.
>
> Our results underscore the value of dynamic narrative progression for detecting **implicit and systemic bias** in Large Language Models.

---

# Evaluation Framework

```text
┌──────────────────────────────┐
│     100 Neutral Scenarios    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│     6 Scenario Variations    │
│                              │
│  1. Original / Neutral       │
│  2. Swapped Names            │
│  3. Gender-coded             │
│  4. Gender-swapped           │
│  5. Gender + Age-coded       │
│  6. Gender + Age-swapped     │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        LLM Evaluation        │
│                              │
│  GPT-4o                      │
│  LLaMA 3.1                   │
│  Qwen2.5                     │
│  Phi-4                       │
│  Mistral                     │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│      Analysis Pipeline       │
│                              │
│  • Counterfactual Analysis   │
│  • Sentiment Shift           │
│  • Adjective Analysis        │
│  • Lexical Richness          │
│  • CDA                       │
└──────────────────────────────┘
```

---

## Scenario Variations

Each of the **100 scenarios** is expanded into **6 variations**, producing **600 prompts in total**.

| Variation | Description |
|---|---|
| **1. Original (Neutral)** | Baseline with neutral names |
| **2. Swapped Names** | Role-preserving, identity-irrelevant control |
| **3. Gender-coded** | N1 = Female, N2 = Male |
| **4. Gender-swapped** | N1 = Male, N2 = Female |
| **5. Gender + Age-coded** | N1 = Female Younger, N2 = Male Older |
| **6. Gender + Age-swapped** | N1 = Male Younger, N2 = Female Older |

> **Progression:**  
> `implicit bias → explicit bias → intersectional bias`

---

# Repository Structure

```text
Neutral_isnot_unbiased/
│
├── A. Dataset/
│   └── RandomNeutralScenarios.json
│
├── B. Dataset Statistics/
│   └── Statistical summaries
│
├── C. Testing LLMs/
│   └── Scripts for querying the evaluated models
│
├── D. LLM Responses/
│   └── Example model outputs in JSON
│
├── E. Counterfactual Analysis/
│   └── Semantic similarity and sentiment-shift analysis
│
├── F. Adjective Analysis/
│   └── Adjective extraction, sentiment, and lexical richness
│
├── G. CDA/
│   ├── Reviewer 1/
│   ├── Reviewer 2/
│   └── cda_questions.json
│
├── CITATION.cff
├── LICENSE
├── LICENSE-CC-BY
└── README.md
```

---

# Adding New Identity Cues

To extend the framework to additional social dimensions such as race, class, or occupation:

- Start with **neutral scaffolds** without overt identity markers.
- Introduce **single identity cues**.
- Add **compound / intersectional cues**.
- Preserve **semantic plausibility** across scenario variants.
- Use **counterfactual swapping** to test robustness.

> [!TIP]
> The goal is to preserve comparability across scenario variants while exposing systematic and intersectional disparities.

---

# Running the Code

### Extract adjectives from model responses

```bash
python adjective_analysis.py
```

### Run counterfactual embedding-shift analysis

```bash
python counterfactual_similarity.py --input LLM_responses/
```

### Generate CDA tables

```bash
python cda_analysis.py --reviewer Reviewer1 --questions cda_questions.json
```

---

# Citation and Use Requirements

> [!IMPORTANT]
> **Citation is required when this repository, its software, scripts, analysis pipeline, or a substantial part of its implementation is used in research or scholarly output.**
>
> Any modified or extended version must also **clearly state that modifications were made** and identify, with reasonable specificity, **which parts of the original repository were changed**.
>
> Full legal terms are provided in the repository's **LICENSE** file.

### Required citation

```bibtex
@inproceedings{haez-dragoni-2025-neutral,
    title = "Neutral Is Not Unbiased: Evaluating Implicit and Intersectional Identity Bias in {LLM}s Through Structured Narrative Scenarios",
    author = "Ghanbari Haez, Saba and Dragoni, Mauro",
    booktitle = "Findings of the Association for Computational Linguistics: EMNLP 2025",
    month = nov,
    year = "2025",
    address = "Suzhou, China",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2025.findings-emnlp.814/",
    doi = "10.18653/v1/2025.findings-emnlp.814",
    pages = "15060--15088"
}
```

📄 **Paper:**  
https://aclanthology.org/2025.findings-emnlp.814/

---

## Modification Disclosure

If you **modify, adapt, extend, or build upon** this repository, you must clearly indicate that the resulting version differs from the original.

The modified work or accompanying documentation should identify the affected parts, for example:

```text
Modified components:
- C. Testing LLMs/model_query.py
- E. Counterfactual Analysis/
- Scenario-generation procedure
- Identity-cue construction
- Evaluation or analysis methodology
```

Modified versions must not be presented as the unmodified original implementation.

---

## Attribution

The original repository software and implementation should be attributed to:

**Saba Ghanbari Haez**

The associated scientific publication is co-authored by:

**Saba Ghanbari Haez and Mauro Dragoni**

Paper authorship and repository/software authorship are therefore stated separately.

---

## Redistribution

Copies or substantial portions of the software must retain:

- the applicable copyright notice;
- the licence notice;
- the citation requirement; and
- the modification-disclosure requirement.

Please see the **LICENSE** file for the complete terms.

---

# Ethics Note

> [!NOTE]
> All scenarios are fictional and constructed with neutral, harm-free language. Analyses are designed to **highlight and contextualize bias**, not reinforce it.

---

# Contact

**Saba Ghanbari Haez**

For questions concerning the repository implementation, software reuse, citation, or licensing, please contact the repository maintainer.
