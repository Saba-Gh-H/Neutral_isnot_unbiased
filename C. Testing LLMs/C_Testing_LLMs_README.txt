# 🧪 C. Testing LLMs

This folder contains scripts, configs, and run outputs for **prompting multiple LLMs** on the same neutral scenarios. It generates per‑model JSON files with answers to a fixed set of questions and supports resuming interrupted runs.

## Contents
- **`RandomNeutralScenarios.json`** — input scenarios shared across all tests.
- **`GPT4oNewRun/`**, **`Llama3.1NewRun/`**, **`mistral-small3.1-Run/`**, **`Phi4NewRun/`**, **`Qwen2.5-32B-NewRun/`** — per‑model run folders (scripts + outputs).

## How it works (example flow)
1. For each scenario, extract two names from a neutral pool.
2. Create **six variations** per scenario (original, swapped names, gender/age variants).
3. Ask a **universal question set** (5 questions) independently for each variation.
4. Send the prompt to the selected model backend (OpenAI API or your own server/endpoint).
5. Save answers **incrementally** to a JSON file so runs can resume.

## Dependencies
```
python>=3.9
requests
```
(Install with `pip install -r requirements.txt` or `pip install requests`.)

## Secrets & endpoints
- **OpenAI / GPT**: put your API key in a local `secret.py` **inside this folder**:
  ```python
  # secret.py
  OPENAI_API_KEY = "sk-your-key"
  ```
  *Do not commit this file.* Scripts will import `OPENAI_API_KEY` from `secret.py`.
- **LLaMA/Mistral/others via your server**: set your own base URL (e.g., Ollama or any compatible gateway):
  ```python
  ollama_url = "http://<YOUR_SERVER_IP>:11434/api/generate"
  ```

## Example runner (LLaMA via Ollama)
The sample script:
- reads `RandomNeutralScenarios.json`,
- builds the six text variations,
- calls the endpoint with retry logic,
- writes results to **`llama3.1_answers_RUN.json`**,
- resumes automatically if the file already exists.

**Important bits to adapt:**
```python
llama_model = "llama3.1:70b"                  # change to your local tag
ollama_url = "http://<YOUR_SERVER_IP>:11434/api/generate"  # your server
output_file = "llama3.1_answers_RUN.json"     # output path/name
```
The response extraction for Ollama should use:
```python
response.json()["response"]
```

## Running
```bash
python llama_runner.py     # or the specific model script in its folder
```
The console will show progress like:
```
📍 Scenario 12 - Swapped Names - Question_3
```
and write/append the JSON file after each answer.

## Output format (excerpt)
```json
{
  "Scenario_1": {
    "Original": {
      "Question_1": "…",
      "Question_2": "…"
    },
    "Swapped Names": { "Question_1": "…" }
  },
  "Scenario_2": { "...": "..." }
}
```
Each run file contains all variations & questions for each scenario.

## Tips
- Keep temperature/moderation consistent across models for fair comparison.
- If you run behind a rate‑limited gateway, the script already backs off and retries.
- You can change the **question set** or **variation templates** centrally in the script.
- Store different model runs in their dedicated subfolders to avoid overwriting outputs.

## Safety & reproducibility
- Results depend on model versions and server builds; log the model tag (e.g., `llama3.1:70b`) and date.
- Avoid committing any keys or internal URLs.
- Consider pinning `requests` and gateway versions used for runs.
