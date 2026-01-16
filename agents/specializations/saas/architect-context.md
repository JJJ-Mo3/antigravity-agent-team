# SaaS Product - Technical Architect Context

## Architecture Overview
SaaS applications require multi-tenant architecture with strong data isolation, scalability, security, and reliability. You're designing for Supabase (backend) and Netlify (frontend) deployment.

---

## Core Architectural Principles

### 1. Multi-Tenancy
**Every design decision must consider tenant isolation.**

```
Tenant Hierarchy:
┌─────────────────────┐
│   Organization      │ ← Tenant root
├─────────────────────┤
│   Users             │ ← Belong to orgs
│   Resources         │ ← Owned by orgs
│   Settings          │ ← Per-org config
└─────────────────────┘
```

**Key Pattern:** `organization_id` in every table

### 2. Security First
- RLS (Row Level Security) is mandatory
- API keys are tenant-scoped
- No cross-tenant data leaks
- Audit logging for all actions

### 3. Scalability
- Stateless application tier
- Database connection pooling
- Caching strategy
- Async job processing

### 4. Performance
- < 2s page loads
- < 500ms API responses
- Real-time where needed
- Optimistic UI updates

---

## Technology Stack

### Frontend (Netlify)
```javascript
Framework: React 18+ with TypeScript
State: React Query + Zustand
  - React Query: Server state, caching
  - Zustand: Local UI state, organization context
Styling: Tailwind CSS
Routing: React Router v6
Forms: React Hook Form + Zod validation
Real-time: Supabase Realtime
Build: Vite
Testing: Jest + React Testing Library + Playwright
```

### Backend (Supabase)
```
Database: PostgreSQL 15+ with RLS
Auth: Supabase Auth (email, OAuth, magic links)
Storage: Supabase Storage with RLS
Real-time: Supabase Realtime (WebSocket)
Functions: Supabase Edge Functions (Deno)
API: Auto-generated REST + GraphQL
```

### Third-Party Services
```
Payments: Stripe
Email: SendGrid
Analytics: Mixpanel or Amplitude
Monitoring: Sentry
Support: Intercom (optional)
```

---

## Database Architecture

### Multi-Tenant Pattern

**Option: Shared Database, Shared Schema** (Recommended)

```sql
-- All tables have organization_id
CREATE TABLE organizations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  plan TEXT NOT NULL DEFAULT 'free',
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE users (
  id UUID PRIMARY KEY REFERENCES auth.users,
  email TEXT NOT NULL,
  name TEXT,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Junction table for user-org relationship
CREATE TABLE organization_members (
  organization_id UUID REFERENCES organizations ON DELETE CASCADE,
  user_id UUID REFERENCES users ON DELETE CASCADE,
  role TEXT NOT NULL DEFAULT 'member',
  invited_by UUID REFERENCES users,
  joined_at TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (organization_id, user_id)
);

-- All resource tables include organization_id
CREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations ON DELETE CASCADE,
  name TEXT NOT NULL,
  description TEXT,
  created_by UUID REFERENCES users,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- RLS Policies
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view their organization's projects"
  ON projects FOR SELECT
  USING (
    organization_id IN (
      SELECT organization_id 
      FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );
```

### Indexes Strategy
```sql
-- Always index organization_id for tenant queries
CREATE INDEX idx_projects_organization_id ON projects(organization_id);
CREATE INDEX idx_projects_org_created ON projects(organization_id, created_at DESC);

-- Index foreign keys
CREATE INDEX idx_projects_created_by ON projects(created_by);

-- Composite indexes for common queries
CREATE INDEX idx_org_members_user_org ON organization_members(user_id, organization_id);
```

---

## Authentication & Authorization

### Authentication Flow
```
1. User signs up/signs in (Supabase Auth)
   ↓
2. JWT token issued with user_id
   ↓
3. Frontend stores token (HttpOnly cookie preferred)
   ↓
4. All API requests include token
   ↓
5. RLS policies check auth.uid()
```

### Authorization Layers

**Layer 1: Row Level Security (Database)**
```sql
-- Database enforces organization membership
CREATE POLICY "view_policy" ON projects FOR SELECT
USING (
  organization_id IN (
    SELECT organization_id FROM organization_members 
    WHERE user_id = auth.uid()
  )
);
```

**Layer 2: Role-Based Access (Application)**
```typescript
enum Role {
  OWNER = 'owner',     // Full access
  ADMIN = 'admin',     // Manage members, settings
  MEMBER = 'member',   // Standard access
  GUEST = 'guest'      // Read-only
}

function hasPermission(
  user: User,
  action: string,
  resource: Resource
): boolean {
  const membership = getMembership(user.id, resource.organization_id);
  return rolePermissions[membership.role][action];
}
```

**Layer 3: Feature Gating (Subscription)**
```typescript
function canAccessFeature(
  org: Organization,
  feature: string
): boolean {
  return planFeatures[org.plan].includes(feature);
}
```

---

## API Architecture

