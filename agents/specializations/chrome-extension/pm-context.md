# Chrome Extension - Product Manager Context

## Platform Understanding
You are creating a PRD for a Chrome Extension (also compatible with Edge, Brave, Opera). Chrome extensions add functionality to the browser, interact with web pages, and can access browser APIs.

---

## Key Questions to Ask

### Extension Type & Functionality
- **Extension Category:**
  - Productivity tool?
  - Developer tool?
  - Content blocker/modifier?
  - Shopping assistant?
  - Social media enhancer?
  - Security/privacy tool?
  - Page analyzer?

- **Core Interaction:**
  - Works on all websites?
  - Specific websites only?
  - Works on browser itself (bookmarks, tabs)?
  - Background processing?
  - Real-time or on-demand?

### User Interface
- **UI Components:**
  - Browser action (icon in toolbar)?
  - Page action (icon in address bar)?
  - Popup window?
  - Options page?
  - Side panel (Chrome 114+)?
  - Context menu items?
  - Omnibox commands?

- **Content Interaction:**
  - Injects UI into web pages?
  - Modifies existing page content?
  - Overlays on pages?
  - Notifications?

### Browser Permissions
- **What Access Needed:**
  - Active tab only?
  - All websites?
  - Specific domains?
  - Browsing history?
  - Bookmarks?
  - Cookies?
  - Downloads?
  - Storage?
  - Clipboard?
  - Notifications?

### Data & Storage
- **Data Handling:**
  - Local storage only?
  - Sync across devices?
  - External API calls?
  - Backend server needed?
  - User accounts?
  - How much storage needed?

### Monetization
- **Revenue Model:**
  - Free (open source)?
  - Freemium (upgrade for features)?
  - Subscription?
  - One-time purchase?
  - No monetization?

---

## Chrome Extension Architecture Components

### 1. Manifest File (manifest.json)
The configuration file that defines everything about the extension.

```json
{
  "manifest_version": 3,
  "name": "Extension Name",
  "version": "1.0.0",
  "description": "What your extension does (132 chars max)",
  "permissions": ["storage", "activeTab"],
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"],
      "css": ["content.css"]
    }
  ]
}
```

### 2. Background Service Worker
Handles events, manages state, long-running processes.

**Use for:**
- Listening to browser events
- Making API calls
- Managing extension state
- Scheduling tasks
- Handling messages from content scripts

### 3. Content Scripts
JavaScript that runs in the context of web pages.

**Use for:**
- Reading/modifying page content
- Injecting UI elements
- Listening to page events
- Communicating with background script

### 4. Popup
Small HTML window when clicking extension icon.

**Use for:**
- Quick actions
- Status display
- Settings toggle
- Mini dashboard

### 5. Options Page
Full settings page for the extension.

**Use for:**
- Detailed configuration
- User preferences
- Account management
- Feature toggles

### 6. Side Panel (New in Chrome 114)
Persistent sidebar alongside web pages.

**Use for:**
- Persistent UI
- Chat interfaces
- Note-taking
- Reference panels

---

## Requirements Sections

Your PRD should include these extension-specific sections:

### Permissions Required
```
Essential Permissions:
1. activeTab
   - Purpose: Access current tab when user clicks extension
   - Privacy: Only active tab, only when invoked
   - Warning: None (optional permission)

2. storage
   - Purpose: Save user preferences and data
   - Privacy: Local only, not transmitted
   - Warning: None

3. scripting
   - Purpose: Inject content into pages
   - Privacy: Only on user action
   - Warning: None

Sensitive Permissions (require justification):
1. <all_urls> host permission
   - Purpose: Work on any website
   - Privacy: Can read/modify all pages
   - Warning: "Read and change all your data on all websites"
   - Justification: Required to analyze content across sites

2. cookies
   - Purpose: Manage session data
   - Privacy: Access to site cookies
   - Warning: "Read and modify cookies"
   - Justification: Essential for [specific feature]

3. tabs
   - Purpose: Manage browser tabs
   - Privacy: Can see all open tabs
   - Warning: "Read browsing history"
   - Justification: Required for [specific feature]
```

### Extension Components
```
1. Browser Action (Toolbar Icon)
   - Always visible in toolbar
   - Badge text: Show count/status
   - Badge color: Indicate state (green=active, red=error)
   - Click action: Open popup

2. Popup (320x400px typical)
   - Quick stats dashboard
   - Toggle on/off
   - Access to main features
   - Link to options page
   - Loads in < 100ms

3. Content Script (injected into pages)
   - Runs on: All websites (or specific domains)
   - Purpose: Analyze page content, inject UI
   - Isolated CSS: Yes (no conflicts)
   - Communication: Message passing to background

4. Background Service Worker
   - Handles: Browser events, API calls, state management
   - Persistent: No (wakes on events)
   - Alarms: For scheduled tasks
   - Message hub: Coordinates components

5. Options Page
   - Full-featured settings
   - User preferences
   - Feature configuration
   - Account management (if applicable)
```

