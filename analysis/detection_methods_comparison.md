# Detection Methods Comparison

Reviewing 20+ papers made one thing clear — each methodology has a well-defined ceiling.
The research community has optimized individual approaches extensively. What's missing is
honest accounting of where each one breaks down operationally.

---

## Signature-Based Detection

**How it works:** Compares file hashes and byte patterns against a database of known
ransomware signatures. Fast, deterministic, low compute cost.

**Where it holds up:**
- Near-perfect detection rate against known variants
- Minimal false positives — if it matches, it's almost certainly malicious
- Low latency — viable as a real-time first-pass filter

**Where it fails:**
- Useless against variants not in the database
- LockBit 3.0 specifically engineered mutation cycles to stay ahead of signature updates
- Polymorphic ransomware rewrites its own code on each execution — no stable signature to match

| Study | Key Finding |
|---|---|
| Moser et al. (2007) | Signature methods cannot reliably detect zero-day variants |
| Bayer et al. (2014) | Real-time signature database updates create detection gaps |

**Bottom line:** Fast and accurate for what it knows. The problem is the assumption
that what it knows stays current.

---

## Behavior-Based Detection

**How it works:** Monitors runtime activity — file system operations, entropy changes,
API call sequences — and flags behavior consistent with ransomware regardless of signature.

**Where it holds up:**
- Catches zero-day and polymorphic variants that signature methods miss
- Detection is based on what the process *does*, not what it *looks like*
- More resilient to evasion techniques

**Where it fails:**
- Legitimate processes trigger the same flags — backup software, compression tools,
  disk encryption all look like ransomware from a behavioral standpoint
- False positive rates are the consistent pain point across every behavior-based study reviewed
- Computational overhead makes pure behavioral monitoring difficult at scale

| Study | Approach | Limitation Identified |
|---|---|---|
| Zhao et al. (2018) | Runtime encryption pattern tracking | False positives from legitimate processes |
| Sharma et al. (2020) | Behavioral flags in real-time environments | Accuracy drops under latency constraints |
| Arabo et al. (2020) | Process-level behavior analysis | Computational overhead |
| Madanayaka et al. (2023) | Proactive behavioral alerting | Rule dependency limits flexibility |

**Bottom line:** The right instinct — detect by behavior, not signature. The execution
problem is noise. Legitimate activity and ransomware activity overlap more than most
papers acknowledge.

---

## Machine Learning-Based Detection

**How it works:** Trains models on labeled ransomware datasets to recognize patterns —
behavioral features, network traffic, file system telemetry — and classify new files
without relying on known signatures.

**Where it holds up:**
- Generalizes across variant families better than signature methods
- Random Forest and Gradient Boosting showed the best accuracy-to-compute tradeoff
  across reviewed studies
- Adaptable — retraining on new data improves coverage over time

**Where it fails:**
- False positive rates remain a problem, particularly when training data doesn't
  represent the full range of legitimate enterprise activity
- Deep learning models showed strong accuracy but required compute resources that
  rule out real-time deployment in most environments
- Dataset quality is the hidden dependency — models trained on limited variant families
  perform poorly when novel ransomware families emerge

| Study | Algorithm(s) | Key Result | Limitation |
|---|---|---|---|
| Yang et al. (2019) | SVM, Decision Trees | Good accuracy | High false positive rate |
| Xie et al. (2020) | Deep Learning / Neural Networks | Strong pattern detection | High compute cost |
| Smith et al. (2022) | Multiple ML frameworks | RF/GB most viable operationally | Framework-dependent variance |
| Ferdous et al. (2024) | AI comprehensive review | AI improves unknown threat handling | Dataset diversity gap |
| Ispahany et al. (2024) | ML review | Consistent latency issues identified | Real-time deployment constraints |

**Bottom line:** The most flexible of the three approaches. The operational ceiling is
dataset quality and compute cost — two problems that are solvable with the right
infrastructure, which is why cloud deployment changes the calculus here.

---

## Why None of Them Work Alone

The failure modes don't overlap — they compound:

- Signature-based misses everything new
- Behavior-based catches new threats but generates noise that overwhelms SOC teams
- ML-based generalizes well but struggles with real-time performance and requires
  data infrastructure most organizations don't have in place

The gap isn't a single weakness in one method. It's that each method's strength
is another method's blind spot. That's what the hybrid model is designed to address —
not by being better at any one thing, but by covering the failure modes the others leave open.

---

## Detection Gap Matrix

| Threat Type | Signature-Based | Behavior-Based | ML-Based | Hybrid |
|---|---|---|---|---|
| Known ransomware variants | ✓ | ✓ | ✓ | ✓ |
| Zero-day variants | ✗ | ✓ | ✓ | ✓ |
| Polymorphic ransomware | ✗ | Partial | ✓ | ✓ |
| Real-time detection | ✓ | Partial | Partial | ✓ |
| Low false positive rate | ✓ | ✗ | Partial | ✓ |
| Scalable to enterprise | ✓ | ✗ | Depends | ✓ |
