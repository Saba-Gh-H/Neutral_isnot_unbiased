Neutral Is Not Unbiased: Evaluating Implicit and Intersectional Identity Bias in LLMs Through Structured Narrative Scenarios

Paper Authors: Saba Ghanbari Haez, Mauro Dragoni
Repository implementation and software: Saba Ghanbari Haez
📄 Published in Findings of the Association for Computational Linguistics: EMNLP 2025
🔗 ACL Anthology
DOI: 10.18653/v1/2025.findings-emnlp.814

Abstract

Large Language Models often reproduce societal biases, yet most evaluations overlook how such biases evolve across nuanced contexts or intersecting identities. We introduce a scenario-based evaluation framework built on 100 narrative tasks, designed to be neutral at baseline and systematically modified with gender and age cues. Grounded in the theory of Normative-Narrative Scenarios, our approach provides ethically coherent and socially plausible settings for probing model behavior.

Analyzing responses from five leading LLMs—GPT-4o, LLaMA 3.1, Qwen2.5, Phi-4, and Mistral—using Critical Discourse Analysis (CDA) and quantitative linguistic metrics, we find consistent evidence of bias. Gender emerges as the dominant axis of bias, with intersectional cues (e.g., age and gender combined) further intensifying disparities. Our results underscore the value of dynamic narrative progression for detecting implicit, systemic biases in Large Language Models.

Repository Structure

Codes_LLM_Runs&Analyses/
│
├── A. Dataset/                  # 100 neutral scenarios + JSON dataset
│   └── RandomNeutralScenarios.json
│
├── B. Dataset Statistics/       # Statistical summaries
│
├── C. Testing LLMs/             # Scripts for querying the evaluated LLMs
│
├── D. LLM Responses/            # Example model outputs in JSON
│
├── E. Counterfactual Analysis/  # Semantic similarity & sentiment-shift analysis
│
├── F. Adjective Analysis/       # Adjective extraction, sentiment, lexical richness
│
├── G. CDA/                      # Critical Discourse Analysis scores & reviewer files
│   ├── Reviewer 1/
│   ├── Reviewer 2/
│   └── cda_questions.json
│
├── CITATION.cff
├── LICENSE
└── README.md

Scenario Variations

Each of the 100 scenarios is expanded into 6 variations (600 prompts total):

Original (Neutral) – baseline with neutral names

Swapped Names – role-preserving, identity-irrelevant control

Gender-coded (N1=Female, N2=Male)

Gender-swapped (N1=Male, N2=Female)

Gender + Age-coded (N1=Female Younger, N2=Male Older)

Gender + Age-swapped (N1=Male Younger, N2=Female Older)

This progression enables studying implicit bias → explicit bias → intersectional bias.

Adding New Identity Cues

To extend this framework to new social dimensions (e.g., race, class, occupation):

Start with neutral scaffolds (no overt identity markers).

Introduce single cues (e.g., racial identity, occupation).

Add compound cues (e.g., race + gender, class + age).

Always ensure semantic plausibility (tasks remain socially coherent).

Use counterfactual swapping to check for robustness.

This preserves comparability while exposing intersectional disparities.

Running the Code

Example: Extract Adjectives from Responses

python adjective_analysis.py

Example: Counterfactual Embedding Shift

python counterfactual_similarity.py --input LLM_responses/

Example: Generate CDA Tables

python cda_analysis.py --reviewer Reviewer1 --questions cda_questions.json

Citation and Use Requirements

Use of the software, scripts, analysis pipelines, or other original research materials contained in this repository is subject to the terms in the repository's LICENSE file.

Citation is required

If you use this repository, its software, or a substantial part of its implementation in a publication, manuscript, preprint, thesis, report, benchmark, dataset, presentation, or other research output, you must cite the associated publication:

Saba Ghanbari Haez and Mauro Dragoni.
Neutral Is Not Unbiased: Evaluating Implicit and Intersectional Identity Bias in LLMs Through Structured Narrative Scenarios.
Findings of the Association for Computational Linguistics: EMNLP 2025, pages 15060–15088, Suzhou, China. Association for Computational Linguistics, 2025.

Paper on ACL Anthology

Modification disclosure is required

If you modify, adapt, extend, or build upon the software or research materials in this repository, you must clearly state that modifications were made.

The resulting work or accompanying documentation must identify, with reasonable specificity, the portions that were modified, for example:

files or scripts that were changed;

components or stages of the analysis pipeline that were modified;

additions or removals to the original implementation;

changes to scenario construction, identity variations, evaluation procedures, or analysis methodology, where applicable.

Modified versions must not be presented as the original implementation.

Attribution

The original repository software and implementation should be attributed to:

Saba Ghanbari Haez

Authorship of the associated scientific publication is separate from authorship of the repository software. The associated publication is co-authored by Saba Ghanbari Haez and Mauro Dragoni and should be cited accordingly.

Redistribution

Copies or substantial portions of the software must retain the applicable copyright notice, licence notice, citation requirement, and modification-disclosure requirement.

Please see the LICENSE file for the complete terms.

Citation

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

Author Contact

Saba Ghanbari Haez

For questions concerning the repository implementation, software, reuse, or licensing, please contact the repository maintainer.

Ethics Note

All scenarios are fictional and constructed with neutral, harm-free language. Analyses are designed to highlight and contextualize bias, not reinforce it.
