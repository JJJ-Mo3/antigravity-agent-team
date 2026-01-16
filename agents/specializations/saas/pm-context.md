# SaaS Product - Product Manager Context

## Platform Understanding
You are creating a PRD for a Software-as-a-Service (SaaS) product. SaaS products are cloud-based applications delivered via web browsers, typically with subscription-based pricing and multi-tenant architecture.

---

## Key Questions to Ask

### Business Model
- **Pricing Structure:**
  - Freemium model? (Free tier + paid plans)
  - Tiered pricing? (Starter, Pro, Enterprise)
  - Per-seat pricing or flat rate?
  - Usage-based pricing?
  - Annual vs. monthly billing?
  
- **Target Market:**
  - B2B or B2C?
  - SMB, Mid-market, or Enterprise?
  - Specific industries or verticals?
  - Geographic focus?

### Multi-Tenancy
- **Organization Structure:**
  - Individual users only?
  - Teams/workspaces/organizations?
  - Hierarchical structure? (Company → Team → User)
  - How do users switch between organizations?

- **Data Isolation:**
  - Strict data separation between tenants?
  - Any shared data or resources?
  - Cross-tenant features needed?

### User Management
- **Roles & Permissions:**
  - What roles are needed? (Owner, Admin, Member, Guest)
  - Custom roles required?
  - Granular permissions?
  - Role-based feature access?

- **Invitation System:**
  - How do users join organizations?
  - Email invitations? Invite links?
  - Approval workflow?
  - SSO/SAML for Enterprise?

### Features & Functionality
- **Core Features:**
  - What is the primary value proposition?
  - What problem does this solve?
  - Must-have features for MVP?
  - Nice-to-have features for later?

- **Collaboration Features:**
  - Real-time collaboration needed?
  - Comments/discussions?
  - Activity feeds?
  - Notifications (email, in-app, push)?
  - Sharing/export capabilities?

### Subscription & Billing
- **Payment Processing:**
  - Credit card payments?
  - Invoice billing for Enterprise?
  - Payment provider? (Stripe, Paddle)
  - Trial period? How long?
  - Upgrade/downgrade flow?

- **Feature Gating:**
  - Which features are premium?
  - Usage limits on free tier?
  - Hard limits or soft limits?
  - How to handle limit overages?

### Integrations
- **Third-Party Services:**
  - Which integrations are critical?
  - OAuth providers? (Google, Microsoft, GitHub)
  - API integrations? (Slack, Zapier, webhooks)
  - Export/import capabilities?

### Onboarding
- **User Journey:**
  - Signup flow complexity?
  - Email verification required?
  - Onboarding tutorial/walkthrough?
  - Sample data or empty state?
  - Time to first value?

---

## SaaS-Specific Features to Consider

### 1. Organization/Workspace Management
- Create/rename/delete organizations
- Switch between organizations
- Organization settings
- Branding customization

### 2. Team Collaboration
- Invite team members
- Manage member roles
- Activity tracking
- Shared resources

### 3. Subscription Management
- Plan selection
- Payment method management
- Billing history
- Usage tracking
- Plan upgrades/downgrades

### 4. User Profile & Settings
- Personal settings
- Notification preferences
- Connected accounts
- API keys/tokens
- Two-factor authentication

### 5. Admin Dashboard
- User management
- Usage analytics
- Billing overview
- System settings
- Audit logs

### 6. Security & Compliance
- Data encryption
- Access logs
- GDPR compliance
- Data export/deletion
- Session management

---

## Requirements Sections

Your PRD should include these SaaS-specific sections:

### Multi-Tenancy Model
```
Organization Structure:
- Organization has many users
- Users can belong to multiple organizations
- Role-based access control per organization
- Data isolated by organization_id

Switching Context:
- Organization selector in navbar
- Persist selected organization
- Clear context on switch
```

### Subscription Tiers
```
Free Tier:
- 1 user per organization
- Basic features
- 5 projects limit
- Community support

Pro Tier ($29/month):
- Unlimited users
- Advanced features
- Unlimited projects
- Email support
- Priority features

Enterprise (Custom pricing):
- Everything in Pro
- SSO/SAML
- SLA guarantee
- Dedicated support
- Custom contracts
```

### User Roles & Permissions
```
Owner:
- Full access
- Manage billing
- Delete organization
- Cannot be removed

Admin:
- All features access
- Manage members
- Manage settings
- Cannot access billing

Member:
- Standard features
- View only for settings
- Cannot invite users

Guest:
- Read-only access
- Limited feature access
- Cannot modify data
```

