# Mobile App - Product Manager Context

## Platform Understanding
You are creating a PRD for a mobile application (iOS and/or Android). Mobile apps have unique constraints around performance, offline functionality, app store requirements, and platform-specific patterns.

---

## Key Questions to Ask

### Platform Strategy
- **Target Platforms:**
  - iOS only?
  - Android only?
  - Both platforms (cross-platform)?
  - Tablet support?
  
- **Development Approach:**
  - Native (Swift/Kotlin)?
  - Cross-platform (React Native, Flutter)?
  - Hybrid (web view)?

### Core Functionality
- **Primary Use Case:**
  - What problem does the app solve?
  - Why mobile over web?
  - What's the core user flow?
  - Expected session length?

- **Platform Features Needed:**
  - Camera/photos?
  - Location services?
  - Push notifications?
  - Biometric auth (Face ID, Touch ID)?
  - Contacts access?
  - Calendar integration?
  - Background processing?
  - Offline functionality?

### User Experience
- **Navigation Pattern:**
  - Tab-based navigation?
  - Stack navigation?
  - Drawer navigation?
  - Modal flows?

- **Onboarding:**
  - Permission requests needed?
  - Tutorial/walkthrough?
  - Sign-up flow?
  - Social login?

### Data & Sync
- **Data Storage:**
  - Local storage only?
  - Cloud sync required?
  - Offline-first or online-first?
  - Real-time updates needed?
  - How much local storage?

- **Backend Requirements:**
  - User accounts?
  - API integration?
  - File uploads?
  - Real-time features?
  - Analytics tracking?

### Monetization
- **Revenue Model:**
  - Free with ads?
  - Paid upfront?
  - Freemium with IAP?
  - Subscription?
  - No monetization?

- **In-App Purchases (if applicable):**
  - Consumable items?
  - Non-consumable features?
  - Auto-renewing subscriptions?
  - Restore purchases flow?

### App Store Requirements
- **Launch Preparation:**
  - App name (unique)?
  - Bundle ID / Package name?
  - App icon (all sizes)?
  - Screenshots (all devices)?
  - Privacy policy required?
  - Age rating considerations?
  - Target countries/regions?

---

## Mobile-Specific Features to Consider

### 1. Native Device Features
- **Camera & Media:**
  - Take photos/videos
  - Choose from library
  - Edit media
  - AR features (optional)

- **Location Services:**
  - Current location
  - Background location tracking
  - Geofencing
  - Maps integration

- **Biometric Authentication:**
  - Face ID (iOS)
  - Touch ID (iOS)
  - Biometric prompt (Android)
  - Fallback to PIN/password

- **Push Notifications:**
  - Local notifications
  - Remote push notifications
  - Rich notifications
  - Notification actions
  - Badge management

- **Device Integration:**
  - Contacts
  - Calendar
  - Files/documents
  - Health data (iOS HealthKit, Android Health Connect)
  - Bluetooth
  - NFC

### 2. Offline Functionality
- **Data Persistence:**
  - What works offline?
  - Sync strategy
  - Conflict resolution
  - Queue offline actions

- **Offline UI:**
  - Cached content
  - Offline indicators
  - Sync status
  - Error handling

### 3. Performance Requirements
- **Launch Time:**
  - Target: < 2 seconds cold start
  - Splash screen strategy
  - Initial load optimization

- **Memory Usage:**
  - Background memory limits
  - Image caching strategy
  - Data cleanup policies

- **Battery Efficiency:**
  - Background processing limits
  - Location tracking optimization
  - Network call batching

### 4. Platform-Specific Patterns
- **iOS:**
  - Bottom tab bar
  - Navigation bar
  - Action sheets
  - Swipe gestures
  - Pull to refresh
  - 3D Touch (older devices)
  - Haptic feedback

- **Android:**
  - Bottom navigation
  - Floating action button
  - Navigation drawer
  - Material Design patterns
  - Back button behavior
  - Deep linking

---

## Requirements Sections

Your PRD should include these mobile-specific sections:

### Platform Support Matrix
```
iOS:
- Minimum: iOS 14.0
- Target: iOS 17.0+
- Devices: iPhone, iPad
- Orientations: Portrait (primary), Landscape (optional)

Android:
- Minimum: Android 8.0 (API 26)
- Target: Android 14 (API 34)
- Devices: Phones, Tablets
- Orientations: Portrait (primary), Landscape (optional)
```

### Permission Requirements
```
Required Permissions:
1. Camera (for profile photos, document scanning)
   - iOS: NSCameraUsageDescription
   - Android: android.permission.CAMERA
   - Justification: "Take photos for your profile and documents"
   - When requested: On first photo action

2. Photo Library (read/write)
   - iOS: NSPhotoLibraryUsageDescription
   - Android: READ_EXTERNAL_STORAGE
   - Justification: "Choose photos from your library"
   - When requested: On first photo selection

3. Notifications
   - iOS: Push notification prompt
   - Android: POST_NOTIFICATIONS (Android 13+)
   - Justification: "Stay updated with important alerts"
   - When requested: After signup, or in settings

4. Location (if needed)
   - iOS: NSLocationWhenInUseUsageDescription
   - Android: ACCESS_FINE_LOCATION
   - Justification: "Find nearby locations"
   - When requested: When feature is used

Optional Permissions:
- Contacts: For inviting friends
- Calendar: For adding events
- Biometrics: For quick login
```

