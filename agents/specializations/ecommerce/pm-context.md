# E-commerce Site - Product Manager Context

## Platform Understanding
You are creating a PRD for an e-commerce website or online store. E-commerce sites have unique requirements around product catalogs, shopping carts, checkout flows, payments, order management, and customer experience.

---

## Key Questions to Ask

### Business Model
- **Store Type:**
  - B2C (Business to Consumer)?
  - B2B (Business to Business)?
  - Marketplace (multi-vendor)?
  - Dropshipping?
  - Subscription boxes?
  - Digital products?
  - Hybrid?

- **Product Categories:**
  - Physical goods?
  - Digital downloads?
  - Services/bookings?
  - Subscriptions?
  - Mix of above?

### Product Catalog
- **Catalog Size:**
  - Number of products?
  - Number of categories?
  - Product variants (sizes, colors)?
  - Inventory complexity?
  - Seasonal products?

- **Product Attributes:**
  - Simple products?
  - Variable products (with variants)?
  - Bundle/package deals?
  - Customizable products?
  - Made-to-order items?

### Checkout & Payments
- **Payment Methods:**
  - Credit/debit cards?
  - Digital wallets (Apple Pay, Google Pay)?
  - Buy Now Pay Later (Klarna, Afterpay)?
  - PayPal?
  - Bank transfer?
  - Cryptocurrency?
  - Cash on delivery?

- **Checkout Flow:**
  - Guest checkout allowed?
  - Account required?
  - One-page checkout?
  - Multi-step checkout?
  - Express checkout options?

### Shipping & Fulfillment
- **Shipping Options:**
  - Free shipping threshold?
  - Flat rate shipping?
  - Calculated shipping rates?
  - Express/overnight options?
  - International shipping?
  - Store pickup?
  - Local delivery?

- **Fulfillment:**
  - Own warehouse?
  - Third-party fulfillment (3PL)?
  - Dropshipping?
  - Print-on-demand?
  - Multiple warehouses?

### Customer Features
- **Account Features:**
  - User profiles?
  - Order history?
  - Wishlists/favorites?
  - Saved addresses?
  - Saved payment methods?
  - Loyalty program?
  - Referral system?

- **Customer Support:**
  - Live chat?
  - Email support?
  - Phone support?
  - Self-service returns?
  - FAQ/help center?
  - Product Q&A?

### Marketing & Sales
- **Promotions:**
  - Discount codes/coupons?
  - Percentage vs. fixed discounts?
  - Buy X Get Y offers?
  - Free shipping promotions?
  - Flash sales?
  - Abandoned cart recovery?

- **SEO & Marketing:**
  - Product page SEO?
  - Blog/content marketing?
  - Email marketing integration?
  - Social media integration?
  - Affiliate program?
  - Product reviews?

---

## E-commerce-Specific Features to Consider

### 1. Product Catalog System
- **Product Management:**
  - Product creation/editing
  - Bulk upload/import
  - Categories and tags
  - Product search
  - Filtering and sorting
  - Product recommendations

- **Product Variants:**
  - Size, color, material options
  - SKU management
  - Inventory per variant
  - Pricing per variant
  - Images per variant

- **Product Content:**
  - Title and description
  - Multiple images
  - Video content
  - 360° views
  - Size charts
  - Specifications table
  - Related products

### 2. Shopping Experience
- **Product Discovery:**
  - Homepage featured products
  - Category pages
  - Search with autocomplete
  - Filters (price, brand, features)
  - Sorting options
  - Quick view modals

- **Product Pages:**
  - Image gallery with zoom
  - Variant selection
  - Quantity selector
  - Add to cart
  - Add to wishlist
  - Size guide
  - Reviews and ratings
  - Shipping information
  - Return policy

- **Shopping Cart:**
  - Cart sidebar/page
  - Update quantities
  - Remove items
  - Apply coupon codes
  - Save for later
  - Cart persistence
  - Mini cart widget

### 3. Checkout Process
- **Checkout Steps:**
  - Login/guest option
  - Shipping address
  - Shipping method
  - Payment information
  - Order review
  - Order confirmation

- **Checkout Features:**
  - Address autocomplete
  - Saved addresses
  - Real-time shipping calculation
  - Tax calculation
  - Promo code application
  - Gift options
  - Order notes
  - Terms acceptance

