# SaaS Product - Database Administrator Context

## Database Design for Multi-Tenant SaaS

You are designing a PostgreSQL database schema for a multi-tenant SaaS application on Supabase. Every design decision must prioritize data isolation, security, and performance.

---

## Core Schema Pattern

### Multi-Tenancy Foundation

```sql
-- ============================================
-- ORGANIZATIONS (Tenant Root)
-- ============================================
CREATE TABLE organizations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  plan TEXT NOT NULL DEFAULT 'free' CHECK (plan IN ('free', 'pro', 'enterprise')),
  
  -- Settings
  settings JSONB DEFAULT '{}'::jsonb,
  
  -- Subscription
  trial_ends_at TIMESTAMPTZ,
  subscription_status TEXT DEFAULT 'trialing',
  
  -- Metadata
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  
  CONSTRAINT valid_slug CHECK (slug ~ '^[a-z0-9-]+$')
);

-- Indexes
CREATE INDEX idx_organizations_slug ON organizations(slug);
CREATE INDEX idx_organizations_plan ON organizations(plan);
CREATE INDEX idx_organizations_created ON organizations(created_at DESC);

-- Update trigger
CREATE TRIGGER update_organizations_updated_at
  BEFORE UPDATE ON organizations
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

-- ============================================
-- USERS (Auth Users)
-- ============================================
CREATE TABLE users (
  id UUID PRIMARY KEY REFERENCES auth.users ON DELETE CASCADE,
  email TEXT NOT NULL UNIQUE,
  name TEXT,
  avatar_url TEXT,
  
  -- Preferences
  preferences JSONB DEFAULT '{}'::jsonb,
  
  -- Metadata
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_users_email ON users(email);

-- ============================================
-- ORGANIZATION MEMBERS (Many-to-Many)
-- ============================================
CREATE TABLE organization_members (
  organization_id UUID NOT NULL REFERENCES organizations ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES users ON DELETE CASCADE,
  
  -- Role & Permissions
  role TEXT NOT NULL DEFAULT 'member' 
    CHECK (role IN ('owner', 'admin', 'member', 'guest')),
  
  -- Invitation tracking
  invited_by UUID REFERENCES users,
  invitation_token TEXT UNIQUE,
  invitation_accepted_at TIMESTAMPTZ,
  
  -- Metadata
  joined_at TIMESTAMPTZ DEFAULT NOW(),
  
  PRIMARY KEY (organization_id, user_id)
);

-- Indexes
CREATE INDEX idx_org_members_user ON organization_members(user_id);
CREATE INDEX idx_org_members_org ON organization_members(organization_id);
CREATE INDEX idx_org_members_role ON organization_members(organization_id, role);
CREATE INDEX idx_org_members_token ON organization_members(invitation_token) 
  WHERE invitation_token IS NOT NULL;

-- Constraint: One owner per organization
CREATE UNIQUE INDEX idx_one_owner_per_org 
  ON organization_members(organization_id) 
  WHERE role = 'owner';
```

---

## Resource Tables Pattern

### Standard Resource Table Template

```sql
-- Every resource table follows this pattern
CREATE TABLE {resource_name} (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  
  -- CRITICAL: Organization relationship
  organization_id UUID NOT NULL REFERENCES organizations ON DELETE CASCADE,
  
  -- Resource-specific fields
  name TEXT NOT NULL,
  description TEXT,
  status TEXT,
  
  -- Common tracking
  created_by UUID REFERENCES users,
  updated_by UUID REFERENCES users,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  deleted_at TIMESTAMPTZ  -- Soft delete
);

-- CRITICAL: Always index organization_id
CREATE INDEX idx_{resource}_org_id ON {resource_name}(organization_id);
CREATE INDEX idx_{resource}_org_created 
  ON {resource_name}(organization_id, created_at DESC);

-- Index foreign keys
CREATE INDEX idx_{resource}_created_by ON {resource_name}(created_by);

-- Update trigger
CREATE TRIGGER update_{resource}_updated_at
  BEFORE UPDATE ON {resource_name}
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

### Example: Projects Table

```sql
CREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations ON DELETE CASCADE,
  
  -- Project fields
  name TEXT NOT NULL,
  description TEXT,
  status TEXT DEFAULT 'active' CHECK (status IN ('active', 'archived', 'deleted')),
  color TEXT,
  icon TEXT,
  
  -- Relationships
  parent_project_id UUID REFERENCES projects,
  
  -- Metadata
  created_by UUID REFERENCES users,
  updated_by UUID REFERENCES users,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  deleted_at TIMESTAMPTZ,
  
  CONSTRAINT name_not_empty CHECK (length(trim(name)) > 0)
);

