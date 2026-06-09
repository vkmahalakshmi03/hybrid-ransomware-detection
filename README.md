# Hybrid Ransomware Detection: A Survey of Current Technologies

**Author:** Mahalakshmi Karthikeyan  
**Focus Area:** Security Operations · Threat Detection · Endpoint Security

---

## How to Read This Repo

Start here with the README for the full picture. Then go to `model/hybrid_model_design.md` to see the proposed detection architecture. From there, `analysis/detection_methods_comparison.md` explains what each detection approach does and where it breaks down. Everything else — the methodology, algorithm analysis, and references — supports those two core documents.

---

## Overview

Ransomware has moved from opportunistic attacks to coordinated, targeted campaigns.
LockBit 3.0 disrupted global logistics and healthcare networks throughout 2023.
BlackCat/ALPHV brought down Change Healthcare in 2024, impacting 100+ million patient
records. WannaCry and the Colonial Pipeline attack demonstrated how a single intrusion
can cascade across critical infrastructure.

What these incidents share — the detection systems in place failed to catch novel
variants early enough. This research examines why, and proposes a hybrid detection
model that addresses the core gap.

I reviewed 20+ ransomware detection studies across three methodologies —
signature-based, behavioral, and machine learning — and found a consistent pattern:
no single approach handles both known variants and zero-day threats without trading
off real-time performance or false positive rates.

---

## Research Problem

The three failure points that existing detection systems don't solve together:

- **Signature-based methods** fail against new and polymorphic variants — LockBit
  alone has released multiple versions specifically designed to evade signature databases
- **Behavior-based methods** generate excessive false positives when legitimate
  processes mirror ransomware activity (mass file operations, encryption tasks)
- **ML models** struggle with real-time detection and require large labeled datasets
  that rarely reflect the latest ransomware families

---

## Proposed Hybrid Detection Model

The model operates in three stages:

| Stage | Method | Purpose |
|---|---|---|
| Stage 1 | Signature-Based Scan | Rapid detection of known ransomware variants |
| Stage 2 | ML Behavioral Analysis | Detection of novel/zero-day variants via pattern recognition |
| Stage 3 | Ensemble Decision | Combined output to minimize false negatives |

**ML Algorithms analyzed:** Random Forest, Gradient Boosting, SVM, Decision Trees  
**Behavioral Indicators monitored:** Mass file encryption, unauthorized file access, abnormal I/O patterns

---

## Detection Methodology Comparison

| Approach | Strengths | Limitations |
|---|---|---|
| Signature-Based | Fast, accurate for known threats | Ineffective against new/polymorphic variants |
| Heuristic / Behavior-Based | Detects unknown behavior | High false positives, computational overhead |
| Machine Learning | Adaptive, pattern-based | Requires labeled data, real-time latency |
| **Hybrid (Proposed)** | **Comprehensive coverage, lower FP rate** | **Complexity of integration** |

---

## Key Findings

Across 20+ surveyed papers, five patterns held consistently regardless of which specific studies or datasets were analyzed:

**No single detection method closes all three gaps.** Every paper demonstrated the same ceiling — high accuracy on what it was designed for, structural failure on what it wasn't. Signature methods hit 99% on known threats and 0% on novel variants. Behavioral methods caught unknown activity but generated false positive rates that weren't operationally sustainable. ML methods generalised well but introduced latency and dataset currency problems. That consistent three-way failure across the entire literature is what drives the hybrid design.

**Signature scanning belongs at Stage 1 as a pre-filter, not as the primary defense.** It's the cheapest detection layer. Running it first means the ML classifier only processes what signatures couldn't resolve — a much smaller and more focused workload. This is what makes real-time hybrid detection feasible at enterprise scale.

**Random Forest and Gradient Boosting outperform deep learning for real-time endpoint detection.** They matched deep learning accuracy on most evaluated datasets while requiring significantly less compute. Deep learning achieved strong numbers on large datasets in research conditions but introduced latency that makes it impractical for real-time file-level classification on managed endpoints.

**The ensemble decision rule is the most consequential design choice.** Flagging if either stage triggers — rather than requiring both — accepts a small false positive increase to eliminate the blind spot that novel variants would otherwise create. For ransomware, a missed detection leads to file encryption and operational disruption. A false positive gets investigated and cleared. The asymmetry in cost makes the tradeoff clear.

**The research literature consistently undervalues operational constraints.** Accuracy was the primary metric across nearly all surveyed papers. False positive burden on SOC analysts, inference latency at endpoint scale, and integration with existing SIEM and EDR tooling were rarely quantified and often omitted entirely. A detection system that works in a research evaluation but generates 200 false positives per day in production will not be used effectively — alert fatigue is a documented failure mode that the detection research community has not adequately addressed.

---

## MITRE ATT&CK Alignment

| Tactic | Technique | ID |
|---|---|---|
| Impact | Data Encrypted for Impact | T1486 |
| Defense Evasion | Obfuscated Files or Information | T1027 |
| Execution | User Execution: Malicious File | T1204.002 |
| Command & Control | Encrypted Channel | T1573 |

---

## Repository Contents

| Folder | Contents |
|---|---|
| `docs/` | Full research paper |
| `analysis/` | Detection method comparison and key findings across 20+ studies |
| `research/` | Survey methodology |
| `model/` | Hybrid model architecture, algorithm selection rationale, and detection logic pseudocode |
| `references/` | Annotated bibliography of surveyed papers |

---

## Relevance to Security Operations

The detection logic in this model maps directly to SOC workflows — signature matching
aligns with IOC-based alerting in SIEM platforms like Splunk and Sentinel, while the
behavioral layer reflects the kind of anomaly detection rules written for endpoint
telemetry. The ensemble decision structure mirrors how tiered alert triage works in
practice.

---

## What I Found Interesting

The deeper I got into the literature, the clearer it became that most detection research
optimizes for one metric — usually accuracy — without accounting for the operational
environment it would run in. A model with 98% accuracy that adds 800ms latency per file
is unusable in an enterprise endpoint context. The hybrid approach isn't just technically
stronger — it's the only design that holds up when you think about it as something that
actually has to run at scale.
