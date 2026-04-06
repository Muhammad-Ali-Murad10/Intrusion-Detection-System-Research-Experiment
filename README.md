# Machine Learning Intrusion Detection System (CIC-IDS2017)

This repository contains my research-oriented intrusion detection experiment on the CIC-IDS2017 dataset. The project investigates how well a two-stage machine learning pipeline can detect malicious traffic and classify attack families under both optimistic and realistic evaluation settings.

## Overview

The pipeline is structured in two levels:

- **Level 1:** Binary classification (`BENIGN` vs `ATTACK`)
- **Level 2:** Multi-class attack-family classification (`DoS`, `PortScan`, `BruteForce`, `WebAttack`, `Bot`)

The goal of this work was not only to obtain strong random-split performance, but also to test whether the system generalizes under more realistic deployment-style conditions such as time-like evaluation and leave-one-file-out testing.

## Dataset

The experiment uses **CIC-IDS2017**, loaded from 8 CSV files. In the notebook output, the dataset is shown with:

- **2,830,743 rows** initially loaded
- **80 columns** before later cleaning steps
- a `_source_file` column to preserve file-level origin
- a `Label` column for attack labels

After cleaning and preprocessing shown in the notebook:

- **308,381 duplicates** were removed
- **8 constant columns** were dropped
- the final feature matrix shape became **(2,522,362, 70)**

## Label structure

### Level 1 labels
- `BENIGN`
- `ATTACK`

### Level 2 families
- `DoS`
- `PortScan`
- `BruteForce`
- `WebAttack`
- `Bot`
- `TrueRare`

The notebook output shows the following family counts:

- `BENIGN`: 2,273,097
- `DoS`: 380,688
- `PortScan`: 158,930
- `BruteForce`: 13,835
- `WebAttack`: 2,180
- `Bot`: 1,966
- `TrueRare`: 47

## Preprocessing

The experiment includes the following preprocessing steps:

- duplicate removal
- constant-column removal
- replacement of infinite values
- median filling for missing values
- log transform for rate-based features such as:
  - `Flow Bytes/s`
  - `Flow Packets/s`
  - `Fwd Packets/s`
  - `Bwd Packets/s`
- robust scaling using `RobustScaler`

## Models

### Level 1
A LightGBM binary classifier is used for `BENIGN` vs `ATTACK`.

Additional steps:
- class weighting
- isotonic probability calibration
- threshold tuning based on target recall

### Level 2
A LightGBM multiclass classifier is used for attack-family classification.

### Additional analysis
The notebook also includes:
- `IsolationForest`
- `HDBSCAN`
- graph/network-style analysis components

## Evaluation settings

This repository includes multiple evaluation styles:

1. **Optimistic row-random split**
2. **Calibrated and threshold-tuned binary evaluation**
3. **Time-like evaluation**
4. **Leave-one-file-out style realism checks**

This is important because row-random evaluation produced nearly perfect metrics, but realistic cross-file evaluation showed clear generalization challenges.

## Main results

### Level 1: optimistic row-random result

Confusion matrix:

[[418490, 807],
 [40, 85136]]

Metrics:
- ROC-AUC: **0.9999661318781935**
- PR-AUC: **0.9998246003994076**

### Level 1: calibrated + tuned-threshold result

Chosen threshold:
- **0.9770408163265306**

Confusion matrix:

[[419158, 139],
 [2821, 82355]]

Metrics:
- ROC-AUC: **0.9999464335333705**
- PR-AUC: **0.9997473689712688**

### Level 2: optimistic row-random multiclass result

The multiclass classifier achieved near-perfect results on the random split. The confusion matrix showed almost complete separation across `Bot`, `BruteForce`, `DoS`, `PortScan`, and `WebAttack`, with only a few misclassifications.

### Realistic findings

The realistic evaluation revealed that performance was much less stable across files.

Examples from time-like results:
- some files had very high recall but extremely low precision
- one PortScan holdout case showed strong failure to generalize to an unseen file distribution

For the held-out file `Friday-WorkingHours-Afternoon-PortScan.pcap_ISCX.csv`, the true class was entirely `PortScan`, but predictions were mostly assigned to other families such as `Bot`, `BruteForce`, `DoS`, and `WebAttack`.

## Key conclusion

The main research finding of this work is that **row-random split performance is highly optimistic**, while file-based and time-like evaluation exposes important challenges related to:

- distribution shift
- class imbalance
- false positives
- poor cross-file generalization

This makes the project more realistic than a standard notebook that reports only high random-split accuracy.

## Repository contents

- `IDS_CODE 11.ipynb` — main experiment notebook
- `RESULTS.md` — structured result tables and interpretation
- `REPRODUCIBILITY.md` — reproducibility notes and current gaps
- `requirements.txt` — dependencies
- `results/` — exported result summaries and artefacts

## Reproducibility note

At the current stage, reproducibility is partial. The experiment documents the pipeline clearly, but some gaps remain:
- package versions are not fully pinned
- dataset download/preparation is not scripted end-to-end
- final summary artefacts should be exported and committed in a cleaner form

See `REPRODUCIBILITY.md` for details.