-- Indexes
CREATE INDEX idx_projects_org_id ON projects(organization_id);
CREATE INDEX idx_projects_org_status ON projects(organization_id, status) 
  WHERE deleted_at IS NULL;
CREATE INDEX idx_projects_org_created ON projects(organization_id, created_at DESC);
CREATE INDEX idx_projects_parent ON projects(parent_project_id) 
  WHERE parent_project_id IS NOT NULL;
```

---

## Row Level Security (RLS) Policies

### Critical RLS Pattern

**Every table with organization_id MUST have RLS enabled.**

```sql
-- Enable RLS
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only access their organization's data
CREATE POLICY "Users access own organization data"
  ON projects
  FOR ALL
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

-- More granular policies for different operations
CREATE POLICY "Users view organization projects"
  ON projects
  FOR SELECT
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Members can create projects"
  ON projects
  FOR INSERT
  WITH CHECK (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
        AND role IN ('owner', 'admin', 'member')
    )
  );

CREATE POLICY "Members can update projects"
  ON projects
  FOR UPDATE
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
        AND role IN ('owner', 'admin', 'member')
    )
  );

CREATE POLICY "Only owners and admins can delete"
  ON projects
  FOR DELETE
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
        AND role IN ('owner', 'admin')
    )
  );
```

### RLS for Organizations Table

```sql
ALTER TABLE organizations ENABLE ROW LEVEL SECURITY;

-- Users can view organizations they belong to
CREATE POLICY "Users view their organizations"
  ON organizations
  FOR SELECT
  USING (
    id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

-- Only owners can update organization
CREATE POLICY "Owners update organization"
  ON organizations
  FOR UPDATE
  USING (
    id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid() AND role = 'owner'
    )
  );

-- Any authenticated user can create organization
CREATE POLICY "Authenticated users create organizations"
  ON organizations
  FOR INSERT
  WITH CHECK (auth.uid() IS NOT NULL);
```

### RLS for Organization Members

```sql
ALTER TABLE organization_members ENABLE ROW LEVEL SECURITY;

-- View members of your organizations
CREATE POLICY "View organization members"
  ON organization_members
  FOR SELECT
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

-- Owners and admins can invite (insert)
CREATE POLICY "Owners and admins invite members"
  ON organization_members
  FOR INSERT
  WITH CHECK (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid() 
        AND role IN ('owner', 'admin')
    )
  );

-- Owners and admins can update roles
CREATE POLICY "Owners and admins update members"
  ON organization_members
  FOR UPDATE
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid() 
        AND role IN ('owner', 'admin')
    )
  );

-- Owners and admins can remove members
CREATE POLICY "Owners and admins remove members"
  ON organization_members
  FOR DELETE
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid() 
        AND role IN ('owner', 'admin')
    )
    -- Cannot remove the last owner
    AND NOT (
      role = 'owner' AND (
        SELECT COUNT(*) 
        FROM organization_members 
        WHERE organization_id = organization_members.organization_id 
          AND role = 'owner'
      ) = 1
    )
  );
```

---

## Subscription & Billing Schema

```sql
-- ============================================
-- SUBSCRIPTIONS
-- ============================================
CREATE TABLE subscriptions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations UNIQUE,
  
  -- Stripe data
  stripe_customer_id TEXT UNIQUE,
  stripe_subscription_id TEXT UNIQUE,
  stripe_price_id TEXT,
  
  -- Subscription details
  plan TEXT NOT NULL,
  status TEXT NOT NULL CHECK (status IN (
    'active', 'trialing', 'past_due', 'canceled', 'unpaid'
  )),
  
  -- Billing period
  current_period_start TIMESTAMPTZ,
  current_period_end TIMESTAMPTZ,
  cancel_at_period_end BOOLEAN DEFAULT FALSE,
  canceled_at TIMESTAMPTZ,
  
  -- Metadata
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_subscriptions_org ON subscriptions(organization_id);
CREATE INDEX idx_subscriptions_stripe_customer 
  ON subscriptions(stripe_customer_id);
CREATE INDEX idx_subscriptions_status ON subscriptions(status);

-- RLS
ALTER TABLE subscriptions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Owners view subscription"
  ON subscriptions
  FOR SELECT
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid() AND role = 'owner'
    )
  );