### Onboarding Flow
```
Step 1: Sign up
- Email + password OR OAuth
- Email verification

Step 2: Create organization
- Organization name
- Organization URL/slug
- Industry selection (optional)

Step 3: Select plan
- Show tier comparison
- Start trial OR select plan
- Collect payment info (Pro+)

Step 4: Invite team (optional)
- Enter email addresses
- Assign roles
- Send invitations

Step 5: Setup/tutorial
- Welcome tour
- Quick start guide
- Sample data option
```

### Feature Access Matrix
```
| Feature              | Free | Pro | Enterprise |
|---------------------|------|-----|------------|
| Users per org       | 1    | ∞   | ∞          |
| Projects            | 5    | ∞   | ∞          |
| Storage             | 1GB  | 100GB | Custom   |
| API access          | ❌   | ✅  | ✅         |
| Advanced analytics  | ❌   | ✅  | ✅         |
| Priority support    | ❌   | ✅  | ✅         |
| SSO/SAML           | ❌   | ❌  | ✅         |
| Custom contracts    | ❌   | ❌  | ✅         |
```

### Trial Management
```
Trial Period: 14 days
- Full access to Pro features
- No credit card required
- Email reminders at 7 days, 3 days, 1 day
- Graceful downgrade to Free after trial
- Easy upgrade during trial
```

### Notification Strategy
```
Email Notifications:
- Welcome email
- Team invitations
- Trial expiring
- Payment successful/failed
- Important product updates

In-App Notifications:
- Real-time activity
- Mentions/assignments
- System alerts
- Feature announcements

Push Notifications (optional):
- Mobile app only
- Critical alerts
- User can configure
```

---

## Anti-patterns to Avoid

❌ **Don't:**
- Force credit card for free trial
- Make organization switching difficult
- Hide pricing information
- Require long forms during signup
- Lock users into annual contracts immediately
- Make it hard to downgrade/cancel
- Surprise users with auto-renewals
- Have unclear feature limitations

✅ **Do:**
- Transparent pricing
- Easy signup (< 60 seconds)
- Clear trial terms
- Simple upgrade path
- Self-service billing
- Easy cancellation
- Fair usage policies
- Proactive communication

---

## Metrics to Track

### Acquisition
- Signups per day/week/month
- Signup source attribution
- Signup funnel conversion rate
- Time from landing to signup

### Activation
- Time to first value
- Onboarding completion rate
- Feature adoption rate
- Trial start rate

### Retention
- Daily/Weekly/Monthly Active Users
- Churn rate
- Feature usage frequency
- Session duration

### Revenue
- Monthly Recurring Revenue (MRR)
- Average Revenue Per User (ARPU)
- Lifetime Value (LTV)
- Customer Acquisition Cost (CAC)
- Trial-to-paid conversion rate

### Engagement
- Feature usage by tier
- Team collaboration metrics
- Support ticket volume
- Net Promoter Score (NPS)

---

## Success Criteria

Define clear success metrics:

```
Launch (Month 1):
- 100 signups
- 20% trial conversion
- < 5% churn

Growth (Month 3):
- 500 signups
- 25% trial conversion
- < 3% churn
- $5,000 MRR

Scale (Month 6):
- 2,000 signups
- 30% trial conversion
- < 2% churn
- $25,000 MRR
```

---

## Technical Considerations

### Architecture
- Multi-tenant database with RLS
- Horizontal scalability
- API-first design
- Real-time capabilities

### Security
- Data encryption at rest and in transit
- Regular security audits
- SOC 2 compliance path
- GDPR compliance
- Data residency options

### Performance
- Fast page loads (< 2s)
- Real-time updates (< 1s)
- API response times (< 500ms)
- 99.9% uptime SLA

### Integrations
- OAuth providers
- Payment processor (Stripe)
- Email service (SendGrid)
- Analytics (Mixpanel/Amplitude)
- Error tracking (Sentry)
- Support system (Intercom)

---

## Competitive Analysis

Research and document:
- Top 3-5 competitors
- Their pricing models
- Their key features
- Their strengths/weaknesses
- Market gaps/opportunities
- Your differentiation strategy

---

## Go-to-Market Strategy

Include:
- Target customer profile
- Marketing channels
- Content strategy
- Partnership opportunities
- Launch timeline
- Beta program details

---

## Risk Assessment

Identify and mitigate:
- Market risks
- Technical risks
- Competitive risks
- Financial risks
- Operational risks

---

This context helps you create comprehensive PRDs for SaaS products with all the critical components for success.
