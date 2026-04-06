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

## 3. Level 1 — binary detection

### 3.1 Optimistic row-random split

Confusion matrix:

```text
[[418490, 807],
 [40, 85136]]
