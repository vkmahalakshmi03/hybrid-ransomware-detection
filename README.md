# Ransomware Detection Survey — Hybrid Detection Model

**Author:** Mahalakshmi Karthikeyan  
**Type:** Research Survey  
**Focus:** Ransomware Detection · Signature-Based · Behavioral · Machine Learning

## Overview

Ransomware attacks have become one of the most damaging cybersecurity threats, affecting healthcare, government, and businesses of all sizes. Traditional signature-based detection methods struggle to keep up with new and evolving ransomware variants, while machine learning approaches face challenges with false positives and real-time detection.

This research surveys 20 ransomware detection studies across three approaches — signature-based, behavioral, and machine learning — and proposes a hybrid model that combines the strengths of each to address the gaps that no single method can cover alone.

## How to Navigate This Repo

Start with this README for the full picture. Then go to `analysis/` to see how each detection method was broken down and what the key findings were. The `model/` folder has the proposed hybrid architecture and pipeline diagram. `research/` explains how the 20 papers were selected and reviewed. `references/` has all papers with annotations.

## Detection Approaches Compared

| Approach | How It Works | Strengths | Limitations |
|---|---|---|---|
| Signature-Based | Matches files against known malware signature database | Fast, accurate for known variants | Cannot detect new or polymorphic ransomware |
| Behavioral / Heuristic | Monitors runtime activity — file changes, encryption patterns | Can detect unknown variants | High false positive rate, computational overhead |
| Machine Learning | Classifies files based on learned patterns | Flexible, adapts to new threats | Requires labeled data, real-time detection challenges |

## Proposed Hybrid Detection Model

The model works in three stages:

```
[File Input]
      │
      ▼
┌──────────────────────────┐
│  STAGE 1                 │
│  Signature-Based Scan    │ ──── MATCH ──→  Known Ransomware Detected
└──────────────────────────┘
      │ NO MATCH
      ▼
┌──────────────────────────┐
│  STAGE 2                 │
│  ML Behavioral Analysis  │ ── MALICIOUS →  Novel Variant Detected
│  Random Forest /         │
│  Gradient Boosting       │
└──────────────────────────┘
      │ CLEAN
      ▼
┌──────────────────────────┐
│  STAGE 3                 │
│  Ensemble Decision       │ → Final Verdict: CLEAN or RANSOMWARE
└──────────────────────────┘
```

Stage 1 quickly filters known threats before the ML layer runs. Stage 2 handles variants that signatures miss. Stage 3 flags a file if either stage triggers, reducing false negatives.

## Key Findings

- The hybrid model outperforms standalone detection methods with higher detection rates and fewer false positives
- Signature-based detection works as an effective first filter for known variants but fails entirely against new strains
- Machine learning using Random Forest and Gradient Boosting improves flexibility for detecting unknown and evolving ransomware
- The ensemble decision approach reduces false negatives by flagging a file if either stage detects a threat
- Real-time detection capabilities are maintained without significant additional computational overhead

## Repository Contents

| Folder | Contents |
|---|---|
| `docs/` | Full research paper |
| `analysis/` | Comparison of detection methods and key findings from the survey |
| `research/` | Survey methodology — how papers were selected and analyzed |
| `model/` | Hybrid model architecture and pipeline diagram |
| `references/` | All 20 surveyed papers with annotations |

## What I Found Interesting

Most detection research optimizes for one metric — usually accuracy — without accounting for the operational environment it would run in. A model with 98% accuracy that adds 800ms latency per file operation is unusable at enterprise endpoint scale. The hybrid approach isn't just technically stronger — it's the only design that holds up when you think about it as something that has to run in production, integrated with real tooling, triaged by real analysts under real alert volume.

**Signature scanning belongs at Stage 1 as a pre-filter, not as the primary defense.** It's the cheapest detection layer. Running it first means the ML classifier only processes what signatures couldn't resolve — a much smaller and more focused workload. This is what makes real-time hybrid detection feasible at enterprise scale.

**Random Forest and Gradient Boosting outperform deep learning for real-time endpoint detection.** They matched deep learning accuracy on most evaluated datasets while requiring significantly less compute. Deep learning achieved strong numbers on large datasets in research conditions but introduced latency that makes it impractical for real-time file-level classification on managed endpoints.

**The ensemble decision rule is the most consequential design choice.** Flagging if either stage triggers — rather than requiring both — accepts a small false positive increase to eliminate the blind spot that novel variants would otherwise create. For ransomware, a missed detection leads to file encryption and operational disruption. A false positive gets investigated and cleared. The asymmetry in cost makes the tradeoff clear.

**The research literature consistently undervalues operational constraints.** Accuracy was the primary metric across nearly all surveyed papers. False positive burden on SOC analysts, inference latency at endpoint scale, and integration with existing SIEM and EDR tooling were rarely quantified and often omitted entirely. A detection system that works in a research evaluation but generates 200 false positives per day in production will not be used effectively — alert fatigue is a documented failure mode that the detection research community has not adequately addressed.