### Navigation Structure
```
Root: Tab Bar
├── Home Tab
│   ├── Home Screen
│   ├── Detail Screen
│   └── Edit Screen (modal)
│
├── Search Tab
│   ├── Search Screen
│   └── Results Screen
│
├── Create Tab (modal)
│   └── Create Flow (3 steps)
│
├── Notifications Tab
│   ├── Notifications List
│   └── Notification Detail
│
└── Profile Tab
    ├── Profile Screen
    ├── Settings Screen
    └── Edit Profile (modal)

Onboarding Flow (first launch):
├── Welcome Screen
├── Permissions Primer
├── Sign Up / Sign In
└── Tutorial (optional)
```

### Offline Functionality
```
Works Offline:
- View previously loaded content
- Create new items (synced when online)
- Edit existing items (synced when online)
- Search local content
- View cached media

Requires Online:
- Initial data load
- Real-time updates
- User authentication
- Media uploads
- Social features

Sync Strategy:
- Auto-sync when app opens
- Auto-sync when network returns
- Manual refresh option
- Conflict resolution: Last write wins
- Sync queue for offline actions
```

### Push Notification Strategy
```
Notification Types:

1. Transactional (high priority):
   - Payment confirmations
   - Security alerts
   - Important updates
   - Delivery: Immediate
   - Sound: Yes
   - Badge: Yes

2. Engagement (normal priority):
   - New content available
   - Friend activity
   - Recommendations
   - Delivery: Batched (max 1/hour)
   - Sound: Optional
   - Badge: Yes

3. Marketing (low priority):
   - Promotional offers
   - Feature announcements
   - Delivery: Time-optimized
   - Sound: No
   - Badge: Optional

User Controls:
- Enable/disable by type
- Quiet hours setting
- Frequency preferences
```

### App Store Optimization (ASO)
```
iOS App Store:
- App Name: "AppName - Short Description" (30 chars)
- Subtitle: Key value proposition (30 chars)
- Keywords: Comma-separated, 100 chars
- Description: Feature-focused, scannable
- Screenshots: 6.7" iPhone, 12.9" iPad
- Preview Video: 30 seconds, show value

Google Play Store:
- Title: "AppName: Short Description" (50 chars)
- Short Description: 80 chars
- Full Description: 4,000 chars
- Screenshots: Multiple form factors
- Feature Graphic: 1024x500
- Promo Video: YouTube link
```

### In-App Purchase Configuration (if applicable)
```
Product Types:

Subscription (Auto-renewable):
- ID: com.yourapp.pro.monthly
- Name: Pro Monthly
- Price: $9.99/month
- Trial: 7 days free
- Features: Unlimited access, no ads, priority support
- Renewal: Auto-renews monthly
- Cancellation: Anytime, access until period ends

Non-Consumable:
- ID: com.yourapp.premium.lifetime
- Name: Lifetime Premium
- Price: $49.99 one-time
- Features: All pro features forever
- Restore: Yes, across devices

Consumable:
- ID: com.yourapp.coins.100
- Name: 100 Coins
- Price: $0.99
- Use: Purchase in-app items
- Restore: No (consumed on use)

Subscription Groups:
- Basic: $4.99/month
- Pro: $9.99/month (most popular)
- Premium: $19.99/month
```

### Performance Targets
```
Launch Time:
- Cold start: < 2 seconds
- Warm start: < 1 second
- Hot start: < 0.5 seconds

Screen Transitions:
- Navigation: < 100ms
- Data loading: < 500ms with skeleton
- Animation: 60 fps minimum

Memory Usage:
- Foreground: < 200MB typical
- Background: < 50MB
- Memory warnings: Graceful cleanup

Battery Impact:
- Minimal drain in background
- Efficient location tracking
- Batch network requests

Network:
- API response: < 1 second
- Retry on failure: 3 attempts
- Timeout: 30 seconds
- Offline queue: Yes
```

---

## User Flows

### First-Time User Experience (FTUE)
```
1. App Launch
   ↓
2. Splash Screen (< 2 seconds)
   ↓
3. Welcome Screen
   - Value proposition
   - "Get Started" CTA
   ↓
4. Permission Primer
   - Explain why permissions needed
   - "Continue" button
   ↓
5. Request Permissions (one at a time)
   - Camera (if needed immediately)
   - Notifications (defer if possible)
   ↓
6. Account Creation
   - Email + Password
   - OR Social login (Google, Apple)
   - Terms acceptance
   ↓
7. Profile Setup (optional)
   - Name, photo
   - Skip option
   ↓
8. Tutorial / Onboarding (optional)
   - Show key features
   - Interactive if possible
   - Skip option
   ↓
9. Main App Experience
```

