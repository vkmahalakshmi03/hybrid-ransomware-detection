# Detection Pipeline — Architecture Diagram

```mermaid
flowchart TD
    A([File Input]) --> B

    B[Stage 1\nSignature-Based Scan\nHash + Pattern DB]
    B -->|MATCH| C([ALERT: Known Ransomware\nQuarantine + Escalate])
    B -->|NO MATCH| D

    D[Stage 2\nML Behavioral Analysis\nRandom Forest / Gradient Boosting]
    D -->|MALICIOUS| E([ALERT: Novel Variant\nQuarantine + Escalate])
    D -->|CLEAN| F

    F[Stage 3\nEnsemble Decision\nCombined Verdict]
    F -->|Either stage triggered| G([RANSOMWARE\nQuarantine + Alert])
    F -->|Both stages clean| H([CLEAN\nNo Action])

    style A fill:#e8f4f8,stroke:#2c7bb6
    style C fill:#fde8e8,stroke:#d73027
    style E fill:#fde8e8,stroke:#d73027
    style G fill:#fde8e8,stroke:#d73027
    style H fill:#e8f5e9,stroke:#1a9641
```

## Stage Summary

| Stage | Method | Triggers On |
|---|---|---|
| Stage 1 | Signature Scan | Known ransomware hash or pattern match |
| Stage 2 | ML Behavioral Analysis | Novel or zero-day variants via feature classification |
| Stage 3 | Ensemble Decision | Either stage flagging — minimises missed detections |

## Behavioral Features Fed into Stage 2

- File entropy — high entropy signals active encryption
- File rename and extension change rate
- API call sequences (`CryptEncrypt`, `FindFirstFile` loops)
- Registry modifications and shadow copy deletion
- Network communication patterns (C2 indicators)
