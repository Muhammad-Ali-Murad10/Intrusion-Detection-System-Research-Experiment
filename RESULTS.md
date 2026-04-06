# Results Summary

## 1. Dataset summary

| Measure | Value |
|---|---:|
| Files | 8 |
| Initially loaded shape | 2,830,743 rows |
| Initial columns | 80 |
| Rows shown after `df.info()` stage | 2,522,362 |
| Columns after later stage | 74 |
| Final feature matrix shape | (2,522,362, 70) |
| Duplicates removed | 308,381 |
| Constant columns dropped | 8 |

---

## 2. Label distribution

### Level 1

| Class | Count |
|---|---:|
| BENIGN | 2,273,097 |
| ATTACK | 557,646 |

### Level 2

| Family | Count |
|---|---:|
| BENIGN | 2,273,097 |
| DoS | 380,688 |
| PortScan | 158,930 |
| BruteForce | 13,835 |
| WebAttack | 2,180 |
| Bot | 1,966 |
| TrueRare | 47 |

---

## 3. Level 1 — binary detection

### 3.1 Optimistic row-random split

**Confusion matrix:**
```text
[[418490, 807],
 [40, 85136]]
```

**Metrics:**

| Metric | Value |
|---|---:|
| ROC-AUC | 0.9999661318781935 |
| PR-AUC | 0.9998246003994076 |

**Classification report:**

| Class | Precision | Recall | F1 | Support |
|---|---|---|---|---:|
| ATTACK | 0.99 | 1.00 | 1.00 | 85,176 |
| BENIGN | 1.00 | 1.00 | 1.00 | 419,297 |

---

### 3.2 Calibrated + tuned-threshold evaluation

**Chosen threshold:**
```
0.9770408163265306
```

**Confusion matrix:**
```text
[[419158, 139],
 [2821, 82355]]
```

**Metrics:**

| Metric | Value |
|---|---:|
| ROC-AUC | 0.9999464335333705 |
| PR-AUC | 0.9997473689712688 |

**Classification report:**

| Class | Precision | Recall | F1 | Support |
|---|---|---|---|---:|
| ATTACK | 1.00 | 0.97 | 0.98 | 85,176 |
| BENIGN | 0.99 | 1.00 | 1.00 | 419,297 |

---

## 4. Level 2 — multiclass family classification

### 4.1 Optimistic row-random split

**Confusion matrix:**

| True \ Pred | Bot | BruteForce | DoS | PortScan | WebAttack |
|---|---:|---:|---:|---:|---:|
| Bot | 391 | 0 | 0 | 0 | 0 |
| BruteForce | 0 | 1830 | 0 | 0 | 0 |
| DoS | 0 | 0 | 64323 | 12 | 18 |
| PortScan | 0 | 0 | 6 | 18152 | 6 |
| WebAttack | 0 | 0 | 3 | 0 | 426 |

**Classification report:**

| Class | Precision | Recall | F1 | Support |
|---|---|---|---|---:|
| Bot | 1.00 | 1.00 | 1.00 | 391 |
| BruteForce | 1.00 | 1.00 | 1.00 | 1,830 |
| DoS | 1.00 | 1.00 | 1.00 | 64,353 |
| PortScan | 1.00 | 1.00 | 1.00 | 18,164 |
| WebAttack | 0.95 | 0.99 | 0.97 | 429 |

---

## 5. Realistic time-like evaluation

| Test file | True attack rate | Predicted attack rate | AUC | Precision (attack) | Recall (attack) | F1 (attack) |
|---|---:|---:|---:|---:|---:|---:|
| Thursday-WorkingHours-Morning-WebAttacks | 0.013677 | 0.626719 | 0.960799 | 0.021823 | 1.000000 | 0.042713 |
| Thursday-WorkingHours-Afternoon-Infilteration | 0.000146 | 1.000000 | 0.473036 | 0.000146 | 1.000000 | 0.000293 |
| Friday-WorkingHours-Morning | 0.010838 | 0.985421 | 0.548692 | 0.010976 | 0.997952 | 0.021714 |
| Friday-WorkingHours-Afternoon-DDos | 0.573775 | 1.000000 | 0.975006 | 0.573775 | 1.000000 | 0.729170 |
| Friday-WorkingHours-Afternoon-PortScan | 0.427205 | 0.089976 | 0.612245 | 0.853513 | 0.179764 | 0.296979 |

---

## 6. Worst-case Level 2 generalization example

**Held-out file:**
```
Friday-WorkingHours-Afternoon-PortScan.pcap_ISCX.csv
```

**True labels:**
- PortScan: 90,819

**Predicted labels:**
- Bot: 87,556  
- BruteForce: 2,700  
- DoS: 442  
- WebAttack: 121  

---

## 7. Interpretation

The experiment shows that:

- Binary detection is extremely strong on row-random evaluation  
- Calibrated thresholding reduces false positives but also lowers attack recall slightly  
- Realistic file-based evaluation is much harder than row-random evaluation  
- Multiclass family classification looks nearly perfect on random splits but can fail badly on unseen file distributions  

**Conclusion:**

Optimistic random splits significantly overestimate real deployment performance.
