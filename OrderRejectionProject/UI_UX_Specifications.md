# Order Rejection System - UI/UX Specifications

**Version:** 1.0
**Date:** 2025-10-10
**Purpose:** User interface design specifications and mockups
**Parent Document:** Order_Rejection_System_Design.md

---

## Mobile App Interface (React Native)

### Screen 1: No Order Button

```
┌─────────────────────────────────────────┐
│  < Back        Retailer Visit      Help │
├─────────────────────────────────────────┤
│                                          │
│  Retailer: Sharma Electronics           │
│  📍 Andheri West, Mumbai                │
│  🕒 11:23 AM                             │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │                                     │ │
│  │  [📦 Take Order]                   │ │
│  │                                     │ │
│  │  Large Green Button                 │ │
│  │                                     │ │
│  └────────────────────────────────────┘ │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │                                     │ │
│  │  [❌ No Order Given]               │ │
│  │                                     │ │
│  │  Tappable to log rejection          │ │
│  │                                     │ │
│  └────────────────────────────────────┘ │
│                                          │
│  Recent Activity:                        │
│  • 3 days ago: Ordered 5 units         │
│  • 7 days ago: Ordered 8 units         │
│                                          │
└─────────────────────────────────────────┘
```

---

### Screen 2: Voice/Text Input Choice

```
┌─────────────────────────────────────────┐
│  < Cancel      Log Rejection       Skip │
├─────────────────────────────────────────┤
│                                          │
│  Why didn't Sharma Electronics order?   │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │  🎤 Speak Reason                   │ │
│  │  (Tap and hold to record)          │ │
│  │                                     │ │
│  │  🔴 Recording... 0:05               │ │
│  └────────────────────────────────────┘ │
│                                          │
│            ──── OR ────                  │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │  💬 Type Reason                    │ │
│  │  ┌──────────────────────────────┐ │ │
│  │  │ Payment pending...           │ │ │
│  │  └──────────────────────────────┘ │ │
│  └────────────────────────────────────┘ │
│                                          │
│  💡 Tip: Speak naturally in English,    │
│     Hindi, or Hinglish                  │
│                                          │
│  [Continue] ───────────────────────────  │
│                                          │
└─────────────────────────────────────────┘
```

---

### Screen 3: Classification Result (High Confidence)

```
┌─────────────────────────────────────────┐
│  < Back       Confirm Reason        Next│
├─────────────────────────────────────────┤
│                                          │
│  ✓ We identified the reason:            │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │  💰 Outstanding Payment/            │ │
│  │     Credit Issues                   │ │
│  │                                     │ │
│  │  Category: Financial                │ │
│  │  Confidence: 89%                    │ │
│  └────────────────────────────────────┘ │
│                                          │
│  Your input:                            │
│  "Retailer ka previous payment clear   │
│   nahi hua"                             │
│                                          │
│  Is this correct?                       │
│                                          │
│  ┌────────────────┐  ┌────────────────┐ │
│  │ ✅ Yes, Correct│  │ ↻ Change       │ │
│  └────────────────┘  └────────────────┘ │
│                                          │
│  [Continue] ───────────────────────────  │
│                                          │
└─────────────────────────────────────────┘
```

---

### Screen 4: Template Selection

```
┌─────────────────────────────────────────┐
│  < Back     Select Specific Reason      │
├─────────────────────────────────────────┤
│                                          │
│  💰 Outstanding Payment/Credit Issues   │
│                                          │
│  Select the most accurate option:       │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │ ○ Previous order payment pending   │ │
│  │   (₹___ outstanding since ___ days)│ │
│  └────────────────────────────────────┘ │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │ ⦿ Credit limit exhausted with      │ │
│  │   distributor                       │ │
│  └────────────────────────────────────┘ │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │ ○ Cash flow issue - will order     │ │
│  │   after receiving payment from      │ │
│  │   customers                         │ │
│  └────────────────────────────────────┘ │
│                                          │
│  [Next] ───────────────────────────────  │
│                                          │
└─────────────────────────────────────────┘
```

---

### Screen 5: Additional Details (Optional)

```
┌─────────────────────────────────────────┐
│  < Back      Additional Details    Submit│
├─────────────────────────────────────────┤
│                                          │
│  Any additional information?             │
│  (Optional)                              │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │ Expected to clear by Oct 15        │ │
│  │ ₹25,000 outstanding                │ │
│  │                                     │ │
│  │                                     │ │
│  │                                     │ │
│  └────────────────────────────────────┘ │
│  0 / 500 characters                     │
│                                          │
│  💡 Helpful details:                     │
│  • Amount outstanding                   │
│  • When they expect to pay              │
│  • Any specific products affected       │
│                                          │
│  ┌────────────────┐  ┌────────────────┐ │
│  │ Skip           │  │ Submit         │ │
│  └────────────────┘  └────────────────┘ │
│                                          │
└─────────────────────────────────────────┘
```

---

### Screen 6: Confirmation