-- ============================================
-- USAGE TRACKING (for metered billing)
-- ============================================
CREATE TABLE usage_records (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations,
  
  -- Usage data
  metric TEXT NOT NULL,  -- e.g., 'api_calls', 'storage_gb'
  quantity NUMERIC NOT NULL,
  
  -- Time period
  period_start TIMESTAMPTZ NOT NULL,
  period_end TIMESTAMPTZ NOT NULL,
  
  -- Metadata
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_usage_org_metric 
  ON usage_records(organization_id, metric, period_start DESC);
CREATE INDEX idx_usage_period 
  ON usage_records(period_start, period_end);

-- Partition by month for performance
-- (Optional, for high-volume applications)
```

---

## Activity & Audit Logging

```sql
-- ============================================
-- AUDIT LOGS
-- ============================================
CREATE TABLE audit_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations,
  user_id UUID REFERENCES users,
  
  -- Action details
  action TEXT NOT NULL,  -- 'create', 'update', 'delete'
  resource_type TEXT NOT NULL,  -- 'project', 'task', etc.
  resource_id UUID,
  
  -- Changes
  old_values JSONB,
  new_values JSONB,
  
  -- Context
  ip_address INET,
  user_agent TEXT,
  
  -- Timestamp
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_audit_org_created 
  ON audit_logs(organization_id, created_at DESC);
CREATE INDEX idx_audit_user ON audit_logs(user_id, created_at DESC);
CREATE INDEX idx_audit_resource 
  ON audit_logs(resource_type, resource_id);

-- Partition by month for performance
CREATE TABLE audit_logs_2025_01 PARTITION OF audit_logs
  FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');

-- RLS
ALTER TABLE audit_logs ENABLE ROW LEVEL SECURITY;

CREATE POLICY "View own organization audit logs"
  ON audit_logs
  FOR SELECT
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid() 
        AND role IN ('owner', 'admin')
    )
  );
```

---

## Helper Functions

```sql
-- ============================================
-- UPDATE TIMESTAMP FUNCTION
-- ============================================
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- ============================================
-- SOFT DELETE FUNCTION
-- ============================================
CREATE OR REPLACE FUNCTION soft_delete()
RETURNS TRIGGER AS $$
BEGIN
  NEW.deleted_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- ============================================
-- CHECK USER MEMBERSHIP
-- ============================================
CREATE OR REPLACE FUNCTION user_is_member(
  check_org_id UUID,
  check_user_id UUID DEFAULT auth.uid()
)
RETURNS BOOLEAN AS $$
BEGIN
  RETURN EXISTS (
    SELECT 1 FROM organization_members
    WHERE organization_id = check_org_id
      AND user_id = check_user_id
  );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- ============================================
-- CHECK USER ROLE
-- ============================================
CREATE OR REPLACE FUNCTION user_has_role(
  check_org_id UUID,
  required_role TEXT,
  check_user_id UUID DEFAULT auth.uid()
)
RETURNS BOOLEAN AS $$
DECLARE
  user_role TEXT;
BEGIN
  SELECT role INTO user_role
  FROM organization_members
  WHERE organization_id = check_org_id
    AND user_id = check_user_id;
  
  RETURN CASE required_role
    WHEN 'owner' THEN user_role = 'owner'
    WHEN 'admin' THEN user_role IN ('owner', 'admin')
    WHEN 'member' THEN user_role IN ('owner', 'admin', 'member')
    WHEN 'guest' THEN user_role IN ('owner', 'admin', 'member', 'guest')
    ELSE FALSE
  END;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- ============================================
-- AUDIT LOG TRIGGER FUNCTION
-- ============================================
CREATE OR REPLACE FUNCTION audit_log_changes()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'DELETE' THEN
    INSERT INTO audit_logs (
      organization_id,
      user_id,
      action,
      resource_type,
      resource_id,
      old_values
    ) VALUES (
      OLD.organization_id,
      auth.uid(),
      'delete',
      TG_TABLE_NAME,
      OLD.id,
      to_jsonb(OLD)
    );
    RETURN OLD;
  ELSIF TG_OP = 'UPDATE' THEN
    INSERT INTO audit_logs (
      organization_id,
      user_id,
      action,
      resource_type,
      resource_id,
      old_values,
      new_values
    ) VALUES (
      NEW.organization_id,
      auth.uid(),
      'update',
      TG_TABLE_NAME,
      NEW.id,
      to_jsonb(OLD),
      to_jsonb(NEW)
    );
    RETURN NEW;
  ELSIF TG_OP = 'INSERT' THEN
    INSERT INTO audit_logs (
      organization_id,
      user_id,
      action,
      resource_type,
      resource_id,
      new_values
    ) VALUES (
      NEW.organization_id,
      auth.uid(),
      'create',
      TG_TABLE_NAME,
      NEW.id,
      to_jsonb(NEW)
    );
    RETURN NEW;
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Apply to tables that need auditing
CREATE TRIGGER audit_projects
  AFTER INSERT OR UPDATE OR DELETE ON projects
  FOR EACH ROW
  EXECUTE FUNCTION audit_log_changes();
