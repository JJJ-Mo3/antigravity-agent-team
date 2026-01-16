# Shopify App - Product Manager Context

## Platform Understanding
You are creating a PRD for a Shopify App. Shopify apps extend Shopify stores with additional functionality via the Shopify App Store. Apps can be public (available to all merchants) or custom (built for specific merchants).

---

## Key Questions to Ask

### App Type & Distribution
- **App Type:**
  - Public app (Shopify App Store)?
  - Custom app (single merchant)?
  - Draft app (development only)?
  
- **Distribution:**
  - Free app?
  - One-time purchase?
  - Recurring subscription?
  - Usage-based pricing?

### Shopify Integration Scope
- **Store Access:**
  - What store data do you need to read?
  - What store data do you need to modify?
  - Real-time updates via webhooks?
  - Offline access needed?

- **Required Scopes:**
  - Products? (read_products, write_products)
  - Orders? (read_orders, write_orders)
  - Customers? (read_customers, write_customers)
  - Inventory?
  - Fulfillment?
  - Content? (pages, blogs)
  - Settings? (store settings)

### App Features
- **Core Functionality:**
  - What problem does this solve for merchants?
  - What makes it better than alternatives?
  - What's the primary use case?
  - What's the user flow?

- **Embedded Features:**
  - Admin UI extensions?
  - Checkout UI extensions?
  - Theme app extensions?
  - POS extensions?
  - App blocks in Online Store?

### Webhooks & Real-time
- **Event Subscriptions:**
  - Which events to listen to?
  - Orders created/updated?
  - Products created/updated?
  - Customer events?
  - Inventory changes?
  - Fulfillment updates?

### Data Storage
- **Merchant Data:**
  - What data needs to be stored per merchant?
  - Customer data? (GDPR considerations)
  - Order data?
  - Analytics data?
  - Configuration/settings?

### Billing Integration
- **Shopify Billing API:**
  - Recurring subscription?
  - Usage charges?
  - One-time charges?
  - Trial period?
  - Pricing tiers?

---

## Shopify-Specific Features to Consider

### 1. OAuth & Installation
- App installation flow
- Scope selection
- Merchant authentication
- Store domain handling

### 2. Admin Embedded App
- Shopify App Bridge integration
- Navigation within Shopify admin
- Polaris design system
- Embedded app authentication

### 3. Webhooks
- Event subscriptions
- Webhook verification
- Retry handling
- Rate limits

### 4. API Interactions
- GraphQL Admin API
- REST Admin API
- Storefront API (if needed)
- Rate limiting strategy

### 5. App Extensions
- Admin UI extensions
- Checkout extensions
- Theme app extensions
- POS extensions

### 6. Billing
- Subscription plans
- Usage charges
- One-time charges
- Trial periods

### 7. GDPR Compliance
- Customer data requests
- Customer data deletion
- Shop data deletion
- Data retention policies

---

## Requirements Sections

Your PRD should include these Shopify-specific sections:

### OAuth Scopes Required
```
Read Scopes:
- read_products: Access product catalog
- read_orders: Access order data
- read_customers: Access customer data (with GDPR)
- read_inventory: Access inventory levels

Write Scopes:
- write_products: Modify products
- write_orders: Update orders
- write_inventory: Update inventory

Note: Request minimum scopes necessary
```

### Webhook Subscriptions
```
Required Webhooks:
1. orders/create
   - Purpose: Track new orders
   - Action: Create order record in database
   - Frequency: High volume

2. products/update
   - Purpose: Sync product changes
   - Action: Update product cache
   - Frequency: Medium volume

3. app/uninstalled
   - Purpose: Clean up merchant data
   - Action: Mark merchant as inactive
   - Frequency: Low volume

4. customers/data_request (GDPR)
   - Purpose: Export customer data
   - Action: Generate data export
   - Frequency: Low volume

5. customers/redact (GDPR)
   - Purpose: Delete customer data
   - Action: Remove customer data
   - Frequency: Low volume

6. shop/redact (GDPR)
   - Purpose: Delete shop data
   - Action: Remove all shop data
   - Frequency: Low volume
```

### App Installation Flow
```
Step 1: Merchant clicks "Add app"
  ↓
Step 2: Shopify redirects to your install URL
  ↓
Step 3: Your app requests OAuth authorization
  - Display scopes being requested
  - Explain why each scope is needed
  ↓
Step 4: Merchant approves scopes
  ↓
Step 5: Shopify redirects back with auth code
  ↓
Step 6: Exchange auth code for access token
  ↓
Step 7: Store access token securely
  ↓
Step 8: Register webhooks
  ↓
Step 9: Redirect to app dashboard/onboarding
```

### App Dashboard (Embedded in Shopify Admin)
```
Layout:
┌─────────────────────────────────────────┐
│  Shopify Admin Navigation (Top)        │
├─────────────────────────────────────────┤
│  Your App (Embedded via App Bridge)    │
│                                         │
│  ┌───────────────────────────────────┐ │
│  │  Dashboard / Statistics           │ │
│  ├───────────────────────────────────┤ │
│  │  Main Feature Area                │ │
│  ├───────────────────────────────────┤ │
│  │  Settings                         │ │
│  └───────────────────────────────────┘ │
└─────────────────────────────────────────┘

Navigation uses Shopify App Bridge
UI uses Polaris design system
```

### Billing Configuration
```
Plan: Basic
- Price: $9.99/month
- Features: Core features only
- Trial: 14 days

Plan: Pro  
- Price: $29.99/month
- Features: Advanced features
- Trial: 14 days

Plan: Enterprise
- Price: $99.99/month
- Features: All features + priority support
- Trial: 14 days

Usage Charges (if applicable):
- Base: $9.99/month
- Additional: $0.10 per processed order
- Cap: $99.99/month
```

