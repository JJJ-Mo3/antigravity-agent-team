---
description: Refactor existing code for performance, readability, or technical debt
---

# Refactor Workflow

This workflow focuses on improving existing code without changing external behavior (mostly).

## Prerequisites

- [ ] Identified code area to refactor

## Process

### Phase 1: Analysis

1. **Run Architect**
   ```
   /run-architect
   ```
   - **User Input**: "Refactor [Component/Module] to improve [Goal]."
   - **Architect Action**:
     - Audits current implementation.
     - Proposes refactoring plan.
     - Identifies risks and regression test needs.

### Phase 2: Execution

2. **Run Engineering Manager**
   ```
   /run-em
   ```
   - **EM Action**:
     - Breaks refactor into safe, atomic steps.
     - Ensures tests are prioritized.

3. **Run Developer**
   ```
   /run-developer
   ```
   - **Dev Action**:
     - Write/Ensure tests cover existing behavior (Safety Net).
     - Perform refactoring.
     - Verify tests still pass.

## Quick Start

```
/run-architect
```
Then say: "I want to refactor [file/component] to..."