### REST API Pattern (via Supabase)
```typescript
// Auto-generated by Supabase
GET    /rest/v1/projects              // List (with RLS)
GET    /rest/v1/projects?id=eq.123    // Get one
POST   /rest/v1/projects              // Create
PATCH  /rest/v1/projects?id=eq.123    // Update
DELETE /rest/v1/projects?id=eq.123    // Delete

// With filters and joins
GET /rest/v1/projects?
  organization_id=eq.{orgId}&
  select=*,created_by(name,email)&
  order=created_at.desc&
  limit=20
```

### Edge Functions (Custom Logic)
```typescript
// supabase/functions/process-payment/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts';
import Stripe from 'https://esm.sh/stripe@11.1.0';

serve(async (req) => {
  const { organizationId, planId } = await req.json();
  
  // Custom business logic
  const stripe = new Stripe(Deno.env.get('STRIPE_SECRET_KEY')!);
  const session = await stripe.checkout.sessions.create({
    // ...
  });
  
  return new Response(JSON.stringify({ sessionUrl: session.url }));
});
```

### Real-time Subscriptions
```typescript
// Client subscribes to organization's resources
const subscription = supabase
  .channel(`org:${organizationId}:projects`)
  .on(
    'postgres_changes',
    {
      event: '*',
      schema: 'public',
      table: 'projects',
      filter: `organization_id=eq.${organizationId}`
    },
    (payload) => {
      // Update UI in real-time
      queryClient.invalidateQueries(['projects']);
    }
  )
  .subscribe();
```

---

## Frontend Architecture

### Application Structure
```
src/
├── features/              ← Feature-based organization
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api.ts
│   │   └── types.ts
│   ├── organizations/
│   ├── projects/
│   └── billing/
│
├── shared/                ← Shared code
│   ├── components/        ← Reusable UI
│   ├── hooks/            ← Reusable hooks
│   ├── utils/            ← Utilities
│   └── types/            ← Shared types
│
├── lib/                   ← Third-party setup
│   ├── supabase.ts
│   ├── stripe.ts
│   └── analytics.ts
│
└── app/                   ← App setup
    ├── routes/           ← Route definitions
    ├── providers/        ← Context providers
    └── App.tsx
```

### State Management Pattern
```typescript
// Organization context (Zustand)
interface OrgStore {
  currentOrg: Organization | null;
  setCurrentOrg: (org: Organization) => void;
  membership: Membership | null;
}

export const useOrgStore = create<OrgStore>((set) => ({
  currentOrg: null,
  setCurrentOrg: (org) => set({ currentOrg: org }),
  membership: null,
}));

// Server state (React Query)
function useProjects(orgId: string) {
  return useQuery({
    queryKey: ['projects', orgId],
    queryFn: async () => {
      const { data } = await supabase
        .from('projects')
        .select('*')
        .eq('organization_id', orgId);
      return data;
    },
    enabled: !!orgId,
  });
}
```

### Organization Context Pattern
```typescript
// Wrap app with organization context
function App() {
  return (
    <OrgProvider>
      <Routes />
    </OrgProvider>
  );
}

// OrgProvider manages current organization
function OrgProvider({ children }) {
  const { user } = useAuth();
  const { currentOrg, setCurrentOrg } = useOrgStore();
  
  // Load user's organizations
  const { data: orgs } = useUserOrganizations(user?.id);
  
  // Set default org if none selected
  useEffect(() => {
    if (!currentOrg && orgs?.length > 0) {
      setCurrentOrg(orgs[0]);
    }
  }, [orgs, currentOrg]);
  
  return children;
}

// Any component can access current org
function ProjectList() {
  const { currentOrg } = useOrgStore();
  const { data: projects } = useProjects(currentOrg?.id);
  
  return <div>{/* ... */}</div>;
}
```

---

## Subscription & Billing Architecture

### Plan Management
```typescript
// Database schema
CREATE TABLE subscriptions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID REFERENCES organizations UNIQUE,
  stripe_customer_id TEXT UNIQUE,
  stripe_subscription_id TEXT UNIQUE,
  plan TEXT NOT NULL,
  status TEXT NOT NULL,
  current_period_start TIMESTAMPTZ,
  current_period_end TIMESTAMPTZ,
  cancel_at_period_end BOOLEAN DEFAULT FALSE
);

// Feature access check
function canAccessFeature(org: Organization, feature: string): boolean {
  const planFeatures = {
    free: ['basic_projects', 'basic_tasks'],
    pro: ['basic_projects', 'basic_tasks', 'advanced_analytics', 'api_access'],
    enterprise: ['*'] // All features
  };
  
  return planFeatures[org.plan].includes(feature) || 
         planFeatures[org.plan].includes('*');
}
```

