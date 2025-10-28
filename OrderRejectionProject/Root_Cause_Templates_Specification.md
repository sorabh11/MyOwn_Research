# Root Cause Templates - Implementation Specification

**Version:** 1.0
**Date:** 2025-10-23
**Purpose:** Root cause template wording and UI/UX flow for order rejection system
**Context:** Post-bucket confirmation layer - sales rep selects specific root cause

---

## Overview

After the AI classifies and confirms the rejection bucket, the sales rep must select 1 of 3 root causes that explain WHY that specific issue exists. This document provides:

1. **Exact template wording** for each root cause option (Top 4 Buckets)
2. **UI/UX flow** specifications for mobile implementation

**Design Principles:**
- ✓ **Simple language** - No jargon, max 10 words per option
- ✓ **Quick scanning** - Rep selects in 3-5 seconds
- ✓ **Terminal explanations** - No circular references to other buckets
- ✓ **Actionable** - Sales manager knows what action to take
- ✓ **Total time** - 10-15 seconds including optional details

---

## BUCKET 1: Outstanding Payment/Credit Issues

### Context Question (shown to rep):
**"Why is the payment/credit stuck?"**

### Root Cause Options (Select 1):

#### ○ Option 1: Credit limit or payment terms issue with distributor
**What this means:** Distributor won't give more credit, or payment terms are too strict
**Smart prompt for details:** "Which distributor? How much credit needed?"
**Action insight:** Negotiate credit terms, distributor relationship management

#### ○ Option 2: Retailer waiting for customer payments to come in
**What this means:** Retailer has cash flow timing issue - will pay when customers pay him
**Smart prompt for details:** "When does he expect payment? (date/festival/month-end)"
**Action insight:** Align delivery/payment schedule with retailer's cash cycle

#### ○ Option 3: Previous payment dispute or trust issue
**What this means:** Something went wrong before (billing error, quality issue, wrong product)
**Smart prompt for details:** "What happened? (billing issue, quality claim, product return)"
**Action insight:** Dispute resolution, relationship repair

#### ○ Option 4: Other reason
**Requires:** Free text explanation

---

## BUCKET 2: Slow Moving Inventory

### Context Question (shown to rep):
**"Why is the stock not selling fast enough?"**

### Root Cause Options (Select 1):

#### ○ Option 1: Low customer demand in this area/locality
**What this means:** Not enough customers buying, low footfall, area-specific issue
**Smart prompt for details:** "Which products are slow? Why low demand in this area?"
**Action insight:** Local demand generation, area-specific promotions, demos

#### ○ Option 2: Wrong product mix for this retailer's customers
**What this means:** Products don't match what his customers want to buy
**Smart prompt for details:** "Which products not moving? What do customers ask for instead?"
**Action insight:** Product assortment optimization, SKU rationalization

#### ○ Option 3: Poor visibility or display in shop (merchandising issue)
**What this means:** Products hidden in back room, bottom shelf, no signage, customers don't see it
**Smart prompt for details:** "Where is stock kept? (back room, bottom shelf, no display)"
**Action insight:** Merchandising support, POS materials, shelf placement training

#### ○ Option 4: Other reason
**Requires:** Free text explanation

---

## BUCKET 3: Competitor Margins/Schemes

### Context Question (shown to rep):
**"What is the competitor offering that we're not?"**

### Root Cause Options (Select 1):

#### ○ Option 1: Competitor giving higher margin (more profit per unit)
**What this means:** Competitor brand gives retailer better profit percentage
**Smart prompt for details:** "Which competitor? How much more margin? (%)"
**Action insight:** Margin structure competitive review, adjust retailer margins

#### ○ Option 2: Competitor has better scheme (free goods, gifts, buyback)
**What this means:** Competitor running promotion - buy 10 get 2 free, gift items, buyback guarantee
**Smart prompt for details:** "Which competitor? What scheme? (buy X get Y free, gift)"
**Action insight:** Design counter promotional scheme, limited-time offer

#### ○ Option 3: Competitor offering easier payment terms (longer credit)
**What this means:** Competitor gives more days to pay, better credit policy
**Smart prompt for details:** "Which competitor? What terms? (30 days vs our 15 days)"
**Action insight:** Credit policy competitive alignment, flexible payment options