```
┌─────────────────────────────────────────┐
│         Rejection Logged Successfully    │
├─────────────────────────────────────────┤
│                                          │
│            ✓                            │
│      [Checkmark Animation]              │
│                                          │
│  Sharma Electronics                      │
│  Andheri West, Mumbai                   │
│                                          │
│  Reason: Outstanding Payment/Credit     │
│  Details: Credit limit exhausted        │
│                                          │
│  ─────────────────────────────────────  │
│                                          │
│  Next Steps:                             │
│  • Follow up when credit available      │
│  • Coordinate with distributor          │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │  Go to Next Retailer               │ │
│  └────────────────────────────────────┘ │
│                                          │
│  [View Today's Summary] (link)          │
│                                          │
└─────────────────────────────────────────┘

Auto-dismiss in 3 seconds or tap anywhere
```

---

## WhatsApp Bot Interface

```
WhatsApp Chat with "Sales Buddy Bot"

─────────────────────────────────────────
You:
No order from Sharma Electronics

─────────────────────────────────────────
Sales Buddy Bot:                 11:23 AM
Got it! Why didn't they order?

Send me a voice note or type the reason 🎤

─────────────────────────────────────────
You:                            11:23 AM
[Voice Note: 0:08] 🔊
"Payment pending hai uska"

─────────────────────────────────────────
Sales Buddy Bot:                 11:23 AM
Thanks! I understood:

💰 *Outstanding Payment/Credit Issues*

Is this correct?
Reply: YES or CHANGE

─────────────────────────────────────────
You:                            11:24 AM
Yes

─────────────────────────────────────────
Sales Buddy Bot:                 11:24 AM
Great! Pick the specific reason:

1️⃣ Previous order payment pending
2️⃣ Credit limit exhausted
3️⃣ Cash flow issue

Reply with the number (1, 2, or 3)

─────────────────────────────────────────
You:                            11:24 AM
2

─────────────────────────────────────────
Sales Buddy Bot:                 11:24 AM
✅ Logged!

*Sharma Electronics* - Credit limit exhausted

Want to add any details?
(Or type SKIP)

─────────────────────────────────────────
You:                            11:24 AM
Expected payment by Oct 15

─────────────────────────────────────────
Sales Buddy Bot:                 11:24 AM
Perfect! All done ✓

Rejection recorded for Sharma Electronics

Type NEXT when you're ready for the next retailer
```

---

## Design System

### Color Palette

**Primary Colors:**
- Green (#4CAF50): Success actions, "Take Order" button
- Red (#F44336): Rejection actions, "No Order" button
- Blue (#2196F3): Informational elements, links
- Yellow (#FFC107): Warnings, clarifications needed

**Neutral Colors:**
- Dark Gray (#212121): Primary text
- Medium Gray (#757575): Secondary text
- Light Gray (#F5F5F5): Backgrounds
- White (#FFFFFF): Cards, buttons

### Typography

**Font Family:** Roboto (Android), SF Pro (iOS)

**Font Sizes:**
- H1 (Page Title): 24px, Bold
- H2 (Section Header): 20px, Semi-Bold
- Body Text: 16px, Regular
- Caption: 14px, Regular
- Button Text: 16px, Medium

### Component Library

**Buttons:**
- Primary Button: Green background, white text, 48px height
- Secondary Button: White background, gray border, 48px height
- Icon Button: Circular, 56px diameter

**Input Fields:**
- Text Field: White background, gray border, 56px height
- Voice Input: Tap-and-hold button with recording indicator
- Radio Buttons: 24px diameter, green when selected

**Cards:**
- Elevation: 2dp shadow
- Border Radius: 8px
- Padding: 16px

### Interaction Patterns

**Voice Recording:**
- Tap and hold to record
- Visual indicator: pulsing red circle
- Audio waveform display during recording
- Maximum duration: 20 seconds

**Loading States:**
- Spinner for AI classification (1-2 seconds)
- Skeleton screens for data loading
- Progress indicators for multi-step flows

**Error States:**
- Inline error messages below input fields
- Toast notifications for system errors
- Retry buttons for failed operations

---

## Accessibility Guidelines

### WCAG 2.1 AA Compliance

**Color Contrast:**
- Text: 4.5:1 minimum contrast ratio
- Large text: 3:1 minimum contrast ratio
- Interactive elements: 3:1 minimum contrast ratio

**Touch Targets:**
- Minimum size: 48x48dp
- Spacing: 8dp minimum between targets

**Screen Reader Support:**
- All buttons labeled with descriptive text
- Form fields have associated labels
- Status messages announced to screen readers

**Keyboard Navigation:**
- Tab order follows visual flow
- Focus indicators visible on all interactive elements
- Escape key dismisses modals/overlays

### Multilingual Support

**Languages:** English, Hindi, Hinglish

**Text Direction:** Left-to-right (LTR)

**Dynamic Text:**
- Allow for 30% text expansion (English → Hindi)
- Use flexible layouts that adapt to text length
- Test all UI strings in all supported languages

---

**Document End**

For questions or feedback, contact: design-team@company.com
