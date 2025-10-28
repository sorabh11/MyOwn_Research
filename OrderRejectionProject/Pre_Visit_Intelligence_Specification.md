# Pre-Visit Intelligence Specification

**Version:** 1.0
**Date:** 2025-10-28
**Purpose:** Sales rep preparation data displayed on store check-in
**Format:** 3-line brief (Scheme + Previous Orders + Market Insight)

---

## Engineering Walkthrough

### Overview

When a sales rep checks into a store, the system displays a 3-line brief:
1. **Scheme** - Relevant promotional scheme for this store
2. **Previous orders** - Last order date and value
3. **Market insight** - Seasonality or local trend

**Response Time:** <500ms

---

### API Endpoint

**Request:**
```http
GET /api/v1/rep/store-brief/{store_id}
Authorization: Bearer {token}
```

**Response:**
```json
{
  "store_id": "STORE_MUM_1234",
  "store_name": "Rajiv Appliances",
  "brief": {
    "scheme": "New \"1+1\" scheme for orders greater than 10 lacs",
    "previous_orders": "20 lacs on last Thursday (25th Oct)",
    "market_insight": "Heaters are on upward trend due to seasonality"
  }
}
```

---

### Scheme Selection Logic

System matches store profile against scheme conditions:
1. Fetch store data (order history, payment status, location)
2. Check which scheme conditions match (2-3 conditions per scheme)
3. Return highest scoring scheme
4. Format as 3-line brief

---

### Display Example

```
┌─────────────────────────────────────────┐
│  ✓ Checked In: Rajiv Appliances         │
│                                          │
│  💡 Remember:                            │
│  • Scheme: New "1+1" for orders >10L    │
│  • Previous: ₹20L on last Thursday      │
│  • Market: Heaters trending up (season) │
│                                          │
│  [Take Order]    [No Order Given]       │
└─────────────────────────────────────────┘
```

---

## Master Schemes Bank

### Scheme 1: Volume Boost
**Description:** "Order 15+ units, get 10% extra margin"

**Conditions:**
- Store ordered <10 units in last 2 months
- High/Medium capacity store (A or B tier)
- No pending payment or <7 days overdue

---

### Scheme 2: Festival Launch
**Description:** "Diwali combo pack - Buy 20 get 5 free + display stand"

**Conditions:**
- Within 30 days before major festival (Diwali, Holi, etc.)
- High footfall area (urban/suburban)
- Store ordered seasonal products in past festivals

---

### Scheme 3: New Product Trial
**Description:** "Water purifiers launch - First order 15% off + free demo"

**Conditions:**
- Store has not ordered this product category before
- Premium catchment area OR good display space
- Store ordered 3+ different categories in past

---

### Scheme 4: Competitive Defense
**Description:** "Match competitor margin + 2% extra for exclusive shelf"

**Conditions:**
- Competitor activity detected in 1km radius
- Store mentioned competitor in last 2 visits
- Order frequency dropping

---

### Scheme 5: Payment Clearance
**Description:** "Clear pending dues, get 5% discount on next order"

**Conditions:**
- Outstanding payment >7 days
- Payment amount <₹50,000
- Store historically pays within 30 days

---

### Scheme 6: Loyalty Reward
**Description:** "Free 2 units on every 50 ordered this quarter"

**Conditions:**
- Store ordered every month for last 3 months
- No payment delays in last 6 months
- Order value stable or increasing

---

## Sample Store Briefs

### Store 1: High-Volume Urban (Mumbai - Andheri)

**Store Profile:**
- Name: Rajiv Appliances | Type: A-tier, high footfall
- Location: Andheri West, Mumbai
- Last 6 months: ₹18L, ₹22L, ₹20L, ₹19L, ₹21L, ₹20L

**Brief on Check-in:**
```
Scheme: Festival Launch - "Diwali combo pack, buy 20 get 5 free + display stand"
Previous orders: ₹20 lacs on last Thursday (25th Oct) - mixers, heaters, fans
Market: Premium appliances up 25% in Andheri area pre-Diwali
```

---

### Store 2: Medium Suburban (Delhi NCR - Gurgaon)

**Store Profile:**
- Name: Kumar Electronics | Type: B-tier, growing area
- Location: Sector 45, Gurgaon
- Last 6 months: ₹8L, ₹9L, ₹0, ₹6L, ₹10L, ₹8L

**Brief on Check-in:**
```
Scheme: New Product Trial - "Water purifiers launch, first order 15% off + free demo"
Previous orders: ₹8 lacs on 10th Oct (no order in September)
Market: Water purifiers demand rising 30% in winter months in Gurgaon
```

---

### Store 3: Small Neighborhood (Tier 2 - Jaipur)

**Store Profile:**
- Name: Sharma General Store | Type: C-tier, community store
- Location: Malviya Nagar, Jaipur
- Last 6 months: ₹3L, ₹4L, ₹3.5L, ₹4L, ₹3L, ₹3.5L

**Brief on Check-in:**
```
Scheme: Volume Boost - "Order 15+ units this month, get 10% extra margin"
Previous orders: ₹3.5 lacs on 8th Oct (basic appliances, fans)
Market: Small appliances performing well in Malviya Nagar locality
```

---

### Store 4: Rural/Semi-Urban (Tier 3 - Nashik)

**Store Profile:**
- Name: Patil Traders | Type: B-tier, agricultural area
- Location: Malegaon Road, Nashik
- Last 6 months: ₹2L, ₹5L, ₹12L, ₹3L, ₹2L, ₹4L

**Brief on Check-in:**
```
Scheme: Festival Launch - "Diwali scheme - Buy 10 get 2 free on all mixers"
Previous orders: ₹4 lacs on 15th Oct (spike in August at ₹12L for festival prep)
Market: Rural demand peaks 45 days before Diwali, order early for stock availability
```

---

### Store 5: High-Competition (Bangalore - Koramangala)

**Store Profile:**
- Name: Tech Home Appliances | Type: A-tier, premium area
- Location: Koramangala 5th Block, Bangalore
- Last 6 months: ₹25L, ₹23L, ₹20L, ₹18L, ₹16L, ₹15L (declining)

**Brief on Check-in:**
```
Scheme: Competitive Defense - "Match competitor margin +2% for exclusive shelf space"
Previous orders: ₹15 lacs on 20th Oct (declining from ₹25L in May)
Market: 3 new competitor brands entered Koramangala in last 2 months
```

---

**Document End**
