# Security Threat Model

> [!NOTE]
> As a client-side POC with no backend, many traditional security concerns (SQLi, Auth bypass, etc.) do not apply.

## 1. Threat: Data Loss
*   **Risk:** User clears browser cache or LocalStorage.
*   **Mitigation:** This is an inherent limitation of a LocalStorage-only POC. We will warn the user that data is local-only.

## 2. Threat: Cross-Site Scripting (XSS)
*   **Risk:** Malicious script injected into Card Title or Description.
*   **Mitigation:**
    *   React automatically escapes content in JSX.
    *   We will strictly **avoid** setting `dangerouslySetInnerHTML`.

## 3. Threat: Dependency Vulnerabilities
*   **Risk:** Vulnerable npm packages.
*   **Mitigation:**
    *   Standard `npm audit` checks.
    *   Minimal dependency tree (Vite, React, Zustand, @hello-pangea/dnd).
