# Eedi — Mining Misconceptions in Mathematics

**Competition solution:** an NLP system that predicts the affinity between distractors in multiple-choice mathematics questions and the underlying misconceptions.

The goal is to replace slow, inconsistent manual matching of each distractor to a misconception with an automated (or semi-automated) approach that covers known misconceptions and generalises to new ones, reducing labelling effort and improving educational quality. Evaluation is **MAP@25** — up to 25 predicted misconceptions per test instance, scored on how accurately and how high the correct one ranks.

---

## Repository contents

| Path | What it is |
|---|---|
| `eedi_code/` | Full solution code (notebooks, training scripts, sample data) |
| `eedi_code/generate.ipynb` | Data generation — uses GPT-4o to generate missing misconceptions and build training samples |
| `eedi_code/infer.ipynb` | Inference / submission pipeline |
| `eedi_code/train/llm_n6.py` | Fine-tuning script (Qwen, contrastive learning, 4-bit + LoRA) |
| `eedi_code/train/qwen_v10.py` | Fine-tuning script (Qwen variant) |
| `eedi_code/data/` | Sample data: `misconception_mapping.csv`, `val_misconceptions.csv`, `misconception_data_all_train_gpt-4o.xlsx` |
| `Eedi competition.docx` | Competition overview and algorithm description |
| `Eedi Explaination (Singapore).pdf` | Detailed solution explanation (Singapore round) |

---

## Algorithm summary

1. **Data generation & training** — GPT-4o generates missing misconceptions in the official dataset; each sample's basic information is turned into training data. Qwen-14b-instruct and Qwen-32b-instruct are fine-tuned with **contrastive learning**, using 4-bit quantisation and **LoRA** on multiple linear modules.
2. **Retrieval & ensemble** — embeddings from three differently trained LLMs (or different parameter settings) are concatenated, then candidates are selected by similarity-based retrieval on the combined embeddings.
3. **Reranking** — Qwen-32b-instruct-AWQ reranks the top 25 retrieval results via zero-shot prompts (processed in batches); `bge-large-en-v1.5` performs similarity search over a retrieval library to finalise results.

---

## Notes

- The `api_key` in `generate.ipynb` is **redacted** (`"****"`). Supply your own credentials (e.g. an OpenAI-compatible endpoint) before running data generation.
- The large training artefacts and full datasets are not included; `eedi_code/data/` holds representative samples only.
- Notebooks were authored on Kaggle; GPU/vLLM environment specifics appear in the cells.

---

*Competition: Eedi — Mining Misconceptions in Mathematics (Kaggle).*
