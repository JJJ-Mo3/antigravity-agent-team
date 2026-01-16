---
description: Request a new feature implementation on an existing codebase
---

# Feature Request Workflow

This workflow guides the team through adding a new feature to an existing application, ensuring it integrates with current architecture and patterns.

## Prerequisites

- [ ] Existing codebase
- [ ] Feature idea or brief

## Process

### Phase 1: Specification (Update)

1. **Run Product Manager**
   ```
   /run-pm
   ```
   - **User Input**: "I want to add [Feature X] to the existing app."
   - **PM Action**:
     - Reads current PRD.
     - Creates a new feature specification or updates PRD.
     - Ensures new feature doesn't conflict with existing requirements.

### Phase 2: Design Integration

2. **Run Architect** (Optional for small features)
   ```
   /run-architect
   ```
   - **Architect Action**:
     - Reviews `docs/architecture/ARCHITECTURE.md`.
     - Updates architecture to accommodate new feature.
     - Designates integration points with existing system.

3. **Run DBA** (If data changes needed)
   ```
   /run-dba
   ```
   - **DBA Action**:
     - Creates *new* migration file (does not rewrite schema.md entirely).
     - Ensures no data loss for existing data.

### Phase 3: Implementation

4. **Run Engineering Manager**
   ```
   /run-em
   ```
   - **EM Action**:
     - Identifies affected components.
     - Creates implementation slices specific to this feature.

5. **Run Developers**
   ```
   /run-developer
   ```
   - **Dev Action**:
     - Reads existing code first.
     - Implements feature matching existing style.

## Quick Start

```
/run-pm
```
Then say: "I want to add a new feature: [Description]"