```

---

## Performance Optimization

### Materialized Views for Analytics

```sql
-- Organization statistics
CREATE MATERIALIZED VIEW organization_stats AS
SELECT 
  o.id,
  o.name,
  o.plan,
  COUNT(DISTINCT om.user_id) as member_count,
  COUNT(DISTINCT p.id) as project_count,
  MAX(p.created_at) as last_project_created,
  o.created_at
FROM organizations o
LEFT JOIN organization_members om ON o.id = om.organization_id
LEFT JOIN projects p ON o.id = p.organization_id AND p.deleted_at IS NULL
GROUP BY o.id, o.name, o.plan, o.created_at;

-- Index for fast lookups
CREATE UNIQUE INDEX idx_org_stats_id ON organization_stats(id);

-- Refresh periodically (via cron job or manually)
REFRESH MATERIALIZED VIEW CONCURRENTLY organization_stats;
```

### Composite Indexes for Common Queries

```sql
-- Frequently filtered and sorted together
CREATE INDEX idx_projects_org_status_created 
  ON projects(organization_id, status, created_at DESC)
  WHERE deleted_at IS NULL;

-- For full-text search
CREATE INDEX idx_projects_name_trgm ON projects 
  USING gin (name gin_trgm_ops);
```

---

## Data Retention & Cleanup

```sql
-- Function to hard delete soft-deleted records
CREATE OR REPLACE FUNCTION cleanup_deleted_records()
RETURNS void AS $$
BEGIN
  -- Delete records soft-deleted over 90 days ago
  DELETE FROM projects
  WHERE deleted_at < NOW() - INTERVAL '90 days';
  
  -- Clean up old audit logs (keep 1 year)
  DELETE FROM audit_logs
  WHERE created_at < NOW() - INTERVAL '1 year';
  
  -- Clean up expired invitation tokens
  DELETE FROM organization_members
  WHERE invitation_token IS NOT NULL
    AND invitation_accepted_at IS NULL
    AND joined_at < NOW() - INTERVAL '7 days';
END;
$$ LANGUAGE plpgsql;

-- Schedule via pg_cron or external scheduler
```

---

## Migration Strategy

### Initial Schema Migration

```sql
-- 001_initial_schema.sql
BEGIN;

-- Create extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";

-- Create tables (organizations, users, etc.)
-- ... (all CREATE TABLE statements)

-- Create functions
-- ... (all function definitions)

-- Create indexes
-- ... (all index definitions)

COMMIT;
```

### Subsequent Migrations

```sql
-- 002_add_project_templates.sql
BEGIN;

CREATE TABLE project_templates (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID REFERENCES organizations,
  name TEXT NOT NULL,
  description TEXT,
  template_data JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_project_templates_org 
  ON project_templates(organization_id);

ALTER TABLE project_templates ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users view organization templates"
  ON project_templates FOR SELECT
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

COMMIT;
```

---

## Testing Queries

```sql
-- Test RLS: Ensure users can only see their org's data
SET ROLE authenticated;
SET request.jwt.claim.sub = 'user-uuid-here';

SELECT * FROM projects;  -- Should only return user's org projects
SELECT * FROM organizations;  -- Should only return user's orgs

-- Test performance
EXPLAIN ANALYZE
SELECT * FROM projects
WHERE organization_id = 'org-uuid'
  AND status = 'active'
ORDER BY created_at DESC
LIMIT 20;

-- Should use index scan, not seq scan
```

---

## Critical Checklist

✅ **Every resource table has `organization_id`**  
✅ **Every resource table has RLS enabled**  
✅ **Every `organization_id` column is indexed**  
✅ **Foreign keys have indexes**  
✅ **Updated_at triggers on all tables**  
✅ **Audit logging on sensitive tables**  
✅ **Soft delete for user-facing data**  
✅ **Constraints for data validity**  
✅ **Helper functions for common checks**  
✅ **Migration files are numbered and reversible**  

---

This schema ensures data isolation, security, performance, and scalability for your SaaS application.
