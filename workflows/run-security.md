---
description: Run the Security Engineer agent for audits and testing
---

# Run Security Engineer

The Security Engineer operates in three modes: **Audit**, **Review**, and **PenTest**.

## Usage
```bash
/run-security
```

## Modes

1.  **Architecture Audit** (Phase 2)
    *   **Input**: `ARCHITECTURE.md`, `SCHEMA.md`
    *   **Action**: Threat modeling, Design review
    *   **Output**: `THREAT_MODEL.md`

2.  **Code Review** (Phase 4)
    *   **Input**: Pull Requests, Source Code
    *   **Action**: Static analysis, Manual logic review
    *   **Output**: GitHub Review Comments

3.  **Penetration Test** (Phase 5)
    *   **Input**: Full application
    *   **Action**: Simulated attacks (SQli, XSS)
    *   **Output**: `SECURITY_AUDIT.md`, `VULNERABILITY_REPORT.md`