- **Payment Processing:**
  - Secure payment gateway
  - PCI compliance
  - 3D Secure support
  - Fraud detection
  - Payment retry logic
  - Failed payment handling

### 4. Order Management
- **Order Processing:**
  - Order creation
  - Payment confirmation
  - Inventory deduction
  - Fulfillment workflow
  - Shipping label generation
  - Tracking number updates
  - Order status notifications

- **Customer Order View:**
  - Order history
  - Order details
  - Track shipment
  - Download invoice
  - Reorder option
  - Request return/exchange

### 5. Inventory Management
- **Stock Tracking:**
  - Real-time inventory
  - Low stock alerts
  - Out of stock handling
  - Backorder support
  - Pre-order capability
  - Multi-location inventory

- **Inventory Rules:**
  - Reserve on order
  - Release on cancel
  - Auto-hide out of stock
  - Show "X left" warnings
  - Wait list for out of stock

### 6. Customer Accounts
- **Profile Features:**
  - Personal information
  - Password management
  - Email preferences
  - Multiple addresses
  - Default address
  - Saved payment methods

- **Account Dashboard:**
  - Order history
  - Track shipments
  - Returns/exchanges
  - Wishlist
  - Loyalty points
  - Store credit
  - Gift cards

---

## Requirements Sections

Your PRD should include these e-commerce-specific sections:

### Product Data Model
```
Product:
- ID (unique identifier)
- Name (title)
- Slug (URL-friendly)
- Description (long text, supports HTML/markdown)
- Short description (summary)
- SKU (Stock Keeping Unit)
- Price (base price)
- Sale price (optional)
- Categories (many-to-many)
- Tags (many-to-many)
- Images (multiple, with alt text)
- Status (draft, published, archived)
- Featured (boolean)
- Inventory tracking (boolean)
- Stock quantity
- Allow backorders (boolean)
- Weight (for shipping)
- Dimensions (L x W x H)
- Metadata (SEO title, description, keywords)
- Created/updated timestamps

Product Variants (if applicable):
- Variant ID
- Product ID (parent)
- Attributes (size: "Large", color: "Blue")
- SKU
- Price override (optional)
- Stock quantity
- Images (optional, otherwise use parent)

Product Categories:
- Category ID
- Name
- Slug
- Description
- Parent category (for hierarchy)
- Image
- SEO metadata
```

### Shopping Cart Architecture
```
Cart Structure:
{
  id: "cart_uuid",
  user_id: "user_uuid" | null (for guest),
  session_id: "session_token" (for guest),
  items: [
    {
      id: "item_uuid",
      product_id: "product_uuid",
      variant_id: "variant_uuid" | null,
      quantity: 2,
      price: 29.99, (locked at add time)
      name: "Product Name",
      image: "url",
      attributes: { size: "M", color: "Blue" }
    }
  ],
  subtotal: 59.98,
  shipping: 0.00,
  tax: 5.40,
  discount: -10.00,
  total: 55.38,
  coupon_code: "SAVE10",
  created_at: "2025-01-14T...",
  updated_at: "2025-01-14T...",
  expires_at: "2025-01-21T..." (7 days)
}

Cart Rules:
- Guest carts expire after 7 days
- Logged-in carts persist indefinitely
- Merge guest cart on login
- Update prices on checkout (not in cart)
- Reserve inventory on checkout start
- Release inventory if payment fails
```