### Stripe Integration
```typescript
// Checkout flow
async function createCheckoutSession(orgId: string, planId: string) {
  const { data, error } = await supabase.functions.invoke('create-checkout', {
    body: { organizationId: orgId, planId }
  });
  
  // Redirect to Stripe Checkout
  window.location.href = data.checkoutUrl;
}

// Webhook handler (Edge Function)
serve(async (req) => {
  const signature = req.headers.get('stripe-signature');
  const event = stripe.webhooks.constructEvent(
    await req.text(),
    signature,
    webhookSecret
  );
  
  switch (event.type) {
    case 'customer.subscription.created':
      // Update database
      await supabase.from('subscriptions').insert({
        organization_id: metadata.organizationId,
        stripe_subscription_id: subscription.id,
        status: subscription.status,
        plan: 'pro'
      });
      break;
    
    case 'customer.subscription.deleted':
      // Downgrade to free
      await supabase.from('organizations')
        .update({ plan: 'free' })
        .eq('id', metadata.organizationId);
      break;
  }
});
```

---

## Real-time Architecture

### When to Use Real-time
✅ Use for:
- Collaborative editing
- Activity feeds
- Notifications
- Live dashboards
- Presence indicators

❌ Avoid for:
- Static data
- Infrequent updates
- Large datasets
- Heavy processing

### Implementation Pattern
```typescript
// Subscribe to organization's updates
useEffect(() => {
  if (!currentOrg) return;
  
  const channel = supabase
    .channel(`org:${currentOrg.id}`)
    .on(
      'postgres_changes',
      {
        event: '*',
        schema: 'public',
        table: 'projects',
        filter: `organization_id=eq.${currentOrg.id}`
      },
      (payload) => {
        // Optimistic update or full refresh
        queryClient.invalidateQueries(['projects', currentOrg.id]);
      }
    )
    .subscribe();
  
  return () => {
    channel.unsubscribe();
  };
}, [currentOrg]);
```

---

## Performance Optimization

### Frontend Performance
```typescript
// Code splitting by route
const Projects = lazy(() => import('./features/projects'));
const Settings = lazy(() => import('./features/settings'));

// Memoize expensive computations
const sortedProjects = useMemo(
  () => projects?.sort((a, b) => b.created_at - a.created_at),
  [projects]
);

// Virtualize long lists
import { FixedSizeList } from 'react-window';
```

### API Performance
```typescript
// Pagination
const { data, fetchNextPage } = useInfiniteQuery({
  queryKey: ['projects', orgId],
  queryFn: async ({ pageParam = 0 }) => {
    const { data } = await supabase
      .from('projects')
      .select('*')
      .eq('organization_id', orgId)
      .range(pageParam * 20, (pageParam + 1) * 20 - 1);
    return data;
  },
});

// Caching strategy
const { data } = useQuery({
  queryKey: ['org', orgId],
  queryFn: () => fetchOrganization(orgId),
  staleTime: 5 * 60 * 1000, // 5 minutes
  cacheTime: 30 * 60 * 1000, // 30 minutes
});
```

### Database Performance
```sql
-- Materialized views for analytics
CREATE MATERIALIZED VIEW org_stats AS
SELECT 
  o.id,
  COUNT(DISTINCT om.user_id) as member_count,
  COUNT(DISTINCT p.id) as project_count
FROM organizations o
LEFT JOIN organization_members om ON o.id = om.organization_id
LEFT JOIN projects p ON o.id = p.organization_id
GROUP BY o.id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW CONCURRENTLY org_stats;
```

---

## Security Architecture

### Defense in Depth

**Layer 1: Network** - HTTPS only, CORS configured
**Layer 2: Authentication** - JWT tokens, session management
**Layer 3: Authorization** - RLS policies, role checks
**Layer 4: Validation** - Input validation, sanitization
**Layer 5: Monitoring** - Audit logs, alerts

### Common Vulnerabilities

**Prevent:**
- ❌ Cross-tenant data leaks
- ❌ Privilege escalation
- ❌ SQL injection
- ❌ XSS attacks
- ❌ CSRF attacks

**Implement:**
- ✅ Parameterized queries
- ✅ Content Security Policy
- ✅ Rate limiting
- ✅ Input sanitization
- ✅ Audit logging

---

## Scalability Plan

### Current (0-10K users)
- Single region deployment
- Supabase free/pro tier
- Netlify standard hosting
- Basic monitoring

### Growth (10K-100K users)
- Multi-region failover
- Database read replicas
- CDN optimization
- Advanced monitoring
- Horizontal scaling

### Scale (100K+ users)
- Database sharding
- Microservices extraction
- Global CDN
- Dedicated support
- Enterprise features

---

## Deployment Architecture

```
┌─────────────────────────────────────┐
│         Netlify Edge Network         │
│  ┌────────────────────────────────┐ │
│  │    Static Site (React)         │ │
│  │    + Serverless Functions       │ │
│  └────────────────────────────────┘ │
└─────────────────────────────────────┘
              ↓ API Calls
┌─────────────────────────────────────┐
│         Supabase Platform           │
│  ┌────────────────────────────────┐ │
│  │    PostgreSQL + RLS            │ │
│  │    Auth + Storage              │ │
│  │    Realtime + Edge Functions   │ │
│  └────────────────────────────────┘ │
└─────────────────────────────────────┘
              ↓ Integrations
┌─────────────────────────────────────┐
│       Third-Party Services          │
│     Stripe | SendGrid | Sentry      │
└─────────────────────────────────────┘
```

---

This architecture ensures your SaaS product is secure, scalable, performant, and maintainable.
