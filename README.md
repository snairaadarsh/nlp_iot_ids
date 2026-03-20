# NLP-Driven IoT Intrusion Detection: A Comprehensive Comparative Study of Machine Learning, Deep Learning, and Transformer Models

**NLP-Driven Intrusion Detection for IoT Networks**  
A comparative study of Machine Learning, Deep Learning, and Transformer models applied to the TON_IoT dataset using structured text serialization.

---

## Overview

This project frames IoT network intrusion detection as a **text classification problem**. Rather than feeding raw numerical features into a classifier, each network connection record from the TON_IoT dataset is converted into a natural language sentence through a deterministic text serialization pipeline. Thirteen models across three paradigms are then trained and evaluated on this NLP representation.

```
tcp protocol http service connection state SF low duration
high outgoing bytes medium incoming bytes dns rejected
```
↑ Example serialized network record fed to classifiers.

---

## Repository Structure

```
nlp-iot-ids/
│
├── dataset/
│   ├── train_test_network.csv        # Raw TON_IoT network traffic data
│   └── preprocessed_dataset.csv      # Cleaned and serialized NLP dataset
│
├── notebooks/
│   ├── EDA.ipynb                     # Exploratory data analysis
│   ├── preprocessing.ipynb           # Feature selection & text serialization
│   ├── ml_models.ipynb               # ML model training & evaluation
│   ├── dl_models.ipynb               # Deep learning model training & evaluation
│   └── transformer_models.ipynb      # Transformer fine-tuning & evaluation
│
└── README.md
```

---

## Pipeline

```
Raw TON_IoT CSV
      │
      ▼
Exploratory Data Analysis       ← EDA.ipynb
      │
      ▼
Feature Selection + Preprocessing
+ Text Serialization            ← preprocessing.ipynb
      │
      ▼
TON_IoT_NLP_Dataset.csv
      │
      ├──▶ ML Models            ← ml_models.ipynb
      ├──▶ DL Models            ← dl_models.ipynb
      └──▶ Transformer Models   ← transformer_models.ipynb
```

---

## Dataset

The project uses the [TON_IoT dataset](https://research.unsw.edu.au/projects/toniot-datasets) — a comprehensive IoT/IIoT network traffic benchmark collected from a realistic testbed with Zeek-captured traffic across attack categories including:

- Scanning, DoS, Ransomware, Backdoor, Injection, MitM, and Benign traffic

**Task:** Binary classification — Normal (0) vs. Attack (1)  
**Raw size:** ~22 million records, 44 features  
**Features retained after EDA:** 16

> The raw dataset is not included in this repository due to size. Download it from the [TON_IoT official page](https://research.unsw.edu.au/projects/toniot-datasets) and place `train_test_network.csv` in the `dataset/` folder before running the notebooks.

---

## Models Evaluated

| Paradigm | Models |
|---|---|
| **Machine Learning** | Logistic Regression, Naive Bayes, SVM (LinearSVC), Random Forest, XGBoost |
| **Deep Learning** | TextCNN, Bi-LSTM, Bi-GRU, Bi-LSTM + Attention, CNN-LSTM Hybrid |
| **Transformer** | DistilBERT, BERT-base, RoBERTa-base |

---

## Results Summary

All models evaluated on 7 metrics: Accuracy, Precision, Recall, F1-score, AUC, MCC, Cohen's Kappa.

| Paradigm | Best Model | F1 | AUC | MCC |
|---|---|---|---|---|
| ML | Random Forest | 0.9408 | 0.9839 | 0.8354 |
| DL | CNN-LSTM Hybrid | 0.9401 | 0.9837 | 0.8335 |
| Transformer | DistilBERT | 0.9406 | 0.9839 | 0.8347 |

Cross-paradigm F1 differences span only **0.0007**, demonstrating that domain-aware text serialization produces representations exploitable even by classical classifiers — substantially reducing the performance advantage typically conferred by architectural complexity.

---

## Setup & Usage

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/nlp-iot-ids.git
cd nlp-iot-ids
```

### 2. Install dependencies

```bash
pip install pandas numpy scikit-learn xgboost tensorflow torch transformers \
            datasets accelerate imbalanced-learn matplotlib seaborn jupyter
```

### 3. Run notebooks in order

```
1. EDA.ipynb                  # Understand the dataset
2. preprocessing.ipynb        # Generate TON_IoT_NLP_Dataset.csv
3. ml_models.ipynb            # Train & evaluate ML classifiers
4. dl_models.ipynb            # Train & evaluate DL models
5. transformer_models.ipynb   # Fine-tune transformer models
```

> **Note:** Transformer fine-tuning requires a CUDA-enabled GPU. DistilBERT trains in ~1.5 hrs, BERT-base in ~10 hrs on a single GPU.

---

## Key Hyperparameters

**ML models** — TF-IDF with unigrams + bigrams (20,000 features, sublinear TF scaling), 80/20 stratified split.

**DL models** — Keras tokenizer (vocab: 20,000, max length: 128), 128-dim embeddings, binary cross-entropy, Adam optimizer, EarlyStopping (patience=3).

**Transformer models** — HuggingFace `transformers`, AdamW (lr=2e-5, weight decay=0.01), 4 epochs, linear warmup (10%), gradient clipping (max norm=1.0).

---

## Requirements

```
Python >= 3.8
pandas
numpy
scikit-learn
xgboost
tensorflow >= 2.10
torch >= 1.13
transformers >= 4.30
datasets
accelerate
matplotlib
seaborn
jupyter
```

---

## License

This project is released under the [MIT License](LICENSE).
