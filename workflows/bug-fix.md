---
description: Fix a reported bug in the codebase
---

# Bug Fix Workflow

This workflow streamlines the process of reproducing, fixing, and verifying bugs.

## Prerequisites

- [ ] Bug report or reproduction steps

## Process

1. **Run Developer Directly**
   ```
   /run-developer
   ```
   - **User Input**: "Fix bug: [Description]. Steps to reproduce: [Steps]"
   - **Dev Action**:
     1. **Reproduce**: Create a reproduction test case that fails.
     2. **Analyze**: Locate the root cause in existing code.
     3. **Fix**: Implement the fix.
     4. **Verify**: Run the reproduction test to see it pass.

2. **Run QA**
   ```
   /run-qa
   ```
   - **QA Action**:
     - Verify the fix.
     - Run regression tests around affected area.

## Quick Start

```
/run-developer
```
Then say: "Fix bug in [feature]: [details]"
