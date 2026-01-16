---
description: Start a new project with the full 12-Agent AI development team
---

# Start New Project (v2.0)

This workflow guides you through the complete software development process using the full roster of 12 specialized AI agents.

## Prerequisites

- [ ] Product idea or concept ready
- [ ] Supabase account (for backend)
- [ ] Netlify account (for frontend)
- [ ] GitHub repository created

## Project Phases

### Phase 1: Planning & Requirements

1. **Run Product Manager**
   ```bash
   /run-pm
   ```
   - Interactive gathering of requirements.
   - Output: `docs/product/PRD.md`

2. **Run Data Analyst**
   ```bash
   /run-data-analyst
   ```
   - Defines North Star Metric and Event Schema.
   - Output: `docs/analytics/ANALYTICS_PLAN.md`

### Phase 2: Architecture & Design

3. **Run Technical Architect** (Parallel)
   ```bash
   /run-architect
   ```
   - Output: `docs/architecture/ARCHITECTURE.md`

4. **Run Database Administrator** (Parallel)
   ```bash
   /run-dba
   ```
   - Output: `docs/database/SCHEMA.md`, `docs/database/RLS_POLICIES.sql`

5. **Run Security Engineer (Audit #1)**
   ```bash
   /run-security
   ```
   - Validates architecture and schema security.
   - Output: `docs/security/THREAT_MODEL.md`

6. **Run UX/UI Designer**
   ```bash
   /run-designer
   ```
   - Output: `docs/design/DESIGN.md`

7. **Run Infrastructure Engineer**
   ```bash
   /run-infrastructure
   ```
   - Output: `docs/infrastructure/SETUP.md`

### Phase 3: Implementation Planning

8. **Run Engineering Manager**
   ```bash
   /run-em
   ```
   - Breaks work into atomic slices.
   - Output: `docs/implementation/PLAN.md` and GitHub Issues.

### Phase 4: Development (Iterative)

9. **Implementation Cycle** (Repeat for each slice)
   - **Developer**: `/run-developer` (Writes code & tests)
   - **QA**: `/run-qa` (Verifies code)
   - **Security**: `/run-security` (Checks Critical/High risks)

10. **Run Technical Writer** (Parallel)
    ```bash
    /run-tech-writer
    ```
    - Creates User Guides and API Docs.

### Phase 5: Pre-Deployment & Launch

11. **Run Security Engineer (Final Audit)**
    ```bash
    /run-security
    ```
    - Full penetration test simulation and compliance check.

12. **Run DevOps Engineer**
    ```bash
    /run-devops
    ```
    - Sets up CI/CD, Load Testing, and deploys to Production.

## Quick Start

To begin, run:
```bash
/run-pm
```