### Checkout Flow Specification
```
Step 1: Cart Review
- Display cart items
- Allow quantity changes
- Allow item removal
- Show subtotal
- "Proceed to Checkout" button

Step 2: Login/Guest Selection
- If logged in → Skip to Step 3
- If not logged in:
  - "Login" option
  - "Checkout as Guest" option
  - "Create Account" option
- Social login options (Google, Apple)

Step 3: Shipping Information
- If logged in:
  - Show saved addresses
  - "Use this address" option
  - "Add new address" option
- If guest:
  - Shipping form:
    * Email (required)
    * First name, last name
    * Address line 1, 2
    * City, State/Province
    * ZIP/Postal code
    * Country
    * Phone number
- Address validation
- "Same as billing" checkbox

Step 4: Shipping Method
- Fetch available methods from shipping API
- Display options:
  * Standard (5-7 days) - $5.99
  * Express (2-3 days) - $12.99
  * Overnight (1 day) - $24.99
  * Free shipping (orders over $50)
- Default: Cheapest option
- Show estimated delivery date

Step 5: Payment Information
- If logged in: Show saved cards
- Payment form:
  * Card number
  * Expiry (MM/YY)
  * CVV
  * Cardholder name
  * Billing address (if different)
- Alternative payment buttons:
  * Apple Pay
  * Google Pay
  * PayPal
- "Save card" checkbox (for logged-in users)
- Security badges (SSL, PCI compliant)

Step 6: Order Review
- Summary of:
  * Items
  * Shipping address
  * Shipping method
  * Payment method (last 4 digits)
- Cost breakdown:
  * Subtotal
  * Shipping
  * Tax
  * Discount (if applied)
  * Total
- Promo code input (if not applied earlier)
- Terms & conditions checkbox
- "Place Order" button
- Show processing indicator on submit

Step 7: Order Confirmation
- "Order placed successfully!" message
- Order number prominently displayed
- Confirmation email sent
- Order summary
- "Continue Shopping" button
- "View Order Details" button
- Tracking information (when available)
```

### Payment Integration
```
Payment Provider: Stripe

Checkout Session:
1. Create Stripe Payment Intent
   - Amount: Order total
   - Currency: USD
   - Customer ID (if logged in)
   - Metadata: Order ID, items

2. Collect Payment Details
   - Use Stripe Elements (PCI compliant)
   - Handle 3D Secure if required
   - Show real-time validation

3. Confirm Payment
   - Call Stripe confirm API
   - Handle authentication if needed
   - Show loading state

4. Handle Result
   - Success: Create order, send confirmation
   - Failure: Show error, allow retry
   - Fraud: Hold order for review

Webhook Handling:
- payment_intent.succeeded
- payment_intent.failed  
- charge.refunded
- Update order status accordingly

Refunds:
- Full refunds: Return all items
- Partial refunds: Return some items
- Process through Stripe API
- Update order status
- Notify customer
```

### Shipping Integration
```
Shipping Provider: ShipStation, EasyPost, or custom

Real-time Rate Calculation:
1. Collect package details
   - Total weight from products
   - Dimensions (if needed)
   - Origin address (warehouse)
   - Destination address (customer)

2. Request rates from carriers
   - USPS, UPS, FedEx
   - Return available services
   - Show prices and delivery estimates

3. Display to customer
   - Sort by price or delivery time
   - Add handling fee if needed
   - Apply free shipping rules

Label Generation:
1. After order confirmed
   - Send package details to provider
   - Specify selected carrier/service
   - Get shipping label PDF

2. Store tracking number
   - Update order record
   - Trigger tracking email to customer

3. Track shipment
   - Poll carrier API for updates
   - Update order status
   - Notify customer of delivery
```

### Tax Calculation
```
Tax Strategy:

Option A: Simple (single jurisdiction):
- Fixed tax rate: 8.5%
- Applied to subtotal
- Added at checkout

Option B: US Multi-state (Stripe Tax):
- Automatic nexus determination
- State, county, city tax rates
- Tax collected based on shipping address
- Compliant with economic nexus laws

Option C: International (Avalara, TaxJar):
- Support VAT, GST
- Cross-border tax rules
- Tax registration management
- Filing and remittance

Implementation:
- Calculate tax in real-time at checkout
- Store tax amount with order
- Generate tax reports
- Handle tax-exempt customers (B2B)
```

---

## User Flows

### Product Discovery to Purchase
```
1. Customer lands on homepage
   ↓
2. Browses categories OR uses search
   ↓
3. Views category/search results
   - Grid of products
   - Filters (price, brand, size)
   - Sorting options
   ↓
4. Clicks on product
   ↓
5. Views product page
   - Reads description
   - Views images
   - Checks reviews
   - Reads shipping info
   ↓
6. Selects variant (if applicable)
   - Choose size
   - Choose color
   ↓
7. Clicks "Add to Cart"
   - Success message
   - Mini cart updates
   - Option: Continue shopping or Checkout
   ↓
8. Continues shopping (optional)
   - Adds more items
   ↓
9. Clicks "Checkout"
   ↓
10. Completes checkout flow
    (See Checkout Flow Specification above)
   ↓
11. Order confirmed!
   - Confirmation page
   - Email sent
   - Can track order
```

