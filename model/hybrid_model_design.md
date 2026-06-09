# Hybrid Detection Model — Design and Algorithm Selection

## Model Architecture

The proposed model runs in three sequential stages. Each stage handles what the previous one cannot.

```
[File Input]
     │
     ▼
┌─────────────────────────┐
│  STAGE 1                │
│  Signature-Based Scan   │  ──── MATCH ──→ [ALERT: Known Ransomware]
│  (Hash + Pattern DB)    │
└─────────────────────────┘
     │ NO MATCH
     ▼
┌─────────────────────────┐
│  STAGE 2                │
│  ML Behavioral Analysis │  ── MALICIOUS → [ALERT: Novel Variant]
│  Random Forest /        │
│  Gradient Boosting      │
└─────────────────────────┘
     │ CLEAN
     ▼
┌─────────────────────────┐
│  STAGE 3                │
│  Ensemble Decision      │  → Final Verdict: CLEAN or RANSOMWARE
└─────────────────────────┘
```

## Stage Design Rationale

| Stage | Method | Why This Order |
|---|---|---|
| Stage 1 | Signature-Based Scan | Cheapest detection — eliminates known threats before ML is invoked |
| Stage 2 | ML Behavioral Classification | Handles zero-day and polymorphic variants that signatures miss |
| Stage 3 | Ensemble Decision | Flag if either stage triggers — minimizes missed detections |

Ensemble rule: **flag if either stage triggers.** A missed ransomware detection is almost always more costly than a false positive — the design prioritises sensitivity by intent.

---

## Behavioral Features — Stage 2 Inputs

- File entropy measurement (high entropy = encryption in progress)
- File rename and extension change rate
- API call sequences (`CryptEncrypt`, `FindFirstFile`/`FindNextFile` loops)
- Registry modification patterns (persistence, shadow copy deletion)
- Network connection behaviour (C2 communication indicators)

---

## Algorithm Selection

Four algorithms were evaluated against the research literature. The selection criteria were accuracy, real-time inference speed, and false positive rate — not just benchmark performance.

| Algorithm | Detection Rate | Inference Speed | False Positive Rate | Selected |
|---|---|---|---|---|
| Random Forest | 95–99% | Fast | Low–Moderate | ✓ Primary |
| Gradient Boosting | 95–99% | Fast | Low | ✓ Alternative |
| SVM | 91–97% | Degrades at scale | Moderate | ✗ |
| Decision Tree (single) | 88–95% | Very fast | Moderate–High | ✗ |

**Random Forest** was selected as the primary Stage 2 classifier. It matches Gradient Boosting on accuracy while being slightly simpler to deploy and tune. Its feature importance output also helps SOC analysts understand which indicators drove a specific detection — useful for triage.

**Gradient Boosting** is the alternative when false positive rate is the higher priority — it consistently showed lower FP rates at comparable accuracy in the surveyed literature.

**SVM and single Decision Trees** were not selected. SVM's inference speed degrades at enterprise scale. A single Decision Tree overfits significantly — it is the base component both ensemble methods are built on, not a standalone classifier.

---

## MITRE ATT&CK Coverage

| Technique | ID | Stage That Covers It |
|---|---|---|
| Data Encrypted for Impact | T1486 | Stage 2 — entropy analysis, file operation rate |
| Obfuscated Files or Information | T1027 | Stage 1 + Stage 2 — pattern detection, static features |
| Encrypted C2 Channel | T1573 | Stage 2 — network behaviour features |
| Inhibit System Recovery | T1490 | Stage 2 — registry modification, shadow copy deletion |
