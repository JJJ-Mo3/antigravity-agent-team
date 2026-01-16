---
description: Start a new SaaS project (Multi-tenant, Subscriptions)
---

# Start SaaS Project

This workflow initializes the AI Team with **SaaS specialization**.

**Context Loaded:** `agents/specializations/saas/`

## Steps

1.  **Set Project Type**
    Agents will use `PROJECT_TYPE="saas"`.

2.  **Run Product Manager**
    ```bash
    /run-pm
    ```
    *Focus: Multi-tenancy, Subscription tiers (Stripe/LemonSqueezy), Team management.*

3.  **Continue with Standard Workflow**
    Follow `/start-project`. Agents will enforce RLS for tenancy and designing for scale.