### Abandoned Cart Recovery
```
Trigger: User adds items but doesn't complete purchase

Timeline:
- 1 hour: No action (too soon)
- 6 hours: Send first email
  - Subject: "You left something in your cart"
  - Content: Show cart items
  - CTA: "Complete your purchase"
  - Incentive: 10% discount code

- 24 hours: Send second email (if still abandoned)
  - Subject: "Still interested?"
  - Content: Remind of items
  - CTA: "Checkout now"
  - Incentive: Free shipping

- 72 hours: Send final email
  - Subject: "Last chance - your cart expires soon"
  - Content: Urgency message
  - CTA: "Shop now"
  - Incentive: 15% discount (max offer)

- 7 days: Cart expires
  - Items removed
  - No further emails
```

---

## Performance Requirements

### Page Load Times
```
Homepage: < 2 seconds
Category pages: < 2 seconds
Product pages: < 2.5 seconds
Search results: < 1.5 seconds
Cart: < 1 second
Checkout: < 2 seconds

Optimization strategies:
- CDN for images and static assets
- Lazy loading for below-fold images
- Minimize JavaScript bundle
- Server-side rendering for SEO
- Database query optimization
- Redis caching for product data
```

### Availability
```
Uptime: 99.9% (8.7 hours downtime/year)
Maintenance windows: Off-peak hours
Failover: Automatic
Monitoring: Real-time alerts
```

### Scalability
```
Target traffic:
- Normal: 10,000 visitors/day
- Peak: 50,000 visitors/day (holidays)
- Concurrent users: 500

Database:
- PostgreSQL with read replicas
- Connection pooling
- Query caching

Static assets:
- CDN (Cloudflare, CloudFront)
- Global distribution
- Auto-scaling

Application:
- Serverless functions for checkout
- Auto-scaling web servers
- Load balancing
```

---

## SEO Requirements

### Product Pages
```
URL Structure:
- /products/{category}/{slug}
- Example: /products/electronics/wireless-headphones

Meta Tags:
- Title: "{Product Name} | {Store Name}"
- Description: "{Short description with key features}"
- Keywords: "{Main keywords}"
- OG tags for social sharing
- Product schema markup (JSON-LD)

Content:
- Unique product descriptions
- H1 for product name
- Bullet points for features
- Alt text for all images
- Customer reviews (user-generated content)
```

### Category Pages
```
URL Structure:
- /category/{slug}
- Example: /category/electronics

Content:
- Category description (unique, SEO-optimized)
- H1 for category name
- Internal linking to subcategories
- Breadcrumbs
- Pagination with rel=next/prev
- Filters in URL parameters
```

---

## Anti-patterns to Avoid

❌ **Don't:**
- Force account creation to checkout
- Hide shipping costs until final step
- Make checkout too many steps
- Require phone number unnecessarily
- Auto-apply maximum discount (reduces margin)
- Show out-of-stock as available
- Complicated return policy
- No guest checkout option
- Poor mobile experience
- Surprise fees at checkout
- No product reviews
- Slow image loading

✅ **Do:**
- Allow guest checkout
- Show all costs upfront
- Keep checkout simple (3-5 steps max)
- Only ask for essential information
- Smart discount strategy
- Clear stock status
- Simple, customer-friendly returns
- Support multiple payment methods
- Mobile-first design
- Transparent pricing
- Enable product reviews
- Optimize images (WebP, lazy loading)

---

## Success Metrics

### Sales Metrics
- Conversion rate (visitors → purchases)
- Average order value (AOV)
- Revenue per visitor (RPV)
- Cart abandonment rate
- Checkout completion rate

### Customer Metrics
- Customer acquisition cost (CAC)
- Customer lifetime value (LTV)
- Repeat purchase rate
- Customer retention rate
- Net Promoter Score (NPS)

### Product Metrics
- Best-selling products
- Product view to add-to-cart rate
- Add-to-cart to purchase rate
- Product return rate
- Average products per order

### Marketing Metrics
- Email open rate
- Email click-through rate
- Abandoned cart recovery rate
- Coupon usage rate
- Referral conversion rate

---

This context ensures your e-commerce site PRD covers all the unique requirements for online retail success.