### Content Script Injection Strategy
```
When to Inject:
- document_start: Before any page scripts
- document_end: After DOM loaded
- document_idle: After page fully loaded (default)

Where to Inject:
Option A: All URLs
- matches: ["<all_urls>"]
- Use: Extensions that work everywhere
- Warning: High permission request

Option B: Specific Domains
- matches: ["*://*.example.com/*"]
- Use: Site-specific extensions
- Warning: Only those domains

Option C: User-triggered
- No manifest declaration
- Inject via scripting API on demand
- Use: Manual activation
- Warning: None (best for privacy)

CSS Isolation:
- Use Shadow DOM for injected UI
- Prefix all classes
- Avoid global styles
```

### Message Passing Architecture
```
Content Script → Background:
chrome.runtime.sendMessage({
  action: "analyzeContent",
  data: pageData
});

Background → Content Script:
chrome.tabs.sendMessage(tabId, {
  action: "highlight",
  elements: [...]
});

Popup → Background:
chrome.runtime.sendMessage({
  action: "getStats"
});

Background → Popup:
// Response via callback or Promise

Long-lived connections:
const port = chrome.runtime.connect({name: "content"});
port.postMessage({data: "streaming data"});
```

### Storage Strategy
```
chrome.storage.sync (100KB limit, syncs across devices):
- User preferences
- Feature toggles
- Small settings
- API keys (encrypted)

chrome.storage.local (10MB limit, local only):
- Cached data
- User content
- Large datasets
- Temporary data

chrome.storage.session (Chrome 102+, 10MB):
- Current session data
- Temporary flags
- Cleared on browser close

IndexedDB (unlimited with permission):
- Large datasets
- Offline data
- Complex queries
```

---

## User Flows

### Installation & Onboarding
```
1. User finds extension in Chrome Web Store
   ↓
2. Reviews permissions
   ↓
3. Clicks "Add to Chrome"
   ↓
4. Confirms permissions dialog
   ↓
5. Extension installed
   - Icon appears in toolbar
   - Welcome notification (optional)
   ↓
6. First-time setup (if needed)
   - Opens onboarding tab
   - Explains features
   - Collects preferences
   - Grants additional permissions if needed
   ↓
7. Ready to use!
```

### Typical Usage Flow
```
Example: Grammar checker extension

1. User browsing a website
   ↓
2. Content script analyzes page text
   ↓
3. Finds grammar issues
   ↓
4. Injects underline UI
   ↓
5. User clicks underlined word
   ↓
6. Shows correction popup
   ↓
7. User clicks suggestion
   ↓
8. Content script applies fix
   ↓
9. Updates badge count (-1 error)
```

### Popup Interaction
```
1. User clicks extension icon
   ↓
2. Popup opens (320x400px)
   ↓
3. Shows current status
   - Statistics
   - Active features
   - Quick actions
   ↓
4. User toggles feature on/off
   ↓
5. Background script receives message
   ↓
6. Updates state
   ↓
7. Badge updates
   ↓
8. Content scripts notified
   ↓
9. Popup closes (or stays open)
```

---

## Technical Requirements

### Manifest V3 Requirements
```json
{
  "manifest_version": 3,
  "name": "Your Extension Name",
  "version": "1.0.0",
  "description": "Clear description under 132 chars",
  
  "permissions": [
    "storage",
    "activeTab"
  ],
  
  "host_permissions": [
    "*://*.example.com/*"
  ],
  
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "32": "icons/icon32.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  
  "background": {
    "service_worker": "background.js",
    "type": "module"
  },
  
  "content_scripts": [{
    "matches": ["<all_urls>"],
    "js": ["content.js"],
    "run_at": "document_idle"
  }],
  
  "options_page": "options.html",
  
  "icons": {
    "16": "icons/icon16.png",
    "32": "icons/icon32.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  }
}
```

### Content Security Policy
```
Manifest V3 CSP (automatic):
- No inline scripts
- No eval()
- No remote code execution
- External requests allowed

Best Practices:
- Load all scripts locally
- Use event listeners (not inline)
- Sanitize user input
- Use HTTPS for API calls
```

### Performance Requirements
```
Popup:
- Load time: < 100ms
- Bundle size: < 500KB
- No blocking operations
- Lazy load if needed

Content Script:
- Injection time: < 50ms
- No layout thrashing
- Debounce DOM changes
- Minimal memory footprint

Background Service Worker:
- Activate quickly: < 100ms
- Handle events efficiently
- Clean up on idle
- No memory leaks
```

