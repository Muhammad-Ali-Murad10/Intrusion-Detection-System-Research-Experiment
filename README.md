# Intrusion Detection System Research Experiment

## Overview

This repository contains a **research-oriented experimentation notebook** exploring **Intrusion Detection Systems (IDS)** using machine learning and anomaly detection techniques.

The project was developed as a **semester research project during my Bachelor's degree** after being introduced to the topic of Intrusion Detection Systems in class.

While exploring public cybersecurity datasets, I discovered that network traffic datasets such as **CIC-IDS2017** are **highly imbalanced and structurally complex**. This motivated me to investigate how different machine learning approaches behave under realistic deployment conditions.

The notebook implements a **multi-stage IDS pipeline** inspired by **two-stage anomaly detection concepts used in CERN’s Large Hadron Collider (LHC)** research, where detection is performed in stages:

- **Stage 1:** Fast anomaly detection  
- **Stage 2:** Detailed classification and investigation  

This idea was adapted to the **Intrusion Detection System domain**.

---

# Dataset

### Dataset Used

**CIC-IDS2017**

The dataset contains **network flow features extracted from simulated enterprise network traffic**, including multiple attack scenarios.

### Dataset Characteristics Observed During Analysis

- **Total rows (raw):** ~2.8M  
- **After cleaning:** ~2.5M  
- **Features:** ~70  
- **Capture files:** 8  

Each file represents a **different time period and attack scenario**.

### Important Observation During Experimentation

Most files contain **only a single attack family**, which strongly affects **multi-class classification evaluation**.

### Example Distribution

| File | Dominant Attack |
|-----|----------------|
| Monday | BENIGN |
| Tuesday | BruteForce |
| Wednesday | DoS |
| Thursday Morning | WebAttack |
| Friday Morning | Bot |
| Friday Afternoon | DDoS |
| Friday Afternoon | PortScan |

This structural characteristic introduces challenges for **cross-file generalization**.

---

# Research Pipeline

The IDS pipeline follows a **multi-stage architecture**:

```
Sense → Detect → Investigate → Decide → Learn
```

---

## 1. Sense

Network flow data is collected and cleaned.

Processing includes:

- Duplicate removal
- Constant column removal
- Feature scaling using **RobustScaler**

---

## 2. Detect

Two-stage supervised detection is implemented.

### Level 1 (L1)

Binary classification:

```
BENIGN vs ATTACK
```

**Model Used**

- LightGBM

**Goal**

Detect whether network traffic is **malicious**.

---

### Level 2 (L2)

Multi-class classification:

- DoS  
- PortScan  
- BruteForce  
- WebAttack  
- Bot  

**Goal**

Identify the **attack family**.

---

## 3. Investigate

Unsupervised techniques are used to analyze attack behavior.

Methods include:

- **Isolation Forest** for anomaly detection
- **HDBSCAN clustering** for grouping similar attack patterns
- **Graph-based correlation** between clusters and network entities (ports)

This stage helps analysts **understand how attacks behave within the network**.

---

## 4. Decide

Simple **rule-based playbooks** are used to map detections to response actions.

| Attack | Response |
|------|---------|
| DoS | Rate limit or block |
| PortScan | Temporary block |
| BruteForce | Alert and investigate |
| Bot | Escalate |

---

## 5. Learn

The system monitors **data drift** using:

- **Population Stability Index (PSI)**
- **Kolmogorov-Smirnov test (KS)**

These metrics indicate when **model retraining may be required**.

---

# Evaluation Strategy

To better understand **model reliability**, multiple evaluation strategies were tested.

---

## 1. Random Split (Optimistic)

Standard machine learning approach:

- **80% train**
- **20% test**

### Result

Very high performance due to **overlapping traffic patterns**.

Example:

```
L1 ROC-AUC ≈ 0.999
```

However, this evaluation is **overly optimistic**.

---

## 2. Time-like Evaluation

Data is split **chronologically**:

- **Train → earlier capture files**
- **Test → later capture files**

This simulates **real IDS deployment conditions**.

Results show that **performance varies significantly depending on the attack distribution**.

---

## 3. Leave-One-File-Out (LOO)

Each capture file is used as a **test set** while training on all others.

This reveals how models **generalize to unseen network conditions**.

---

# Key Findings

Several interesting observations emerged from the experiments.

---

### 1. Random splits can produce misleadingly high results

Row-random evaluation produced **near-perfect scores**, but more realistic evaluations showed **significantly lower performance**.

---

### 2. Dataset structure strongly affects multi-class classification

Because many capture files contain **only one attack type**, holding out a file removes that class from training.

As a result:

The **Level 2 classifier often fails** when evaluated on a file containing an **unseen attack family**.

---

### 3. Binary detection generalizes better than multi-class classification

The **Level 1 detector (Attack vs Benign)** performed more consistently across files compared to **Level 2 attack-family classification**.

---

### 4. Drift analysis indicates traffic distributions change across capture files

**PSI and KS statistics** show significant **distribution differences** between training and test periods.

This highlights the importance of **continuous model monitoring and retraining** in real IDS deployments.

---

# Implementation Environment

The experiments were conducted using:

- **Python**
- **Google Colab**
- **Scikit-learn**
- **LightGBM**
- **HDBSCAN**
- **NetworkX**

---

# Limitations

Several limitations were observed:

- The dataset structure limits **realistic multi-class evaluation**
- Some attack types appear **only in specific capture files**
- Real-world networks would contain a **more continuous mix of attack behaviors**

### Potential Future Work

- Explore **additional cybersecurity datasets**
- Apply **deep learning models**
- Develop **real-time streaming IDS pipelines**

---

# How to Run

Install dependencies:

```bash
pip install -r requirements.txt
```

Open the notebook:

```
IDS_CODE.ipynb
```

Run all cells sequentially.