#### ○ Option 4: Other reason
**Requires:** Free text explanation

---

## BUCKET 4: Pricing/Margin Concerns

### Context Question (shown to rep):
**"What's the pricing issue?"**

### Root Cause Options (Select 1):

#### ○ Option 1: Our MRP/price is higher than competitor brands
**What this means:** Our product costs more than similar competitor products
**Smart prompt for details:** "Which competitor brand? Price difference? (₹ per unit)"
**Action insight:** Pricing strategy review, category positioning, value justification

#### ○ Option 2: Retailer's margin is too low (not enough profit for him)
**What this means:** Retailer makes less money selling our product compared to others
**Smart prompt for details:** "What margin is he getting now? What does he want? (%)"
**Action insight:** Trade margin structure adjustment, margin enhancement schemes

#### ○ Option 3: Recent price increase - market not accepting new price
**What this means:** We raised price recently, customers/retailers resisting
**Smart prompt for details:** "Which product? How much increase? When did it happen?"
**Action insight:** Price increase communication, temporary support scheme, value messaging

#### ○ Option 4: Other reason
**Requires:** Free text explanation

---

## UI/UX Flow Specifications

### Screen 1: Root Cause Selection

**Layout:**
```
┌─────────────────────────────────────────────────┐
│  ✓ Reason: PAYMENT/CREDIT ISSUE                │
│                                                  │
│  Why is payment stuck?                          │
│  ────────────────────────────────────────       │
│                                                  │
│  ⦿ Credit limit or terms issue with             │
│    distributor                                   │
│                                                  │
│  ○ Retailer waiting for customer                │
│    payments to come in                           │
│                                                  │
│  ○ Previous payment dispute or                  │
│    trust issue                                   │
│                                                  │
│  ○ Other reason                                 │
│                                                  │
│                                                  │
│  [Next]                                         │
└─────────────────────────────────────────────────┘
```

**Design Elements:**
- **Header:** Shows confirmed bucket name with checkmark
- **Context Question:** Bold, clear question in conversational tone
- **Radio Buttons:** Large tap targets (minimum 44x44 pt)
- **Option Text:** 2 lines max per option, left-aligned
- **Spacing:** 16pt between options for easy scanning
- **Next Button:** Enabled only after selection

**Timing Target:** 3-5 seconds to scan and select

---

### Screen 2: Additional Details (Optional)

**Layout:**
```
┌─────────────────────────────────────────────────┐
│  ✓ Credit limit or terms issue                  │
│                                                  │
│  Add details (helps your manager take action):  │
│                                                  │
│  Which distributor? How much credit needed?     │
│  ┌───────────────────────────────────────────┐  │
│  │                                            │  │
│  │                                            │  │
│  │                                            │  │
│  └───────────────────────────────────────────┘  │
│                                                  │
│  You can also use voice input:  [🎤 Speak]     │
│                                                  │
│                                                  │
│  [Skip]                    [Submit]             │
└─────────────────────────────────────────────────┘
```

**Design Elements:**
- **Header:** Shows selected root cause (confirmation)
- **Guidance Text:** Shows context-specific smart prompt
- **Text Area:** Multiline input, auto-focuses
- **Voice Input:** Optional mic button for quick input
- **Skip Button:** Clearly visible - doesn't force input
- **Submit Button:** Primary action, bright color

**Timing Target:** 5-10 seconds for optional details (or skip immediately)

---

### Screen 3: Confirmation

**Layout:**
```
┌─────────────────────────────────────────────────┐
│                                                  │
│              ✓                                  │
│                                                  │
│     Rejection Logged Successfully!              │
│                                                  │
│  ────────────────────────────────────────       │
│                                                  │
│  Retailer: Sharma Electronics                   │
│  Reason: Payment/Credit Issue                   │
│  Details: Credit limit issue with XYZ Dist.     │
│                                                  │
│                                                  │
│  [Continue to Next Retailer]                    │
└─────────────────────────────────────────────────┘
```

**Design Elements:**
- **Success Icon:** Large checkmark, green color
- **Summary:** Shows what was logged for quick verification
- **Next Action:** Clear button to continue workflow