### Core User Flow Example
```
Task: Create and Share a Post

1. User on Home Feed
   ↓
2. Tap "+" button (tab bar or FAB)
   ↓
3. Create Screen (modal)
   - Text input
   - Add photo button
   - Add location button
   ↓
4. Tap "Add Photo"
   ↓
5. Photo Source Sheet
   - Take Photo
   - Choose from Library
   - Cancel
   ↓
6. [If Camera] Request camera permission
   ↓
7. Camera View
   - Take photo
   - Review & confirm
   ↓
8. Back to Create Screen
   - Photo attached
   - Complete text
   ↓
9. Tap "Post"
   ↓
10. Loading state (1-2 seconds)
    ↓
11. Success!
    - Dismiss modal
    - Show post in feed
    - Optional: Success toast
    ↓
12. Back to Home Feed
    - New post at top
```

---

## Platform-Specific Considerations

### iOS Guidelines
- Follow Human Interface Guidelines
- Use system fonts (San Francisco)
- Use system colors and blur effects
- Support Dynamic Type (text sizing)
- Support Dark Mode
- Handle safe areas (notch, home indicator)
- Use appropriate haptics
- Support handoff / continuity (if multi-device)
- TestFlight for beta testing

### Android Guidelines
- Follow Material Design 3
- Use Roboto font
- Follow color system
- Support different screen sizes
- Support different densities
- Handle system back button
- Use proper notification channels
- Support Android Auto/Wear (if relevant)
- Google Play beta track

---

## Technical Requirements

### Backend API
```
Authentication:
- JWT token-based
- Refresh token mechanism
- Social login support

Endpoints Needed:
- POST /auth/register
- POST /auth/login
- POST /auth/refresh
- GET /user/profile
- PATCH /user/profile
- GET /content/feed
- POST /content/create
- etc.

Real-time (if needed):
- WebSocket connection
- Fallback to polling
- Reconnection logic
```

### Local Storage
```
User Defaults / SharedPreferences:
- User preferences
- Settings
- Small config data
- < 1MB total

SQLite / Realm:
- Cached data
- Offline queue
- User content
- < 100MB typical

File System:
- Images/media
- Documents
- Logs
- Cleanup policy: 30 days
```

### Analytics & Monitoring
```
Events to Track:
- App launches
- Screen views
- Button taps
- Feature usage
- Error events
- Performance metrics

Crash Reporting:
- Firebase Crashlytics
- Sentry (alternative)
- Automatic uploads
- User context attached

Performance Monitoring:
- Screen load times
- API response times
- Startup time
- Memory usage
```

---

## Anti-patterns to Avoid

❌ **Don't:**
- Request all permissions upfront
- Use auto-playing video/audio
- Use non-native UI patterns
- Ignore platform guidelines
- Use web views for everything
- Drain battery with background tasks
- Store sensitive data unencrypted
- Show desktop website patterns
- Use tiny touch targets (< 44pt)
- Ignore accessibility
- Make users wait without feedback

✅ **Do:**
- Request permissions contextually
- Follow platform conventions
- Use native components
- Optimize for mobile data
- Support offline gracefully
- Handle interruptions (calls, etc.)
- Encrypt sensitive data
- Mobile-first design
- Large, tappable areas (44pt min)
- Full accessibility support
- Show loading states

---

## App Store Submission Checklist

### iOS App Store
✅ App Icon (all required sizes)  
✅ Screenshots (iPhone 6.7", iPad 12.9")  
✅ App Preview video (optional)  
✅ Description and keywords  
✅ Privacy policy URL  
✅ Support URL  
✅ App Store Connect account  
✅ Certificates and provisioning profiles  
✅ Test on real device  
✅ No crashes, bugs  
✅ Follows App Store Review Guidelines  
✅ Privacy manifest (iOS 17+)  

### Google Play Store
✅ App Icon (512x512 PNG)  
✅ Feature graphic (1024x500)  
✅ Screenshots (multiple sizes)  
✅ Short & full description  
✅ Privacy policy URL  
✅ Content rating questionnaire  
✅ Signed APK/AAB  
✅ Test on multiple devices  
✅ No crashes, works offline  
✅ Follows Play Store policies  
✅ Data safety section completed  

---

## Success Metrics

### Acquisition
- App store impressions
- App store conversion rate
- Daily installs
- Cost per install (CPI)
- Install source attribution

### Activation
- Registration completion rate
- Tutorial completion rate
- Time to first action
- Day 1 retention

### Engagement
- Daily Active Users (DAU)
- Monthly Active Users (MAU)
- DAU/MAU ratio
- Session length
- Sessions per user
- Feature adoption rate

### Retention
- Day 1, 7, 30 retention
- Churn rate
- Cohort analysis
- Uninstall rate

### Revenue (if monetized)
- In-app purchase conversion
- Average revenue per user (ARPU)
- Lifetime value (LTV)
- Ad revenue (if applicable)

---

This context ensures your mobile app PRD covers all platform-specific requirements and best practices.
