# LoRA Fine-Tuning for Text Summarization

This repository contains the code, configurations, and results for a study on LoRA (Low-Rank Adaptation) fine-tuning applied to text summarization using the CNN/DailyMail dataset. The project replicates and extends the experimental design of Wu & Wan (2025), applying it to English news summarization using Llama 3.2-1B and Qwen2.5-7B.

---

## Project Overview

**Task:** Abstractive news summarization  
**Dataset:** CNN/DailyMail (version 3.0.0)  
**Method:** LoRA fine-tuning via [LLamaFactory](https://github.com/hiyouga/LLaMA-Factory)  
**Evaluation Metrics:** BLEU-4, ROUGE-1, ROUGE-2, ROUGE-L  
**Models:**
- `meta-llama/Llama-3.2-1B` — 1.24B parameters
- `Qwen/Qwen2.5-7B` — 7B parameters

### LoRA Configuration

| Parameter | Value |
|-----------|-------|
| `lora_target` | all |
| `lora_alpha` | 32 × rank (256 for r=8, 512 for r=16) |
| `lora_dropout` | 0.05 |
| `cutoff_len` | 512 |
| `learning_rate` | 3e-4 |
| `per_device_train_batch_size` | 4 |
| `gradient_accumulation_steps` | 4 (effective batch = 16) |
| `num_train_epochs` | 3 |
| `lr_scheduler_type` | cosine |

---

## Repository Structure

```
LoRA-Text-Summarization/
├── llama/
│   ├── baseline/
│   │   ├── baseline_eval_1k.ipynb          # Baseline eval on 1,000 test examples
│   │   └── baseline_eval_11k.ipynb         # Baseline eval on full 11,490 test examples
│   ├── lora_1k/
│   │   ├── lora_r8_train_eval_1k.ipynb     # LoRA rank=8, train 1k, eval 1k
│   │   └── lora_r16_train_eval_1k.ipynb    # LoRA rank=16, train 1k, eval 1k
│   ├── lora_full/
│   │   ├── lora_r8_train_eval_287k.ipynb   # LoRA rank=8, train 287k, eval 11k
│   │   └── lora_r16_train_eval_287k.ipynb  # LoRA rank=16, train 287k, eval 11k
│   ├── configs/
│   │   ├── lora_r8_1k.yaml
│   │   ├── lora_r16_1k.yaml
│   │   ├── lora_r8_287k.yaml
│   │   └── lora_r16_287k.yaml
│   ├── models/
│   │   ├── lora_r8_1k_model/               # Trained LoRA adapter (rank=8, 1k)
│   │   ├── lora_r16_1k_model/              # Trained LoRA adapter (rank=16, 1k)
│   │   ├── lora_r8_287k_model/             # Trained LoRA adapter (rank=8, 287k)
│   │   └── lora_r16_287k_model/            # Trained LoRA adapter (rank=16, 287k)
│   └── results/
│       ├── baseline_results_1k.json
│       ├── baseline_results_11k.json
│       ├── lora_r8_1k_results.json
│       ├── lora_r16_1k_results.json
│       ├── lora_r8_287k_results.json
│       ├── lora_r16_287k_results.json
│       └── results_summary.ipynb           # Full results table + visualizations
├── qwen/
│   ├── baseline/
│   │   ├── baseline_eval_1k.ipynb
│   │   └── baseline_eval_11k.ipynb
│   ├── lora_1k/
│   │   ├── lora_r8_train_eval_1k.ipynb
│   │   └── lora_r16_train_eval_1k.ipynb
│   ├── lora_full/
│   │   ├── lora_r8_train_eval_287k.ipynb
│   │   └── lora_r16_train_eval_287k.ipynb
│   ├── configs/
│   │   ├── lora_r8_1k.yaml
│   │   ├── lora_r16_1k.yaml
│   │   ├── lora_r8_287k.yaml
│   │   └── lora_r16_287k.yaml
│   ├── models/
│   │   ├── lora_r8_1k_model/               # To be populated after training
│   │   ├── lora_r16_1k_model/              # To be populated after training
│   │   ├── lora_r8_287k_model/             # To be populated after training
│   │   └── lora_r16_287k_model/            # To be populated after training
│   └── results/
│       └── results_summary.ipynb           # Ready to run after experiments complete
└── README.md
```

---

## Llama 3.2-1B Results

All Llama experiments have been completed and results are reported below.

| Configuration | Train Samples | Test Samples | BLEU-4 | ROUGE-1 | ROUGE-2 | ROUGE-L |
|--------------|---------------|--------------|--------|---------|---------|---------|
| Baseline | — | 1,000 | 0.9546 | 15.6771 | 6.7044 | 11.3215 |
| Baseline | — | 11,490 | 2.0184 | 21.3652 | 10.8040 | 14.7681 |
| LoRA rank=8 | 1,000 | 1,000 | 2.3351 | 28.6097 | 9.4605 | 20.0208 |
| LoRA rank=16 | 1,000 | 1,000 | 2.3261 | 28.1723 | 9.0748 | 19.4923 |
| LoRA rank=8 | 258,401 | 11,490 | 5.5490 | 37.6981 | 16.8067 | 25.7951 |
| **LoRA rank=16** | **258,401** | **11,490** | 5.5308 | **37.8248** | **16.8288** | 25.7429 |

> Train samples for 287k experiments reflect 90% of 287,113 after `val_size: 0.1` holdout.

### Key Findings

- LoRA fine-tuning consistently and substantially outperforms the no-training baseline across all metrics
- Training data scale is the dominant factor — scaling from 1k to 287k examples more than doubled BLEU-4 and improved ROUGE-1 by over 9 points
- LoRA rank=8 and rank=16 perform comparably at full scale, with differences of less than 0.15 across all metrics — rank=8 is the more parameter-efficient choice

---

## Qwen2.5-7B Results

Qwen2.5-7B experiments are designed to be run on a university compute cluster. Notebooks are fully prepared and ready to execute. Results will be populated here once available.

| Configuration | Train Samples | Test Samples | BLEU-4 | ROUGE-1 | ROUGE-2 | ROUGE-L |
|--------------|---------------|--------------|--------|---------|---------|---------|
| Baseline | — | 1,000 | — | — | — | — |
| Baseline | — | 11,490 | — | — | — | — |
| LoRA rank=8 | 1,000 | 1,000 | — | — | — | — |
| LoRA rank=16 | 1,000 | 1,000 | — | — | — | — |
| LoRA rank=8 | 258,401 | 11,490 | — | — | — | — |
| LoRA rank=16 | 258,401 | 11,490 | — | — | — | — |

---

## How to Reproduce

### Requirements

```bash
pip install transformers peft datasets accelerate bitsandbytes rouge-score nltk evaluate
pip install git+https://github.com/hiyouga/LLaMA-Factory.git
```

### Running Llama Experiments

Each notebook is self-contained and runs top to bottom. Open the desired notebook in Google Colab with an A100 GPU and run all cells.

**Recommended order:**
1. `llama/baseline/baseline_eval_1k.ipynb` — quick sanity check (~30 mins)
2. `llama/baseline/baseline_eval_11k.ipynb` — full baseline (~1 hour)
3. `llama/lora_1k/lora_r8_train_eval_1k.ipynb` — small-scale LoRA (~20 mins)
4. `llama/lora_1k/lora_r16_train_eval_1k.ipynb` — small-scale LoRA (~20 mins)
5. `llama/lora_full/lora_r8_train_eval_287k.ipynb` — full-scale LoRA (~12 hours)
6. `llama/lora_full/lora_r16_train_eval_287k.ipynb` — full-scale LoRA (~12 hours)

### Running Qwen Experiments

Same notebook structure as Llama. Qwen2.5-7B is publicly available — no license acceptance required, only a HuggingFace access token is needed.

**Note for cluster users:** The notebooks use `device_map="auto"` and `bf16=true`, making them compatible with any GPU configuration. Adjust `BATCH_SIZE` and `per_device_train_batch_size` based on available VRAM:

| VRAM | Recommended batch size |
|------|----------------------|
| 40GB+ | 8 |
| 24GB | 4 |
| 16GB | 2 |

### Loading a Trained Adapter

```python
from transformers import AutoTokenizer, AutoModelForCausalLM
from peft import PeftModel
import torch

MODEL_NAME = "meta-llama/Llama-3.2-1B"

base_model = AutoModelForCausalLM.from_pretrained(
    MODEL_NAME,
    torch_dtype=torch.float16,
    device_map="auto"
)

model = PeftModel.from_pretrained(base_model, "llama/models/lora_r8_287k_model")
tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME)
tokenizer.padding_side = "left"
```

---

## Reference

Wu, Z., & Wan, C. (2025). *A Study of Text Summarization Based on Large Models*. DOI: https://doi.org/10.1145/3746709.3746945

---

## Author

Mohammad Aasim  
MS Data Science, Northeastern University  
GitHub: [aasimsk98](https://github.com/aasimsk98)
