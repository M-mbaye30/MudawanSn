# 🌍 MudawanSn: A Gold-Standard Wolof–Arabic Parallel Corpus for Machine Translation

> A publicly available parallel corpus for the Wolof–Arabic language pair, a gold-standard resource containing 1,271 sentence-aligned pairs. The corpus consists of manual translations from Wolof into Modern Standard Arabic (MSA). The source texts are drawn from the MasakhaNER corpus, covering politics, society, religion, and sports in Senegalese news discourse.

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![Hugging Face Dataset](https://img.shields.io/badge/%F0%9F%A4%97-Hugging%20Face-blue.svg)](https://huggingface.co/datasets/mbaye930/wolof-arabic-parallel-corpus)

---

## 📖 Dataset Description

This corpus consists of **1,271 gold-standard, sentence-aligned pairs** manually translated from Wolof into Modern Standard Arabic (MSA). The source Wolof texts are drawn from the [MasakhaNER](https://github.com/masakhane-io/masakhane-ner) project, which crawled articles from major Senegalese online news portals (Seneweb, Jotna News, YerimPost, and SocialNetLink) covering politics, society, religion, and sports.

### 📊 Corpus Splits & Statistics

The corpus is divided into fixed, standard splits to ensure fair and reproducible benchmarking across future research:
*   **`train`**: 1,080 sentence pairs (available in `data/train.tsv`)
*   **`validation`**: 95 sentence pairs (available in `data/dev.tsv`)
*   **`test`**: 96 sentence pairs (available in `data/test.tsv`)


### 📂 Data Fields

Each parallel sentence pair contains the following fields:
*   `id`: Unique identifier for the parallel sentence pair.
*   `wolof`: The sentence in Wolof.
*   `arabic`: The manual translation in Modern Standard Arabic.
*   `french_source`: The original French news text from which the Wolof source sentence was translated.
*   `source_dataset`: The original source dataset.
*   `source_type`: Category of the text source.

---

## 📦 How to Load the Dataset

You can easily load this dataset directly from Hugging Face using the `datasets` library:

```python
from datasets import load_dataset

dataset = load_dataset("mbaye930/wolof-arabic-parallel-corpus")

# Access the splits
train_data = dataset["train"]
val_data = dataset["validation"]
test_data = dataset["test"]

# Print a sample
print(train_data[0])
```

---

## 🛠️ Dataset Summary & Quality Control

*   **Translation Process**: The parallel corpus was translated manually from Wolof to Arabic. Crucially, the translations were produced directly from the Wolof source texts by a native Wolof speaker and Arabic scholar, avoiding pivot-language translation errors.
*   **Alignment & Verification**: Cross-lingual semantic verification was conducted using a LASER3 semi-automated loop, verifying that the cosine similarity scores of the pairs were high and filtering out misaligned or erroneous source texts. The average LASER3 cosine similarity is 0.75, indicating high semantic alignment.

---

## 🔬 Baseline Benchmarks

To establish reliable reference benchmarks for the community, we report translation experiments using lexical metrics (**sacreBLEU**, **chrF++**) and the state-of-the-art African-centric semantic neural metric (**AfriCOMET**). We evaluate four architectures in both Zero-shot and Fine-tuned settings:

| Direction | Model | BLEU | chrF++ | AfriCOMET |
| :--- | :--- | :---: | :---: | :---: |
| **wo ➔ ar** | NLLB-200 (600M) (Zero-shot) | 1.12 | 15.42 | -12.22 |
| | **NLLB-200 (600M) (Fine-tuned)** | **6.47** | **29.31** | **0.19** |
| | mT5-base (Zero-shot) | 0.04 | 0.17 | -40.43 |
| | **mT5-base (Fine-tuned)** | **0.43** | **11.10** | **-31.98** |
| | AfriNLLB-12 (Zero-shot) | 3.97 | 25.39 | -28.38 |
| | **AfriNLLB-12 (Fine-tuned)** | **7.76** | **30.72** | **-24.47** |
| | AfriNLLB-8 (Zero-shot) | 4.53 | 25.53 | -29.86 |
| | **AfriNLLB-8 (Fine-tuned)** | **7.05** | **28.40** | **-28.66** |
| | | | | |
| **ar ➔ wo** | NLLB-200 (600M) (Zero-shot) | 3.14 | 18.24 | 36.49 |
| | **NLLB-200 (600M) (Fine-tuned)** | **7.28** | **30.87** | **41.93** |
| | mT5-base (Zero-shot) | 0.02 | 2.57 | -41.11 |
| | **mT5-base (Fine-tuned)** | **0.09** | **2.50** | **-50.83** |
| | AfriNLLB-12 (Zero-shot) | 3.08 | 21.09 | -14.71 |
| | **AfriNLLB-12 (Fine-tuned)** | **8.75** | **33.08** | **-3.18** |
| | AfriNLLB-8 (Zero-shot) | 5.66 | 26.31 | -7.49 |
| | **AfriNLLB-8 (Fine-tuned)** | **8.18** | **31.95** | **-1.53** |

### 💡 Key Findings
1. **Strong Data Quality**: Fine-tuning systematically improves both NLLB and AfriNLLB models in both directions, yielding up to **+5.67 BLEU** and **+12.63 chrF++** gains. This validates the semantic coherence and high quality of the parallel translations.
2. **SOTA African Adaptation**: The specialized **AfriNLLB-12** and **AfriNLLB-8** models achieve the best overall scores, proving that leveraging sub-Saharan language pre-training transfers successfully to the Wolof-Arabic pair.
3. **AfriCOMET Arabic Bottleneck**: The neural metric AfriCOMET (based on `AfroXLM-R`) exhibits low scores when Arabic is the target language due to the poor representation of Arabic in the backbone vocabulary, illustrating the need to combine lexical and semantic metrics.

---

## 🚀 How to Reproduce

All data splits and reproduction codes are fully packaged. To reproduce the baselines:

### 1. Requirements
Ensure your Python environment has the following libraries installed:
```bash
pip install torch transformers datasets accelerate sacrebleu unbabel-comet
```

### 2. Running the Experiments
All models (NLLB-200, mT5-base, AfriNLLB-12, and AfriNLLB-8) can be fine-tuned and evaluated step-by-step by opening and executing the Jupyter Notebooks inside the `notebooks/` folder:
*   [baseline_nllb.ipynb](notebooks/baseline_nllb.ipynb): Fine-tuning NLLB-200 (600M).
*   [baseline_mt5.ipynb](notebooks/baseline_mt5.ipynb): Fine-tuning mT5-base.
*   [baseline_afrinllb.ipynb](notebooks/baseline_afrinllb.ipynb): Fine-tuning AfriNLLB-12 and AfriNLLB-8.

---

## 👥 Contributors

*   **Mouhamed Mbaye** - [mouhamedmbaye371@gmail.com](mailto:mouhamedmbaye371@gmail.com)
*   **Thierno Diop** - [thiernodiop0@gmail.com](mailto:thiernodiop0@gmail.com)

---

## 📄 License

This dataset is released under the [**Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)**](https://creativecommons.org/licenses/by-nc/4.0/) license.

---

## 📌 Citation

If you use this corpus or the baseline code in your research, please cite our paper:

### 1. The Data Paper (Baseline Benchmarks)
```bibtex
@inproceedings{mbaye&diop2026gold,
  title     = {MudawanSn: A Gold-Standard Wolof--Arabic Parallel Corpus for Machine Translation},
  author    = {Mbaye, Mouhamed and Diop, Thierno },
  year      = {2026}
}
```

