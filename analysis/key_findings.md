# Key Findings

Findings that held consistently across all 20+ surveyed papers — patterns that appeared regardless of which specific studies or datasets were analyzed.

---

## Finding 1 — No Single Method Closes All Three Gaps

Every paper demonstrated the same ceiling: high accuracy on what it was designed for, structural failure on what it wasn't.

- Signature methods hit 99% on known threats and 0% on novel variants
- Behavioral methods caught unknown activity but generated false positive rates that weren't operationally sustainable
- ML methods generalised well but introduced latency and dataset currency problems

This consistent three-way failure across the entire literature is the direct justification for the hybrid model.

---

## Finding 2 — Signature Scanning Is the Right First Stage

Despite its zero-day gap, signature scanning remains the cheapest and fastest detection layer. In a hybrid pipeline it belongs at Stage 1 — not because it's sufficient, but because it eliminates known threats before the more compute-intensive ML stage runs.

Running ML inference on every single file operation is expensive. Filtering known threats at Stage 1 means the ML layer only processes what signatures couldn't resolve — a smaller, more focused workload with better performance characteristics.

---

## Finding 3 — Random Forest and Gradient Boosting Outperform Deep Learning for Real-Time Use

Across the ML detection literature, Random Forest and Gradient Boosting were the most consistently strong performers. They matched or exceeded deep learning accuracy while requiring significantly less compute — which matters when detection has to run at endpoint speed.

Deep learning approaches achieved strong accuracy on large datasets but introduced latency and resource overhead that makes them difficult to deploy in real-time endpoint contexts.

---

## Finding 4 — The Ensemble Decision Rule Is the Most Consequential Design Choice

Flagging if either stage triggers — rather than requiring both — accepts a small false positive increase to eliminate the blind spot that novel variants would otherwise create.

For ransomware, a missed detection leads to file encryption and operational disruption. A false positive gets investigated and cleared. The asymmetry in cost makes the tradeoff clear.

---

## Finding 5 — The Research Literature Undervalues Operational Constraints

Accuracy was the primary metric across nearly all surveyed papers. False positive burden on SOC analysts, inference latency at endpoint scale, and integration with existing SIEM and EDR tooling were rarely quantified.

A detection system that works in a research evaluation but generates 200 false positives per day in production will not be used effectively. Alert fatigue is a documented failure mode that the detection research community has not adequately addressed.
