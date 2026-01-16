---
description: Start a new Shopify App project
---

# Start New Shopify Project

This workflow guides you through building a Shopify App.

## Prerequisites

- [ ] Shopify Partner Account
- [ ] Development Store

## Phases

### Phase 1: Definition

1. **Run Shopify Product Manager**
   ```
   /run-shopify-pm
   ```
   - specialized PRD for Shopify ecosystem

### Phase 2: Design & Tech

2. **Run Architect** (Standard)
   ```
   /run-architect
   ```
   - *Note: Ensure Architect is aware of Shopify APIs via PRD*

3. **Run DBA** (Standard)
   ```
   /run-dba
   ```

4. **Run Designer** (Standard)
   ```
   /run-designer
   ```
   - Should use Polaris Design System (specify in PRD)

### Phase 3: Implementation

5. **Run Engineering Manager**
   ```
   /run-em
   ```

## Quick Start

To begin:
```
/run-shopify-pm
```