**Timing:** Auto-advances after 2 seconds, or manual tap

---

## Complete Flow Timeline

**Total Time: 10-17 seconds**

| Step | Action | Time | Cumulative |
|------|--------|------|------------|
| 1 | Bucket already confirmed by AI | 0s | 0s |
| 2 | **Screen 1:** Scan 3 options, select root cause | 3-5s | 3-5s |
| 3 | Tap "Next" | 1s | 4-6s |
| 4 | **Screen 2:** Add details OR skip | 5-10s | 9-16s |
| 5 | Tap "Submit" | 1s | 10-17s |
| 6 | **Screen 3:** Confirmation (auto-advance) | 2s | 12-19s |

**vs Manual Form:** 2-3 minutes (120-180 seconds)
**Time Saved:** 103-168 seconds per rejection (~90% reduction)

---

## Mobile Optimization

### Accessibility
- **Font Size:** Minimum 16pt for body text, 18pt for options
- **Contrast:** WCAG AA compliant (4.5:1 minimum)
- **Touch Targets:** Minimum 44x44 pt (iOS), 48x48 dp (Android)

### Performance
- **Screen Load:** <200ms
- **Tap Response:** Instant feedback (<100ms)
- **Submit Processing:** <500ms

### Language Support
- **Primary:** English
- **Secondary:** Hindi, Hinglish (same template structure)
- **Auto-detect:** Based on previous user behavior or manual toggle

---

## Hindi/Hinglish Translations (Example for Bucket 1)

**Hinglish Version:**

**Context Question:** "Payment/credit kyun stuck hai?"

**Options:**
1. ○ Distributor ke saath credit limit ya payment terms ki problem
2. ○ Retailer apne customers se payment aane ka wait kar raha hai
3. ○ Pehle ka payment dispute ya trust issue hai
4. ○ Koi aur reason

**Smart Prompts:**
- "Konsa distributor? Kitna credit chahiye?"
- "Payment kab aayega? (date/festival/mahine ke end)"
- "Kya hua tha? (billing issue, quality, product return)"

---

## Implementation Notes

### Data Structure (JSON)

```json
{
  "rejection_id": "REJ_2025102301234",
  "bucket_id": 1,
  "bucket_name": "Outstanding Payment/Credit Issues",
  "root_cause_selected": 2,
  "root_cause_text": "Retailer waiting for customer payments to come in",
  "additional_details": "Expects payment by month-end (Oct 31)",
  "input_method": "text",
  "selection_time_seconds": 12,
  "timestamp": "2025-10-23T14:23:45Z"
}
```

### Backend Validation

- Enforce: Root cause selection is REQUIRED (cannot submit without selecting 1-4)
- Optional: Additional details (can be empty string)
- Track: Selection time for UX optimization
- Flag: If >20% of users select "Other" for a bucket → Review template quality

### Analytics Tracking

**Track these metrics:**
1. **Selection Distribution:** % of users selecting each option per bucket
2. **"Other" Usage Rate:** If >15% select "Other" → Templates need improvement
3. **Details Fill Rate:** Target >70% (measure quality of prompts)
4. **Time to Select:** Median time should be 3-5 seconds
5. **Skip Rate:** How many skip optional details

---

## Quality Assurance Checklist

### Template Quality
- [ ] Each option is distinct and mutually exclusive
- [ ] Language is simple (no technical jargon)
- [ ] Options cover 80%+ of real scenarios
- [ ] Smart prompts guide useful details
- [ ] No circular references to other buckets

### UI/UX Quality
- [ ] All text readable on smallest supported device (iPhone SE)
- [ ] Radio buttons have clear selected/unselected states
- [ ] Keyboard doesn't hide submit button on text input
- [ ] Voice input works in noisy field environments
- [ ] Screen loads in <200ms on 3G connection

### User Testing
- [ ] 5 sales reps complete flow in <15 seconds average
- [ ] 90%+ understand each option without explanation
- [ ] 70%+ provide additional details voluntarily
- [ ] Zero confusion about which option to select
- [ ] Hindi/Hinglish translations verified by native speakers

---

**Document End**

For questions or implementation support, contact: product-team@company.com