### GDPR Compliance Requirements
```
1. Customer Data Request (customers/data_request)
   - Receive webhook from Shopify
   - Compile all customer data stored in your app
   - Format as JSON
   - Provide download link
   - Respond within 30 days

2. Customer Redaction (customers/redact)
   - Receive webhook from Shopify
   - Delete or anonymize customer data
   - Respond within 30 days

3. Shop Redaction (shop/redact)
   - Receive webhook when merchant uninstalls
   - Delete all shop data after 48 hours
   - Keep minimal data for financial/legal requirements
```

### Rate Limiting Strategy
```
Shopify API Rate Limits:
- REST API: 2 requests/second (leaky bucket)
- GraphQL: 1000 points/second (calculated cost)

Strategy:
1. Use GraphQL for bulk operations (more efficient)
2. Implement exponential backoff
3. Queue non-urgent API calls
4. Cache frequently accessed data
5. Use webhooks instead of polling
6. Batch operations when possible
```

---

## Shopify Admin UI Requirements

### Polaris Design System
Your app MUST use Shopify's Polaris components:

```typescript
Required Components:
- Page (for main layouts)
- Layout (for responsive grids)
- Card (for content sections)
- DataTable (for tabular data)
- Button (for actions)
- TextField (for inputs)
- Select (for dropdowns)
- Modal (for dialogs)
- Banner (for alerts/notices)
- Loading (for loading states)

Navigation:
- Use App Bridge for navigation
- No full page reloads
- Maintain Shopify admin context
```

### App Bridge Integration
```javascript
// Initialize App Bridge
import createApp from '@shopify/app-bridge';
import { Redirect } from '@shopify/app-bridge/actions';

const app = createApp({
  apiKey: 'your-api-key',
  host: shopifyHost,
});

// Navigation within app
const redirect = Redirect.create(app);
redirect.dispatch(Redirect.Action.APP, '/settings');

// Open external URL
redirect.dispatch(Redirect.Action.REMOTE, 'https://example.com');
```

---

## Anti-patterns to Avoid

❌ **Don't:**
- Request scopes you don't need
- Store access tokens insecurely
- Poll Shopify API (use webhooks)
- Ignore rate limits
- Break Shopify admin UI patterns
- Ignore GDPR webhooks
- Make users leave Shopify admin
- Use non-Polaris UI components
- Forget to handle app uninstall
- Store sensitive customer data without encryption

✅ **Do:**
- Request minimal necessary scopes
- Encrypt access tokens at rest
- Use webhooks for real-time updates
- Implement proper rate limiting
- Follow Polaris design guidelines
- Handle all GDPR webhooks
- Keep users in Shopify admin
- Use Polaris components
- Clean up on uninstall
- Comply with GDPR

---

## Technical Architecture Notes

### Multi-Tenant Per Shop
```
Each Shopify store is a tenant:
- Store access token per shop
- Shop domain as identifier
- Separate data per shop
- Shop-level settings
```

### Database Schema Pattern
```sql
shops:
  - shop_domain (primary key)
  - access_token (encrypted)
  - scopes
  - plan
  - installed_at
  - uninstalled_at

shop_settings:
  - shop_domain (foreign key)
  - settings (JSONB)

shop_data:
  - shop_domain (foreign key)
  - data specific to your app
```

### Authentication Pattern
```
1. Install: OAuth flow → store access token
2. Requests: JWT session tokens for your app
3. API calls: Use stored Shopify access token
4. Webhooks: Verify HMAC signature
```

---

## Metrics to Track

### App Performance
- Installation rate
- Uninstall rate (churn)
- Active shops
- API call volume
- Webhook processing time
- Error rate

### Business Metrics
- MRR (Monthly Recurring Revenue)
- Trial-to-paid conversion
- Average revenue per shop
- Retention rate (30/60/90 day)

### User Engagement
- Daily/Weekly/Monthly active shops
- Feature adoption rate
- Support ticket volume
- App Store ratings/reviews

---

## App Store Listing Requirements

### Required Information
- App name (must be unique)
- App icon (1024x1024 PNG)
- Screenshots (1280x720 minimum)
- Description (clear, concise)
- Feature list
- Pricing information
- Support contact
- Privacy policy URL
- Terms of service URL

### App Store Guidelines
- Clear value proposition
- Accurate screenshots
- Honest feature claims
- Responsive support
- Regular updates
- Handle edge cases
- Graceful error handling

---

## Development Workflow

### Test Store Setup
```
1. Create Partner account (partners.shopify.com)
2. Create development store
3. Create app in Partner dashboard
4. Get API key and secret
5. Install app in test store
6. Test full flow
```

### App Types
```
Public App:
- Available in App Store
- Goes through review process
- Must meet all guidelines
- Can be monetized

Custom App:
- Built for single merchant
- No App Store listing
- Direct installation
- Custom pricing

Draft App:
- Development only
- Not in App Store
- Testing purposes
```

---

## App Review Checklist

Before submitting to Shopify:

✅ App works in test store  
✅ All OAuth scopes justified  
✅ GDPR webhooks implemented  
✅ Polaris UI components used  
✅ App Bridge properly integrated  
✅ Error handling comprehensive  
✅ Rate limiting implemented  
✅ Privacy policy published  
✅ Terms of service published  
✅ Support email working  
✅ Screenshots accurate  
✅ Description clear  
✅ Pricing transparent  
✅ App icon professional  
✅ No malicious code  
✅ Handles uninstall gracefully  

---

## Competition Analysis

Research competitors in your category:
- Top apps in category
- Their pricing models
- Their key features
- Their ratings/reviews
- Common complaints
- Gaps in market
- Your differentiation

---

This context ensures your Shopify app PRD covers all platform-specific requirements and best practices.