---

## Chrome Web Store Requirements

### Store Listing
```
Required Assets:

1. Extension Icon
   - 128x128 PNG
   - Clear, recognizable
   - Works at small sizes

2. Screenshots
   - 1280x800 or 640x400
   - At least 1, max 5
   - Show actual functionality
   - Annotate features

3. Promotional Tile (optional)
   - 440x280 PNG
   - Used in featured sections
   - Eye-catching design

4. Description
   - Clear value proposition
   - Key features (bullets)
   - How to use
   - Support information
   - Under 132 chars summary

5. Category
   - Choose most relevant
   - Affects discovery

6. Privacy Policy
   - Required if collecting data
   - Must be publicly accessible
   - Clear and honest
```

### Review Guidelines
```
Must Have:
✅ Single, clear purpose
✅ Accurate description
✅ Justified permissions
✅ Privacy policy (if collecting data)
✅ No obfuscated code
✅ No malware/spam
✅ Works as described

Must Not:
❌ Misleading functionality
❌ Cryptocurrency mining
❌ Excessive permissions
❌ Copy other extensions
❌ Keyword stuffing
❌ Adult content
❌ Hate speech
```

---

## Privacy & Security Considerations

### Data Collection
```
If collecting ANY user data:
1. Disclose in privacy policy
2. Explain what and why
3. How it's stored/transmitted
4. How it's protected
5. How users can delete it
6. Third-party sharing

Even local storage counts as "data collection"
```

### Permission Best Practices
```
1. Minimum necessary permissions
   - Start with activeTab
   - Add only when truly needed

2. Request permissions at runtime
   - Use optional_permissions
   - Request when feature is used
   - Explain why before requesting

3. Sensitive permissions require justification
   - <all_urls>: Explain why all sites needed
   - cookies: Explain specific use case
   - tabs: Explain necessity
```

### Security Best Practices
```
1. Content Security Policy
   - No inline scripts
   - No eval()
   - Sanitize user input
   - Use textContent, not innerHTML

2. Message validation
   - Verify message sender
   - Validate message structure
   - Sanitize message content

3. External communications
   - HTTPS only
   - Validate API responses
   - Handle errors gracefully

4. Storage security
   - Encrypt sensitive data
   - Don't store passwords
   - Clear data on uninstall
```

---

## Anti-patterns to Avoid

❌ **Don't:**
- Request more permissions than needed
- Use remote code (Manifest V3 blocks it)
- Collect data without disclosure
- Slow down web pages
- Conflict with page scripts
- Use excessive CPU/memory
- Modify pages without user consent
- Store sensitive data unencrypted
- Make every API call from content script
- Use synchronous storage APIs
- Forget to handle errors

✅ **Do:**
- Minimal permissions
- Bundle all code
- Clear privacy policy
- Optimize performance
- Isolate your CSS/JS
- Efficient resource usage
- User control over features
- Encrypt sensitive data
- Use background script for APIs
- Async storage APIs
- Comprehensive error handling

---

## Testing Checklist

### Before Publishing
✅ Load unpacked in Chrome  
✅ Test all features  
✅ Test on multiple websites  
✅ Check memory usage  
✅ Check CPU usage  
✅ Test popup in different states  
✅ Test with slow network  
✅ Test with DevTools open  
✅ Test storage limits  
✅ Test permissions flow  
✅ Test error handling  
✅ Check console for errors  
✅ Validate manifest.json  
✅ All icons present (16, 32, 48, 128)  
✅ Description accurate  
✅ Privacy policy published  
✅ No unnecessary permissions  

---

## Success Metrics

### Acquisition
- Web Store impressions
- Install rate
- Weekly installs
- Active installations

### Engagement  
- Daily Active Users (DAU)
- Weekly Active Users (WAU)
- Feature usage frequency
- Average session time

### Quality
- User ratings (target: 4.5+)
- Review sentiment
- Uninstall rate
- Support requests

### Performance
- Load time
- Memory usage
- CPU usage
- Error rate

---

## Common Extension Types & Patterns

### Productivity Extensions
- Todo lists
- Note-taking
- Time tracking
- Tab management
- Focus tools

**Pattern:** Side panel + background sync

### Developer Tools
- Code helpers
- API testing
- Page analysis
- Performance monitoring

**Pattern:** DevTools panel + content inspection

### Content Modifiers
- Ad blockers
- Dark mode
- Reader mode
- Translators

**Pattern:** Content scripts + declarative rules

### Shopping Assistants
- Price comparison
- Coupon finders
- Wishlist managers

**Pattern:** Page scanning + popup display

---

This context ensures your Chrome extension PRD covers all platform requirements, best practices, and common patterns.
