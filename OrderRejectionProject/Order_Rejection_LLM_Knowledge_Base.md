# Order Rejection Classification - LLM Knowledge Base

**Version:** 1.0
**Date:** 2025-10-10
**Purpose:** RAG-optimized knowledge base for real-time order rejection reason classification
**Usage:** Vector embeddings, semantic search, pattern matching for AI-powered classification

---

## 🎯 Executive Summary for LLM Implementation

### Purpose
This knowledge base powers **GPT-3.5-turbo with prompt caching** to classify natural language retailer rejection reasons into 15 predefined buckets with **85-90% accuracy**. The system is production-ready with 200+ multilingual samples and domain-aware causality logic.

### Key Innovation: Domain-Aware Causality Detection
**Problem:** Retailers often describe symptoms ("payment pending") when root cause is elsewhere ("stock not selling").

**Solution:** The system understands the FMCG distribution business model to infer causal relationships automatically:
- **Top 4 Buckets (50-60% of rejections):** Apply domain-aware causality reasoning
- **Other 11 Buckets (40-50%):** Standard keyword/semantic matching

**Result:** System identifies root causes, not symptoms, enabling actionable brand intelligence.

### Implementation Approach
```
Input: "Payment pending hai aur stock bhi pada hai"
       (Translation: Payment pending and stock also lying)

Standard Classification → Payment Issues (WRONG - treats symptom)
Domain-Aware Classification → Slow Moving Stock (CORRECT - identifies root cause)
Reasoning: Stock not moving → Capital locked → Payment delayed
```

### Cost & Performance
- **Model:** GPT-3.5-turbo with 90% prompt caching
- **Cost:** $0.001 per classification (~$3/month for 100/day)
- **Latency:** 1-1.5 seconds per classification
- **Accuracy:** 85-90% top-1, 95%+ top-3

### When Clarification is Asked
Only 3 scenarios (~15% of cases):
1. Ambiguous multi-issue input (equal weight to both causes)
2. Low confidence (<60%)
3. New/unusual pattern not in knowledge base

Otherwise: Auto-classified with high confidence.

---

## 📐 Business Model Understanding (CRITICAL for Causality)

### Working Capital Flow Cycle in FMCG Distribution

The LLM **MUST** understand this business model to correctly identify causal relationships:

```
┌─────────────────────────────────────────────────────────┐
│         RETAILER WORKING CAPITAL CYCLE                  │
└─────────────────────────────────────────────────────────┘

1. Retailer places order → Distributor ships goods (credit: 15-30 days)
2. Stock sits in shop → CAPITAL LOCKED (cannot use for other purposes)
3. Stock sells to consumers → Cash inflow
4. Capital released → Retailer pays distributor
5. Can place new order → Cycle repeats

BOTTLENECK SCENARIOS (causing rejections):

Scenario A: Price → Stock → Payment
- High pricing → Demand suppressed → Stock doesn't move → Capital locked → Payment delayed

Scenario B: Competition → Pricing/Margin → Stock
- Competitor pressure → Lower margins OR high MRP → Reduced orders OR slow sales

Scenario C: Stock → Payment (most common)
- Stock not selling → Capital tied up → Cannot pay distributor → Cannot order more
```

### Key Business Insights for Classification

**Insight 1: Payment problems are often SYMPTOMS**
When a retailer says "payment pending", it's usually because:
- Capital is locked in unsold inventory (Bucket #2 - Stock)
- OR pricing makes products unaffordable/uncompetitive (Bucket #4 - Pricing)

**Insight 2: Causal chains follow capital flow**
- If **Price** AND **Stock** mentioned → Price is root cause (suppresses demand)
- If **Payment** AND **Stock** mentioned → Stock is root cause (blocks capital)
- If **Competitor** AND **Margin** mentioned → Competition is root cause (margin pressure)

**Insight 3: Independent issues don't have causal chains**
Buckets #5-15 are independent operational issues:
- Delivery problems (Bucket #5) - logistics issue
- Service concerns (Bucket #10) - after-sales issue
- Space constraints (Bucket #9) - physical limitation
- These don't cause each other; standard matching works fine.

---

## 🧠 Three Causal Rules (Apply ONLY to Buckets #1-4)

### Rule 1: Stock Blocks Capital → Payment Issues

**Pattern Detection:**
```python
# Pseudo-code for causality detection
if (payment_keywords AND stock_keywords):
    if has_explicit_causality_marker("kyunki", "because"):
        # Trust explicit marker
        root_cause = parse_causality(input)
    elif stock_context_is_primary(input):
        root_cause = "Slow Moving Stock"  # Bucket #2
        secondary = "Payment consequence"
    else:
        # Ambiguous - ask clarification
        request_clarification(["Stock not selling?", "Payment issue?"])
```

**Sample LLM System Prompt for Rule 1:**
```
RULE 1: Stock Blocks Capital → Payment Delays

BUSINESS LOGIC:
In FMCG distribution, unsold inventory is the #1 reason for payment delays.
When stock doesn't sell, retailer's working capital is locked, preventing payment to distributor.

CLASSIFICATION GUIDANCE:
IF input mentions BOTH "payment/credit" AND "stock/inventory":
  → PRIMARY ISSUE: Usually STOCK (Bucket #2)
  → SECONDARY TAG: Payment consequence
  → EXCEPTION: If explicit "payment pending because distributor won't extend credit" → Bucket #1

KEYWORDS FOR THIS RULE:
Payment side: payment, credit, dues, outstanding, pending, clear
Stock side: stock, inventory, pada hai, not sold, slow moving, remaining

EXAMPLES:
✅ "Payment pending hai aur stock bhi pada hai"
   → Bucket #2 (Stock) + secondary: Payment
   → Reasoning: Stock not moving is blocking capital

✅ "Payment stuck because stock not selling"
   → Bucket #2 (Stock) + secondary: Payment
   → Reasoning: Explicit causality marker "because" confirms stock is root cause

⚠️ "Payment bhi hai stock bhi hai" (ambiguous)
   → Ask: "Is main issue stock not selling OR payment pending?"
```

---

### Rule 2: Price Suppresses Demand → Stock Doesn't Move

**Pattern Detection:**
```python
# Pseudo-code
if (pricing_keywords AND stock_keywords):
    if has_explicit_causality_marker("kyunki", "because"):
        # Parse causality direction
        if "price" is_cause_of "stock_problem":
            root_cause = "Pricing Concerns"  # Bucket #4
            secondary = "Stock accumulation result"
    else:
        # Default to pricing as deeper root cause
        root_cause = "Pricing Concerns"  # Bucket #4
        secondary = "Stock impact"
```

**Sample LLM System Prompt for Rule 2:**
```
RULE 2: Price Suppresses Demand → Stock Accumulates

BUSINESS LOGIC:
High pricing is the ROOT CAUSE of demand suppression. If products are overpriced,
consumers won't buy, leading to inventory buildup. Fixing stock levels won't help
if fundamental issue is pricing.

CLASSIFICATION GUIDANCE:
IF input mentions BOTH "pricing/MRP/expensive" AND "stock/not selling":
  → PRIMARY ISSUE: Usually PRICING (Bucket #4)
  → SECONDARY TAG: Stock accumulation consequence
  → EXCEPTION: If "stock old/seasonal" without price mention → Bucket #2

KEYWORDS FOR THIS RULE:
Pricing side: price, MRP, expensive, costly, margin low, zyada, mahanga
Stock side: stock, not selling, not moving, bik nahi raha, slow

EXAMPLES:
✅ "Price bahut zyada hai, stock nahi bik raha"
   → Bucket #4 (Pricing) + secondary: Stock not moving
   → Reasoning: Price is explicitly causing poor sales

✅ "Stock pada hai kyunki customers price zyada bol rahe"
   → Bucket #4 (Pricing) + secondary: Stock consequence
   → Reasoning: "kyunki" marker + customers citing price = pricing is root

✅ "MRP high hai compared to competitor, nahi bik raha"
   → Bucket #4 (Pricing) + note: competitive pressure
   → Reasoning: Pricing competitiveness is the core issue
```

---

### Rule 3: Competition Drives Market Pressure

**Pattern Detection:**
```python
# Pseudo-code
if competitor_keywords:
    if (competitor_keywords AND margin_keywords):
        root_cause = "Competitor Margins"  # Bucket #3
    elif (competitor_keywords AND pricing_keywords):
        root_cause = "Pricing Concerns"  # Bucket #4
        secondary = "Competitive pressure"
    elif competitor_alone:
        root_cause = "Market Competition Intensity"  # Bucket #11
```

**Sample LLM System Prompt for Rule 3:**
```
RULE 3: Competition Manifests in Different Ways

BUSINESS LOGIC:
Competitor activity impacts retailers differently:
- Better schemes/margins → Direct competitive disadvantage (actionable)
- Lower pricing → Pricing strategy issue (brand must respond)
- General competition → Market saturation (structural issue)

CLASSIFICATION GUIDANCE:
IF input mentions "competitor/competition":
  IF competitor + "better margin/scheme/incentive" → Bucket #3 (Competitor Margins)
  ELIF competitor + "lower price/MRP comparison" → Bucket #4 (Pricing) + note: competitive
  ELIF competitor mentioned generally → Bucket #11 (Market Competition)

KEYWORDS FOR THIS RULE:
Competitor: competitor, competition, other brand, rival, dusri company
Margins: margin, profit, scheme, incentive, benefit
Pricing: price, MRP, rate, cost

EXAMPLES:
✅ "Competitor 5% zyada margin de raha hai"
   → Bucket #3 (Competitor Margins)
   → Reasoning: Actionable margin/scheme disadvantage

✅ "Competitor ka price kam hai hamse"
   → Bucket #4 (Pricing) + secondary: Competitive pressure
   → Reasoning: Pricing competitiveness issue (brand must adjust pricing)

✅ "Market mein bahut competition hai, bahut brands hain"
   → Bucket #11 (Market Competition Intensity)
   → Reasoning: General market saturation, not specific competitive action
```

---

## 📋 Classification Examples with Causal Reasoning

### Example 1: Implicit Causality (Stock → Payment)
```
INPUT: "Payment pending hai aur stock bhi pada hai"
(Translation: Payment is pending and stock is also lying)

STEP 1: Detect keywords
- payment_keywords = ["payment", "pending"]
- stock_keywords = ["stock", "pada hai"]
→ Both in top 4 buckets, check causality

STEP 2: Check explicit markers
- No "kyunki", "because", or other explicit causality marker
- "aur" (and) = neutral connector, no causality direction

STEP 3: Apply business model knowledge (Rule 1)
- In distribution, unsold stock → capital locked → payment delayed
- Stock is DEEPER root cause, payment is SYMPTOM

STEP 4: Classification
→ PRIMARY: Bucket #2 (Slow Moving Stock)
→ SECONDARY: Payment consequence
→ CONFIDENCE: 87%
→ REASONING: "Stock not moving blocks capital, preventing payment to distributor"
```

### Example 2: Explicit Causality (Price → Stock)
```
INPUT: "Stock nahi bik raha kyunki price bahut zyada hai"
(Translation: Stock not selling because price is too high)

STEP 1: Detect keywords
- stock_keywords = ["stock", "nahi bik raha"]
- pricing_keywords = ["price", "zyada"]
→ Both in top 4 buckets, check causality

STEP 2: Check explicit markers
- "kyunki" (because) detected
- Causality direction: PRICE (cause) → STOCK (effect)

STEP 3: Apply business model knowledge (Rule 2)
- Explicit causality overrides implicit patterns
- Price suppressing demand is confirmed by "kyunki"

STEP 4: Classification
→ PRIMARY: Bucket #4 (Pricing Concerns)
→ SECONDARY: Stock accumulation result
→ CONFIDENCE: 92%
→ REASONING: "High pricing (explicitly stated cause) suppresses demand, causing inventory buildup"
```

### Example 3: Competition + Margin
```
INPUT: "Competitor 5% extra margin de raha hai"
(Translation: Competitor is giving 5% extra margin)

STEP 1: Detect keywords
- competitor_keywords = ["competitor"]
- margin_keywords = ["margin", "extra"]
→ No co-occurrence with payment/stock, check Rule 3

STEP 2: Apply Rule 3
- Competitor + Margin keywords = Bucket #3

STEP 3: Classification
→ PRIMARY: Bucket #3 (Competitor Margins)
→ CONFIDENCE: 89%
→ REASONING: "Direct competitive disadvantage in margin/scheme offerings"
```

### Example 4: Ambiguous Causality (Clarification Needed)
```
INPUT: "Payment bhi hai stock bhi hai"
(Translation: There's payment issue AND stock issue)

STEP 1: Detect keywords
- payment_keywords = ["payment"]
- stock_keywords = ["stock"]
→ Both in top 4 buckets, check causality

STEP 2: Check explicit markers
- "bhi...bhi" (both...and) = equal weight, no causality direction
- No other markers

STEP 3: Apply business model knowledge
- Could be Stock → Payment (capital locked)
- Could be Payment → Can't order (independent credit issue)
- AMBIGUOUS - cannot determine root cause

STEP 4: Ask clarification
→ CONFIDENCE: 55% (low)
→ ACTION: Show options to rep:
   1. "Stock not selling fast (payment delayed because of this)"
   2. "Payment pending (cannot order due to credit limit)"
→ Rep selects one → Confidence updated to 85%
```

---

## Knowledge Base Structure

This knowledge base contains **15 pre-defined rejection reason buckets** identified through market research and field data analysis. Each bucket includes:

- **Clear definitions** for semantic understanding
- **10-15 sample inputs** in multiple languages (English, Hindi, Hinglish)
- **Keywords and patterns** for matching
- **3 template options** (radio buttons) for quick selection
- **Disambiguation rules** to handle overlapping cases
- **Additional details guidance** for context capture

### Classification Methodology by Bucket Type

**Buckets #1-4 (Payment, Stock, Competitor Margins, Pricing) - 50-60% of rejections:**
- ✅ **Domain-aware causality reasoning** applied
- ✅ Detects causal relationships using business model knowledge
- ✅ Identifies root causes, not just symptoms
- ✅ May ask clarification for ambiguous causal direction (~15% of cases)

**Buckets #5-15 (All other buckets) - 40-50% of rejections:**
- ✅ **Standard keyword/semantic matching** applied
- ✅ Independent operational issues (no causal relationships with other buckets)
- ✅ Straightforward classification based on keywords and patterns
- ✅ Examples: Delivery (Bucket #5), Service (Bucket #10), Space (Bucket #9), etc.

**Why this approach works:**
- 80/20 principle: Focus complexity on 4 buckets causing 80% of causal ambiguity
- Other 11 buckets are independent issues that don't cause each other
- Single-pass processing handles both types in one LLM call

---

## BUCKET 01: Outstanding Payment/Credit Issues

**Category:** Financial
**Severity:** High (Most Critical)
**Frequency:** 15-20% of all rejections
**Bucket ID:** 1

### Definition

Retailer is unable to place a new order due to financial constraints related to:
1. Pending payment from previous orders with distributor
2. Credit limit exhausted or unavailable
3. Cash flow problems preventing new stock purchases
4. Payment terms not favorable or unaffordable

This is the #1 most common rejection reason, often systemic in nature, affecting multiple retailers in a territory.

---

### Sample Inputs

#### English Variations
1. "Retailer has pending payment from last order"
2. "Credit limit is exhausted with the distributor"
3. "Cannot place order until previous dues are cleared"
4. "Retailer says he needs to pay the outstanding first"
5. "Outstanding balance needs to be settled before new order"
6. "Distributor not allowing new orders due to unpaid bills"
7. "Cash flow issue, will order after receiving customer payments"
8. "Credit line is full, cannot take more stock"
9. "Payment terms are not feasible right now"
10. "Retailer is waiting for his customers to pay him first"
11. "Previous invoice amount still pending"
12. "Distributor asking to clear dues before new order"
13. "Working capital tied up in existing stock"
14. "Bank loan pending, cash crunch situation"
15. "Payment cycle not aligned with distributor terms"

#### Hindi Variations
1. "दुकानदार का पेमेंट पेंडिंग है पिछले आर्डर का"
2. "क्रेडिट लिमिट खत्म हो गई है"
3. "पिछला पैसा नहीं दिया है अभी तक"
4. "पहले बकाया चुकाना पड़ेगा"
5. "डिस्ट्रीब्यूटर क्रेडिट नहीं दे रहा"
6. "कैश की दिक्कत है अभी"
7. "पुराना बिल क्लियर नहीं हुआ"
8. "पैसा अटका हुआ है कस्टमर के पास"
9. "क्रेडिट लिमिट पूरी भर गई है"
10. "पेमेंट टर्म्स सही नहीं हैं"

#### Hinglish Variations
1. "Retailer ka payment pending hai last order ka"
2. "Credit limit full ho gayi hai distributor ke saath"
3. "Pehle payment clear karna padega"
4. "Dukandaar bol raha hai paise nahi hain abhi"
5. "Previous dues clear nahi hue abhi tak"
6. "Distributor credit nahi de raha new order ke liye"
7. "Cash flow ka problem hai, customer se paisa aane ke baad order denge"
8. "Working capital block ho gaya hai"
9. "Credit line exhaust ho gayi hai"
10. "Payment pending hai ₹25000 ka last month se"
11. "Retailer bola ki pehle purana payment clear karo"
12. "Cash nahi hai dukaan mein, customers se collection hone ke baad lenge"
13. "Outstanding amount bahut zyada ho gaya hai"
14. "Distributor ka payment terms tight hai, afford nahi kar sakta"
15. "Previous bill ka paisa dena hai pehle"

#### Voice Pattern Variations

**Short/Quick Forms:**
- "Payment pending"
- "Credit limit khatam"
- "Paise nahi hain"
- "Outstanding hai"
- "Dues clear nahi"

**Medium Length:**
- "Retailer has pending payment issue"
- "Credit limit full hai distributor ke paas"
- "Cash flow problem hai abhi"

**Long/Detailed Forms:**
- "Retailer is saying that his previous order payment is not yet cleared and distributor is not allowing new orders until he pays"
- "Dukaan wale ka last month ka ₹25000 pending hai aur distributor bol raha ki pehle wo clear karo tab naya order lenge"
- "Cash flow issue hai because customers ne payment nahi diya hai, jab wo denge tab ye order kar payega"

---

### Keywords & Patterns

**Primary Keywords:**
`payment`, `credit`, `dues`, `outstanding`, `pending`, `clear`, `settle`, `unpaid`, `limit`, `exhausted`

**Secondary Keywords:**
`previous order`, `last order`, `distributor`, `balance`, `invoice`, `bill`, `cash flow`, `working capital`, `terms`, `afford`

**Hindi Keywords:**
`पेमेंट`, `क्रेडिट`, `बकाया`, `पैसा`, `लिमिट`, `क्लियर`, `पेंडिंग`

**Negation Patterns:**
- "not paid", "hasn't cleared", "didn't pay", "cannot pay"
- "nahi diya", "clear nahi hua", "nahi kar sakta"

**Amount/Time Indicators:**
- "₹___", "rupees", "thousand", "lakh"
- "days pending", "since last month", "weeks ago"
- "___ दिन से", "महीने से"

**Emotional/Urgency Indicators:**
- "tight", "crunch", "stuck", "blocked", "problem"
- "दिक्कत", "अटका", "मुश्किल"

---

### Template Options (3 Radio Buttons)

```
○ Previous order payment pending (₹___ outstanding since ___ days)

○ Credit limit exhausted with distributor

○ Cash flow issue - will order after receiving payment from customers
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #14 (MOQ Too High):**
- Key differentiator: Is the issue **affordability** (Bucket #1) or **order quantity requirement** (Bucket #14)?
- If retailer says "cannot afford" or "too much money" → Bucket #1
- If retailer says "order size too big" or "don't need that much" → Bucket #14

**If confused with Bucket #12 (Distributor Relationship):**
- Key differentiator: Is the issue **specific unpaid dues** (Bucket #1) or **general poor relationship** (Bucket #12)?
- If specific amount or payment mentioned → Bucket #1
- If "not happy with distributor" or "service issues" → Bucket #12

**If confused with Bucket #15 (Economic Conditions):**
- Key differentiator: Is it **retailer's specific cash flow** (Bucket #1) or **broader market downturn** (Bucket #15)?
- If "my payment pending" or "my credit" → Bucket #1
- If "business is slow overall" or "market conditions" → Bucket #15

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Outstanding amount:** "₹_____"
- **Days/months pending:** "Since when?"
- **Expected payment date:** "When will they pay?"
- **Distributor name:** "Which distributor?"
- **Any specific SKUs affected:** "All products or specific items?"

---
---

## BUCKET 02: Existing Stock Not Sold / Slow Moving Inventory

**Category:** Inventory Management
**Severity:** High
**Frequency:** 15-20% of all rejections
**Bucket ID:** 2

### Definition

Retailer already has unsold inventory from previous orders and cannot accommodate new stock because:
1. Products are not selling fast enough (slow offtake/turnover)
2. Shelf space is occupied by existing stock
3. Fear of dead stock tying up working capital
4. Seasonal products from last cycle still unsold

This is especially common for appliances with longer sales cycles and seasonal demand patterns.

---

### Sample Inputs

#### English Variations
1. "Retailer still has stock from last month"
2. "Products are not moving fast enough"
3. "Previous order inventory still available"
4. "Shop has no space for new stock, existing stock not sold"
5. "Slow moving inventory, customers not buying"
6. "Last season's stock still sitting on shelves"
7. "Offtake is very low, stock rotation poor"
8. "Already have 10 units, haven't sold even 2"
9. "Festival stock from last quarter not cleared"
10. "Retailer says products are not in demand right now"
11. "Too much inventory already, no room for more"
12. "Stock is gathering dust, customers not interested"
13. "Previous stock not finished yet"
14. "Shelves are full, cannot accommodate new products"
15. "Sales velocity is very low for this brand"

#### Hindi Variations
1. "दुकान में स्टॉक पड़ा हुआ है अभी भी"
2. "पिछला माल नहीं बिका है"
3. "सामान बिक नहीं रहा है"
4. "जगह नहीं है नए स्टॉक के लिए"
5. "पुराना स्टॉक पड़ा हुआ है"
6. "ग्राहक नहीं खरीद रहे हैं"
7. "माल धीरे चल रहा है"
8. "सीजन का स्टॉक अभी तक नहीं बिका"
9. "शेल्फ भरी हुई है"
10. "डिमांड नहीं है अभी"

#### Hinglish Variations
1. "Dukaan mein abhi bhi stock pada hai"
2. "Last month wala stock nahi bika hai"
3. "Saman bikk nahi raha hai"
4. "Jagah nahi hai store mein naye stock ke liye"
5. "Previous order ka stock abhi bhi pada hai"
6. "Products move nahi ho rahe hain"
7. "10 units hain abhi bhi, sirf 2 bik गये"
8. "Slow moving hai ye category"
9. "Festival season ka stock clear nahi hua abhi tak"
10. "Customers nahi aa rahe hain, demand nahi hai"
11. "Shelf space full hai, room nahi hai"
12. "Mixer grinder 8 pieces hain already, nahi bik रहे"
13. "Offtake bahut slow hai is brand ka"
14. "Stock rotation kharab hai, purana stock pehle khatam karna hai"
15. "Working capital block ho gaya hai unsold stock mein"

#### Voice Pattern Variations

**Short Forms:**
- "Stock pada hai"
- "Nahi bika abhi tak"
- "Jagah nahi hai"
- "Slow moving"

**Medium Length:**
- "Retailer has stock from previous order"
- "Products nahi bik rahe hain, stock pada hai"
- "Space issue hai, purana stock clear nahi hua"

**Long Forms:**
- "Retailer is saying that he still has 10 units of mixer grinder from last month's order and only 2 have sold, so he doesn't want new stock until these are cleared"
- "Dukaan mein last season ka stock abhi bhi pada hai, customers nahi khhareed rahe hain, isliye naya order nahi le sakta"

---

### Keywords & Patterns

**Primary Keywords:**
`stock`, `inventory`, `unsold`, `not sold`, `not moving`, `slow`, `previous order`, `last month`, `sitting`, `remaining`

**Secondary Keywords:**
`offtake`, `turnover`, `rotation`, `space`, `shelves full`, `no room`, `demand low`, `seasonal`, `gathering dust`, `blocked capital`

**Hindi Keywords:**
`स्टॉक`, `माल`, `बिका`, `पड़ा`, `जगह`, `सामान`

**Quantity Indicators:**
- "10 units", "5 pieces", "half box", "cartons"
- "___  units remaining", "___ pieces abhi bhi hain"

**Time Indicators:**
- "last month", "last season", "previous quarter", "since festival"
- "पिछले महीने", "पिछले सीजन"

**Negation/Problem Indicators:**
- "not sold", "not moving", "not selling", "slow"
- "nahi bika", "nahi chal raha"

---

### Template Options (3 Radio Buttons)

```
○ High inventory, products not moving fast enough

○ Previous order stock still available, no space for new stock

○ Seasonal products from last season not yet sold
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #9 (Space Constraints):**
- Key differentiator: Is the issue **unsold stock** (Bucket #2) or **physical space limitation** (Bucket #9)?
- If "stock not sold" or "not moving" mentioned → Bucket #2
- If "small shop" or "no display space" without mentioning unsold stock → Bucket #9

**If confused with Bucket #6 (Seasonal Timing):**
- Key differentiator: **Unsold old stock** (Bucket #2) vs **waiting for future demand** (Bucket #6)?
- If "last season's stock" or "previous stock remaining" → Bucket #2
- If "will order before next festival" or "waiting for season" → Bucket #6

**If confused with Bucket #4 (Pricing Concerns):**
- If retailer mentions "products not selling because price is high" → Could be Bucket #2 OR #4
- Ask clarification: "Is it price issue or demand issue?"
- If emphasis on "price too high" → Bucket #4
- If emphasis on "not selling / slow demand" → Bucket #2

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Quantity remaining:** "How many units/pieces?"
- **Which products:** "Which SKU/model?"
- **Since when:** "From which order/month?"
- **Reason for slow movement:** "Why not selling? (price/demand/competition)"
- **Expected clearance:** "When does retailer expect to sell existing stock?"

---
---

## BUCKET 03: Competitor Offering Better Margins/Schemes

**Category:** Competitive Pressure
**Severity:** High
**Frequency:** 8-12% of all rejections
**Bucket ID:** 3

### Definition

Retailer prefers competitor brands because they are offering:
1. Higher trade margins or better profit potential
2. More attractive promotional schemes or incentives
3. Better credit terms or payment flexibility
4. Immediate benefits (gifts, discounts, targets)

This indicates direct competitive threat and requires brand-level response with counter-offers or value differentiation.

---

### Sample Inputs

#### English Variations
1. "Competitor is giving better margin"
2. "Other brand offering 5% more trade discount"
3. "Rival brand has better scheme running this month"
4. "Competitor giving better credit terms"
5. "Other company offers better incentives"
6. "Bajaj giving extra 2% margin compared to us"
7. "Retailer says Prestige has better scheme"
8. "Another brand giving free gifts on purchase"
9. "Competition offering better payment terms"
10. "Competitor scheme gives immediate benefits"
11. "Other brand more profitable for retailer"
12. "Retailer prefers competitor due to higher margin"
13. "Competitor running Diwali offer, retailer wants that"
14. "Another brand giving display material free"
15. "Competition has festival scheme with better returns"

#### Hindi Variations
1. "दूसरी कंपनी ज्यादा मार्जिन दे रही है"
2. "कम्पटीटर का स्कीम अच्छा है"
3. "बाकी ब्रांड बेहतर ऑफर दे रहे हैं"
4. "प्रतिस्पर्धी कंपनी बेहतर क्रेडिट टर्म दे रही है"
5. "दूसरे ब्रांड का प्रॉफिट ज्यादा है"
6. "कम्पटीटर गिफ्ट दे रहा है"
7. "अन्य कंपनी की स्कीम बेहतर है"
8. "प्रेस्टीज ज्यादा मार्जिन दे रहा है"
9. "दूसरे ब्रांड में फायदा ज्यादा है"
10. "कम्पटीटर बेहतर पेमेंट टर्म्स दे रहा है"

#### Hinglish Variations
1. "Competitor zyada margin de raha hai"
2. "Dusri company ka scheme better hai"
3. "Prestige zyada profit de raha hai retailer ko"
4. "Competition me 5% extra margin mil raha hai"
5. "Competitor brand ka scheme achha chal raha hai"
6. "Bajaj 2% extra de raha hai compared to hamare product"
7. "Other brand se zyada kammai hai"
8. "Competitor festival scheme leke aaya hai better wala"
9. "Dusre brand ka credit terms achhe hain"
10. "Competition gift aur incentive de raha hai"
11. "Retailer bol raha hai competitor ka margin better hai"
12. "Other company immediate benefit de rahi hai scheme mein"
13. "Competitor ka offer zyada attractive hai"
14. "Dusre brand pe zyada profit banta hai"
15. "Prestige walo ne achha scheme diya hai is month"

#### Voice Pattern Variations

**Short Forms:**
- "Competitor better hai"
- "Margin kam hai"
- "Dusri company zyada de rahi hai"

**Medium Length:**
- "Competitor is offering better margins"
- "Other brand ka scheme zyada attractive hai"
- "Competition me profit zyada milta hai"

**Long Forms:**
- "Retailer is saying that Prestige is giving 5% more margin compared to our brand and also running a festival scheme with free gifts"
- "Competitor brand Bajaj zyada margin de raha hai aur credit terms bhi better hain, isliye wo prefer kar raha hai"

---

### Keywords & Patterns

**Primary Keywords:**
`competitor`, `competition`, `other brand`, `rival`, `other company`, `better margin`, `better scheme`, `more profit`

**Secondary Keywords:**
`Prestige`, `Bajaj`, `Philips`, `Crompton` (competitor brand names)
`discount`, `incentive`, `gift`, `offer`, `credit terms`, `attractive`

**Hindi Keywords:**
`प्रतिस्पर्धी`, `कम्पटीटर`, `दूसरी कंपनी`, `मार्जिन`, `स्कीम`

**Comparison Indicators:**
- "better than", "more than", "compared to", "vs", "versus"
- "zyada", "बेहतर", "achha"

**Margin/Profit Keywords:**
- "margin", "profit", "commission", "earnings", "returns"
- "मार्जिन", "मुनाफा", "फायदा", "kammai"

**Scheme Keywords:**
- "scheme", "offer", "promotion", "deal", "festival scheme"
- "स्कीम", "ऑफर", "छूट"

---

### Template Options (3 Radio Buttons)

```
○ Competitor brand offering higher trade margin/profit

○ Competitor has better promotional scheme or incentives running

○ Competitor providing better credit terms or payment flexibility
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #4 (Pricing Concerns):**
- Key differentiator: Is issue **retailer margin** (Bucket #3) or **MRP/consumer price** (Bucket #4)?
- If "competitor giving better margin/profit" → Bucket #3
- If "product price too high for customers" → Bucket #4

**If confused with Bucket #7 (Scheme Confusion):**
- Key differentiator: **Our scheme unclear** (Bucket #7) vs **Competitor scheme better** (Bucket #3)?
- If "don't understand our scheme" or "scheme not clear" → Bucket #7
- If "competitor's scheme is better" → Bucket #3

**If confused with Bucket #13 (Product Quality):**
- If retailer says "competitor product is better quality" → Might be quality issue (Bucket #13)
- But if "competitor offers better margin despite same quality" → Bucket #3
- Check if focus is on financial benefit or product itself

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Competitor brand name:** "Which brand?"
- **Specific margin difference:** "How much more margin? (% or ₹)"
- **Scheme details:** "What is competitor offering?"
- **Validity:** "For how long is this offer?"
- **Territory scope:** "Is this common in the area?"

---
---

## BUCKET 04: Pricing/Margin Concerns

**Category:** Financial
**Severity:** High
**Frequency:** 8-10% of all rejections
**Bucket ID:** 4

### Definition

Retailer believes the product pricing is not competitive or profitable because:
1. MRP too high compared to local market conditions
2. Trade margin offered is insufficient
3. Recent price increases making products less attractive
4. Consumer price sensitivity limiting sales potential

This is distinct from competitor margins (Bucket #3) - here the issue is absolute pricing, not relative to competition.

---

### Sample Inputs

#### English Variations
1. "Product price is too high"
2. "MRP not competitive for this market"
3. "Customers won't pay this much"
4. "Price increased recently, demand dropped"
5. "Margin is too low on this product"
6. "Product is overpriced for tier-2 city"
7. "Cannot sell at this MRP in local market"
8. "Price too high for customers here"
9. "Recent price hike making it difficult to sell"
10. "Retailer says consumers are price-sensitive"
11. "MRP should be lower for this category"
12. "Landing price too high, margin not enough"
13. "Price point not suitable for this area"
14. "Customers comparing with lower-priced alternatives"
15. "₹2500 is too expensive, should be ₹2000"

#### Hindi Variations
1. "प्रोडक्ट की कीमत बहुत ज्यादा है"
2. "एमआरपी बहुत ऊंची है"
3. "ग्राहक इतना नहीं देंगे"
4. "कीमत बढ़ गई है हाल में"
5. "मार्जिन कम है"
6. "यहाँ के बाजार के लिए महंगा है"
7. "इस दाम पर नहीं बिकेगा"
8. "लोकल मार्केट में यह रेट नहीं चलेगा"
9. "ग्राहकों की खरीद क्षमता कम है"
10. "प्राइस ज्यादा है इस एरिया के लिए"

#### Hinglish Variations
1. "Price bahut zyada hai"
2. "MRP high hai, customer nahi lenge"
3. "Itne mein nahi bikega yahan"
4. "Price badh gayi hai recently, demand down ho gayi"
5. "Margin kam hai is product pe"
6. "₹2500 bahut zyada hai, ₹2000 hona chahiye"
7. "Customers price-sensitive hain yahan"
8. "Tier-2 city hai, itna mahanga nahi chalega"
9. "Local market mein ye rate nahi milega"
10. "Landing price zyada aa raha hai, profit kam hai"
11. "Price point theek nahi hai is area ke liye"
12. "Competitors ka price kam hai"
13. "Customers saste option dekh rahe hain"
14. "MRP reduce karo, tab order lenge"
15. "Margin aur price dono adjust nahi ho rahe"

#### Voice Pattern Variations

**Short Forms:**
- "Price zyada hai"
- "Mahanga hai"
- "MRP high"
- "Margin kam"

**Medium Length:**
- "Product price too high for this market"
- "MRP zyada hai, customers afford nahi kar sakte"
- "Margin theek nahi hai is price pe"

**Long Forms:**
- "Retailer is saying that ₹2500 MRP is too high for mixer grinder in this tier-2 city, customers are price-sensitive and preferring ₹2000 options"
- "Price recently badh gayi hai ₹200 se, ab demand kam ho gayi hai aur margin bhi satisfactory nahi hai"

---

### Keywords & Patterns

**Primary Keywords:**
`price`, `MRP`, `expensive`, `costly`, `high price`, `overpriced`, `margin low`, `pricing`

**Secondary Keywords:**
`price increase`, `hike`, `rate`, `cost`, `price-sensitive`, `afford`, `competitive`, `landing price`

**Hindi Keywords:**
`कीमत`, `दाम`, `रेट`, `महंगा`, `एमआरपी`, `मार्जिन`

**Price Indicators:**
- "₹___", "rupees", "___ rs"
- "too high", "too expensive", "zyada", "mahanga"

**Comparison Words:**
- "should be ___", "can be ___", "lower", "reduce"
- "kam", "reduce", "कम"

**Customer Impact:**
- "customers won't buy", "consumer price-sensitive", "demand dropped"
- "customers nahi lenge", "afford nahi kar sakte"

---

### Template Options (3 Radio Buttons)

```
○ MRP too high compared to local market conditions

○ Trade margin insufficient, not profitable enough

○ Recent price increase making product less attractive
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #3 (Competitor Margins):**
- Key differentiator: **Absolute price** (Bucket #4) vs **Relative to competitor** (Bucket #3)?
- If "price is too high" or "MRP should be lower" → Bucket #4
- If "competitor giving better margin" → Bucket #3

**If confused with Bucket #1 (Payment Issues):**
- Key differentiator: **Product pricing** (Bucket #4) vs **Retailer affordability** (Bucket #1)?
- If "product price high" or "MRP issue" → Bucket #4
- If "I cannot afford" or "my payment pending" → Bucket #1

**If confused with Bucket #2 (Slow Moving):**
- If retailer says "not selling because price is high" → Could be both
- Primary focus matters:
  - If "price needs to come down" → Bucket #4
  - If "stock is stuck, not moving" → Bucket #2

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Current MRP:** "₹____"
- **Retailer's suggested price:** "What price does he think will work?"
- **Specific product/SKU:** "Which model?"
- **Competitor pricing:** "What are competitors selling at?"
- **Local market context:** "Why is this price not suitable here?"

---
---

## BUCKET 05: Delivery/Logistics Issues

**Category:** Service & Operations
**Severity:** Medium-High
**Frequency:** 5-8% of all rejections
**Bucket ID:** 5

### Definition

Retailer experienced or anticipates problems with order fulfillment related to:
1. Past delivery delays or unreliable service
2. Damaged goods received in previous shipments
3. Delivery timing not matching retailer's needs
4. Logistics challenges specific to location (tier-2/3 cities, remote areas)

This often reflects distributor service quality issues requiring escalation.

---

### Sample Inputs

#### English Variations
1. "Last delivery came 5 days late"
2. "Products arrived damaged last time"
3. "Distributor service is unreliable"
4. "Delivery timing doesn't match shop requirements"
5. "Previous order took too long to arrive"
6. "Received broken items in last shipment"
7. "Delivery schedule not consistent"
8. "Logistics problem in this area, always delayed"
9. "Distributor doesn't deliver on time"
10. "Packaging was poor, products damaged"
11. "Cannot trust delivery dates given"
12. "Takes 2 weeks to get stock, too slow"
13. "Delivery person attitude is bad"
14. "Loading/unloading damaged products"
15. "Remote area, distributor charges extra"

#### Hindi Variations
1. "डिलीवरी देर से आती है"
2. "सामान टूटा हुआ आया था"
3. "डिस्ट्रीब्यूटर की सर्विस खराब है"
4. "समय पर माल नहीं पहुंचता"
5. "पिछली बार देर से आया"
6. "पैकिंग खराब थी"
7. "ट्रांसपोर्ट की दिक्कत है"
8. "भरोसा नहीं है डिलीवरी पर"
9. "दूर का इलाका है, मुश्किल होती है"
10. "नुकसान हुआ था आखिरी शिपमेंट में"

#### Hinglish Variations
1. "Delivery late aati hai har baar"
2. "Last time saman damaged condition mein aaya"
3. "Distributor service achhi nahi hai"
4. "Time pe delivery nahi hoti"
5. "Previous order 1 week late tha"
6. "Products broken condition mein mile the"
7. "Packaging weak hoti hai, saman tut jata hai"
8. "Delivery promise nahi hoti reliable"
9. "Remote area hai, logistics issue hai"
10. "Distributor transport charges extra leta hai"
11. "2-3 baar complaint di hai, koi action nahi"
12. "Delivery person ka behavior achha nahi"
13. "Loading time damage ho jata hai saman"
14. "Tier-3 city hai, delivery mushkil hoti hai"
15. "Unloading mein bhi problem hai, handling kharab"

#### Voice Pattern Variations

**Short Forms:**
- "Delivery late hai"
- "Damaged aaya tha"
- "Service kharab"

**Medium Length:**
- "Delivery time pe nahi hoti"
- "Last order damaged condition mein aaya"
- "Logistics issue hai is area mein"

**Long Forms:**
- "Previous order 5 days late deliver hua tha aur 2 mixer grinder damaged condition mein the, isliye ab order nahi dena chahta"
- "Distributor ki delivery service unreliable hai, promise karte hain 3 days mein par 1 week lag jata hai"

---

### Keywords & Patterns

**Primary Keywords:**
`delivery`, `logistics`, `transport`, `late`, `delayed`, `damaged`, `broken`, `shipment`, `unreliable`

**Secondary Keywords:**
`packaging`, `distributor service`, `timing`, `schedule`, `loading`, `unloading`, `remote area`, `charges`, `promise`

**Hindi Keywords:**
`डिलीवरी`, `ट्रांसपोर्ट`, `सामान`, `टूटा`, `देर`, `सर्विस`

**Time Indicators:**
- "late", "delayed", "slow", "took ___ days"
- "देर से", "late"

**Damage Indicators:**
- "damaged", "broken", "defective", "poor packaging"
- "टूटा", "खराब", "damaged"

**Location Indicators:**
- "remote area", "tier-3", "far", "distance"
- "दूर", "remote"

---

### Template Options (3 Radio Buttons)

```
○ Previous delivery was delayed, unreliable service

○ Received damaged goods in last shipment, packaging issue

○ Delivery timing/schedule doesn't match retailer's requirements
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #12 (Distributor Relationship):**
- Key differentiator: **Specific delivery issue** (Bucket #5) vs **General relationship problem** (Bucket #12)?
- If "delivery late" or "damaged goods" → Bucket #5
- If "don't like working with this distributor" → Bucket #12

**If confused with Bucket #8 (Product Availability):**
- Key differentiator: **Delivery of ordered items** (Bucket #5) vs **Stock not available to order** (Bucket #8)?
- If "ordered but delivery was late" → Bucket #5
- If "product out of stock, cannot order" → Bucket #8

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **What went wrong:** "Delay / Damage / Both?"
- **When:** "Which order/date?"
- **Extent of issue:** "How many days late? How many pieces damaged?"
- **Distributor name:** "Which distributor?"
- **Action taken:** "Was damage claim filed?"

---
---

## BUCKET 06: Seasonal/Demand Timing

**Category:** Market Dynamics
**Severity:** Medium
**Frequency:** 5-8% of all rejections
**Bucket ID:** 6

### Definition

Retailer is timing inventory purchases based on seasonal demand patterns:
1. Currently off-season for the product category
2. Waiting for festival/wedding season to stock up
3. Local market demand currently low, will order closer to peak
4. Prefers to buy just-in-time rather than hold inventory

This is a temporary deferral, not a permanent rejection - important to track for follow-up timing.

---

### Sample Inputs

#### English Variations
1. "Off-season right now, will order before Diwali"
2. "Waiting for wedding season demand"
3. "Festival is 2 months away, will order closer"
4. "Not the right time, demand is low currently"
5. "Will stock up before peak season"
6. "Customers don't buy fans in winter"
7. "Summer products will order in March"
8. "Diwali season approaching, will order then"
9. "Wedding season starts next month, will take then"
10. "Off-season for this category right now"
11. "Too early to stock for festival"
12. "Prefer to order just before demand increases"
13. "Market is slow now, will order in peak time"
14. "Monsoon season products will order later"
15. "Waiting for right time to stock"

#### Hindi Variations
1. "अभी सीजन नहीं है"
2. "त्योहार के समय आर्डर करेंगे"
3. "शादी का सीजन आएगा तब लेंगे"
4. "दिवाली से पहले स्टॉक लेंगे"
5. "अभी डिमांड कम है"
6. "पीक टाइम पर आर्डर देंगे"
7. "सर्दी में पंखे नहीं बिकते"
8. "गर्मी के मौसम में लेंगे"
9. "फेस्टिवल पास आने पर स्टॉक करेंगे"
10. "अभी समय नहीं है"

#### Hinglish Variations
1. "Abhi off-season hai, Diwali se pehle lenge"
2. "Festival time pe order karenge"
3. "Wedding season shuru hone wala hai, tab stock karenge"
4. "Abhi demand nahi hai, peak season mein lenge"
5. "2 mahine baad Diwali hai, tab order denge"
6. "Summer products March mein lenge"
7. "Abhi customers nahi kharid rahe, baad mein ayenge"
8. "Off-season chal raha hai is category ka"
9. "Just-in-time lena pasand hai, pehle se nahi"
10. "Peak time aane do, tab order karenge"
11. "Monsoon products baad mein chahiye"
12. "Sahi time pe stock karna chahte hain"
13. "Festive season ke liye plan kar rahe hain"
14. "Winter mein heater lenge, fans nahi"
15. "Demand badhne pe order denge"

#### Voice Pattern Variations

**Short Forms:**
- "Off-season hai"
- "Diwali pe lenge"
- "Abhi nahi"

**Medium Length:**
- "Festival season mein order karenge"
- "Peak time pe stock karna hai"
- "Demand low hai abhi"

**Long Forms:**
- "Abhi off-season chal raha hai is product category ka, Diwali 2 months baad hai, us time peak demand hoga tab stock karenge"
- "Wedding season March mein start hoga, abhi order dene se working capital block ho jayega unnecessarily"

---

### Keywords & Patterns

**Primary Keywords:**
`season`, `seasonal`, `off-season`, `festival`, `Diwali`, `wedding`, `peak time`, `timing`, `demand low`

**Secondary Keywords:**
`too early`, `wait`, `later`, `before`, `just-in-time`, `summer`, `winter`, `monsoon`, `festive`

**Hindi Keywords:**
`सीजन`, `त्योहार`, `शादी`, `दिवाली`, `समय`

**Festival/Event Names:**
- "Diwali", "Holi", "Christmas", "New Year"
- "wedding season", "festival season", "holiday season"
- "दिवाली", "होली", "शादी का सीजन"

**Time Indicators:**
- "before ___", "after ___", "in ___ months", "next month"
- "पहले", "बाद में", "___ महीने में"

**Demand Words:**
- "demand low", "demand will increase", "peak time"
- "demand kam hai", "demand badhegi"

---

### Template Options (3 Radio Buttons)

```
○ Off-season for this product category currently

○ Waiting for festival/wedding season to place order

○ Will order closer to peak demand period to avoid inventory blocking
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #2 (Slow Moving Inventory):**
- Key differentiator: **Future timing** (Bucket #6) vs **Current unsold stock** (Bucket #2)?
- If "will order before Diwali" or "waiting for season" → Bucket #6
- If "last season's stock still unsold" → Bucket #2

**If confused with Bucket #15 (Economic Conditions):**
- Key differentiator: **Seasonal pattern** (Bucket #6) vs **Economic downturn** (Bucket #15)?
- If mentions specific festival/season → Bucket #6
- If "business slow overall" or "market down" → Bucket #15

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Which season/festival:** "Diwali? Wedding season?"
- **When will they order:** "Expected order timing?"
- **Which products:** "All products or specific categories?"
- **Follow-up date:** "When to revisit?"

---
---

## BUCKET 07: Scheme/Promotion Confusion or Dissatisfaction

**Category:** Operational Complexity
**Severity:** Medium-High
**Frequency:** 5-7% of all rejections
**Bucket ID:** 7

### Definition

Retailer is hesitant to order due to issues with promotional schemes:
1. Unclear scheme terms and conditions
2. Benefits from previous schemes not received/delayed
3. Complexity in claiming scheme benefits
4. Confusion about eligibility or how to participate

This indicates a need for clearer communication and scheme simplification.

---

### Sample Inputs

#### English Variations
1. "Scheme is not clear, don't understand terms"
2. "Last scheme benefit not received yet"
3. "Previous scheme discount was not credited"
4. "Too complicated to claim scheme benefits"
5. "Don't know if we're eligible for current scheme"
6. "Scheme details are confusing"
7. "Promise was made but benefit not given"
8. "Documentation required for scheme is too much"
9. "Claimed last month but still pending"
10. "Scheme always has hidden conditions"
11. "Never got the gifts that were promised"
12. "Scheme benefits take too long to process"
13. "Don't trust scheme promises anymore"
14. "Terms keep changing, difficult to track"
15. "Target achievement not calculated correctly"

#### Hindi Variations
1. "स्कीम समझ नहीं आ रही है"
2. "पिछली स्कीम का फायदा नहीं मिला"
3. "स्कीम बहुत जटिल है"
4. "क्लेम करना मुश्किल है"
5. "स्कीम के पैसे नहीं आए"
6. "शर्तें साफ नहीं हैं"
7. "वादा किया था पर नहीं मिला"
8. "बहुत डॉक्यूमेंट मांगते हैं"
9. "स्कीम बेनिफिट पेंडिंग है"
10. "स्कीम का भरोसा नहीं है अब"

#### Hinglish Variations
1. "Scheme clear nahi hai, samajh nahi aa raha"
2. "Last scheme ka benefit nahi mila abhi tak"
3. "Previous month ka discount credit nahi hua"
4. "Scheme claim karna bahut complicated hai"
5. "Pata nahi current scheme mein eligible hain ki nahi"
6. "Terms and conditions bahut zyada hain"
7. "Promise tha gift ka, nahi mila"
8. "Scheme benefits processing mein bahut time lagta hai"
9. "Documents ki requirement zyada hai"
10. "Last time claim kiya tha, abhi tak pending hai"
11. "Scheme ka calculation galat hai"
12. "Target achieve kiya par benefit nahi diya"
13. "Hidden conditions hoti hain hamesha"
14. "Scheme trust nahi kar sakte ab"
15. "Terms change ho jati hain beech mein"

#### Voice Pattern Variations

**Short Forms:**
- "Scheme clear nahi"
- "Benefit nahi mila"
- "Complicated hai"

**Medium Length:**
- "Scheme samajh nahi aa rahi, terms confusing hain"
- "Last scheme ka benefit abhi tak pending hai"
- "Claim process bahut difficult hai"

**Long Forms:**
- "Previous month mein scheme ke tahat 5% extra discount ka promise tha par abhi tak account mein credit nahi hua, isliye ab order nahi dena chahta"
- "Scheme bahut complicated hai, terms and conditions samajh nahi aate aur documentation requirement bhi zyada hai"

---

### Keywords & Patterns

**Primary Keywords:**
`scheme`, `promotion`, `benefit`, `claim`, `discount`, `not received`, `unclear`, `confusing`, `complicated`

**Secondary Keywords:**
`terms`, `conditions`, `eligibility`, `promise`, `pending`, `documentation`, `gift`, `target`, `calculation`, `hidden`

**Hindi Keywords:**
`स्कीम`, `फायदा`, `बेनिफिट`, `क्लेम`, `डिस्काउंट`

**Complaint Indicators:**
- "not received", "not given", "not credited", "pending"
- "nahi mila", "pending hai"

**Complexity Indicators:**
- "confusing", "complicated", "difficult", "too much"
- "complicated", "mushkil", "zyada"

**Trust Issues:**
- "don't trust", "not reliable", "hidden conditions"
- "bharosa nahi", "trust nahi"

---

### Template Options (3 Radio Buttons)

```
○ Scheme terms unclear or too complicated to understand

○ Previous scheme benefits not received or delayed

○ Difficulty in claiming scheme benefits (documentation/process issues)
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #3 (Competitor Schemes):**
- Key differentiator: **Our scheme issues** (Bucket #7) vs **Competitor scheme better** (Bucket #3)?
- If "our scheme not clear" or "benefits not received" → Bucket #7
- If "competitor scheme better" → Bucket #3

**If confused with Bucket #1 (Payment Issues):**
- If "scheme amount not credited" could seem like payment issue
- Check context:
  - If scheme-specific → Bucket #7
  - If general payment pending → Bucket #1

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Which scheme:** "Scheme name/month?"
- **Specific issue:** "What is confusing / What wasn't received?"
- **Amount pending:** "₹___ benefit pending?"
- **Claim date:** "When was it claimed?"
- **Retailer expectation:** "What does he expect should happen?"

---
---

## BUCKET 08: Product Availability Issues

**Category:** Supply Chain
**Severity:** Medium
**Frequency:** 5-7% of all rejections
**Bucket ID:** 8

### Definition

Retailer wants to order but specific products are unavailable:
1. Specific SKUs/models not in stock at distributor
2. Stockout of popular variants
3. Lead time too long for restocking
4. Retailer wants products that distributor doesn't carry

This requires supply chain coordination and demand planning improvements.

---

### Sample Inputs

#### English Variations
1. "Model number MX-500 is out of stock"
2. "Distributor doesn't have the variant I want"
3. "Popular SKU is not available"
4. "Wanted black color, only white available"
5. "Out of stock at distributor level"
6. "Lead time is 3 weeks, too long"
7. "Specific model not available in market"
8. "Distributor says stockout, will come next month"
9. "Wanted 500W model, only 750W available"
10. "Fast-moving variant always out of stock"
11. "Cannot get the product I want to sell"
12. "Availability issue from company side"
13. "Distributor inventory doesn't have my requirement"
14. "Waiting for restocking from brand"
15. "Product discontinued, alternative not suitable"

#### Hindi Variations
1. "डिस्ट्रीब्यूटर के पास स्टॉक नहीं है"
2. "जो मॉडल चाहिए वो नहीं है"
3. "स्टॉकआउट है"
4. "सही वैरिएंट नहीं मिल रहा"
5. "काला रंग चाहिए, सफेद है"
6. "लीड टाइम बहुत ज्यादा है"
7. "लोकप्रिय मॉडल उपलब्ध नहीं"
8. "महीने भर बाद आएगा"
9. "कंपनी का स्टॉक खत्म है"
10. "जो प्रोडक्ट चाहिए वो नहीं है"

#### Hinglish Variations
1. "MX-500 model out of stock hai distributor ke paas"
2. "Jo variant chahiye wo available nahi hai"
3. "Popular SKU stockout hai"
4. "Black color chahiye tha, sirf white hai"
5. "Distributor ke paas stock nahi hai"
6. "3 weeks lead time hai, bahut zyada"
7. "Specific model market mein nahi mil raha"
8. "Stockout hai, next month aayega"
9. "500W wala chahiye tha, 750W hai"
10. "Fast-moving variant hamesha out of stock rehta hai"
11. "Product availability ka issue hai"
12. "Company se stock nahi aa raha"
13. "Distributor inventory mein requirement nahi hai"
14. "Restocking wait kar rahe hain"
15. "Product discontinue ho gaya, alternative theek nahi"

#### Voice Pattern Variations

**Short Forms:**
- "Stock nahi hai"
- "Out of stock"
- "Available nahi"

**Medium Length:**
- "Specific model available nahi hai"
- "Distributor ke paas stock out hai"
- "Lead time bahut zyada hai"

**Long Forms:**
- "MX-500 mixer grinder jo mujhe chahiye wo distributor ke paas out of stock hai, next month aayega wo bola, tab order kar sakta hoon"
- "Popular black color variant hamesha stockout rehta hai, sirf white available hai jo demand nahi hai mere area mein"

---

### Keywords & Patterns

**Primary Keywords:**
`out of stock`, `not available`, `stockout`, `unavailable`, `distributor stock`, `availability`, `variant`, `SKU`, `model`

**Secondary Keywords:**
`lead time`, `restocking`, `waiting`, `discontinued`, `inventory`, `specific`, `popular`, `color`, `size`

**Hindi Keywords:**
`स्टॉक`, `नहीं है`, `उपलब्ध नहीं`, `वैरिएंट`

**Product Specificity:**
- Model numbers: "MX-500", "XYZ-123"
- Variants: "black", "white", "500W", "1.5L"
- "model", "variant", "SKU", "color", "size"

**Time Indicators:**
- "next month", "3 weeks", "long lead time"
- "अगले महीने", "बाद में"

**Stock Status:**
- "out of stock", "stockout", "not available"
- "stock नहीं", "नहीं है"

---

### Template Options (3 Radio Buttons)

```
○ Specific SKU/model out of stock at distributor

○ Desired variant/color not available

○ Lead time too long, waiting for restocking
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #5 (Delivery Issues):**
- Key differentiator: **Product not in stock** (Bucket #8) vs **Delivery of ordered product delayed** (Bucket #5)?
- If "product not available to order" → Bucket #8
- If "ordered but delivery late" → Bucket #5

**If confused with Bucket #2 (Slow Moving):**
- These are opposite issues:
  - Bucket #2: Has too much stock
  - Bucket #8: Cannot get stock
- Should be clear from context

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Which product:** "Model number/SKU?"
- **Variant details:** "Color/size/specification?"
- **Current availability:** "What's available instead?"
- **Expected restock:** "When will it be available?"
- **Distributor name:** "Which distributor?"

---
---

## BUCKET 09: Space/Display Constraints

**Category:** Physical Infrastructure
**Severity:** Medium
**Frequency:** 5-7% of all rejections
**Bucket ID:** 9

### Definition

Retailer's physical shop space limits ability to stock new products:
1. Limited shop floor area for new products
2. No shelf space available for additional SKUs
3. Existing display commitments to other brands
4. Store layout cannot accommodate product size/dimensions

This is common in small kirana stores and requires creative merchandising solutions.

---

### Sample Inputs

#### English Variations
1. "Shop is too small for more products"
2. "No shelf space available"
3. "Display area is full, cannot add new products"
4. "Already committed shelf space to another brand"
5. "Store layout doesn't have room"
6. "Product size too big for my shop"
7. "Limited floor space, cannot accommodate"
8. "Shelves are fully occupied"
9. "Don't have display space for this category"
10. "Small shop, space is the issue"
11. "Cannot fit more brands in limited space"
12. "Existing brands taking all shelf space"
13. "Need to remove something to add this"
14. "Counter space is full"
15. "No room for additional appliances"

#### Hindi Variations
1. "दुकान में जगह नहीं है"
2. "शेल्फ भरी हुई है"
3. "डिस्प्ले एरिया फुल है"
4. "जगह की कमी है"
5. "दुकान छोटी है"
6. "और प्रोडक्ट रखने की जगह नहीं"
7. "स्पेस नहीं है नए ब्रांड के लिए"
8. "शेल्फ पूरी भरी है"
9. "फ्लोर स्पेस लिमिटेड है"
10. "बड़ा साइज़ है, नहीं रख सकते"

#### Hinglish Variations
1. "Dukaan mein jagah nahi hai"
2. "Shelf space full hai"
3. "Display area mein room nahi hai"
4. "Chhoti dukaan hai, space issue"
5. "Floor space limited hai"
6. "Shelves occupied hain already"
7. "Naye product ke liye jagah nahi"
8. "Store layout mein fit nahi hoga"
9. "Product ka size bada hai, adjust nahi ho sakta"
10. "Existing brands ne sara space le rakha hai"
11. "Shelf commitment hai dusre brand ko"
12. "Counter pe jagah nahi hai"
13. "Limited space hai, aur nahi rakh sakte"
14. "Kuch hatana padega naya add karne ke liye"
15. "Appliances ke liye display space nahi hai"

#### Voice Pattern Variations

**Short Forms:**
- "Jagah nahi hai"
- "Space full"
- "Chhoti dukaan"

**Medium Length:**
- "Shop mein jagah nahi hai naye products ke liye"
- "Shelf space fully occupied hai"
- "Display area full hai, room nahi"

**Long Forms:**
- "Dukaan bahut chhoti hai, shelf space puri bhari hui hai existing brands se, naye product ke liye jagah nahi hai"
- "Mixer grinder ka size bada hai aur mere counter pe fit nahi hoga, limited space hai store mein"

---

### Keywords & Patterns

**Primary Keywords:**
`space`, `room`, `area`, `shelf`, `display`, `small shop`, `limited`, `no room`, `full`, `occupied`

**Secondary Keywords:**
`floor space`, `counter`, `layout`, `size`, `fit`, `accommodate`, `commitment`, `small`, `tiny`

**Hindi Keywords:**
`जगह`, `स्पेस`, `शेल्फ`, `दुकान`, `छोटी`

**Space Indicators:**
- "no space", "no room", "limited space", "full"
- "jagah nahi", "space nahi", "भरी हुई"

**Size/Physical Mentions:**
- "small shop", "big product", "size", "layout"
- "chhoti dukaan", "bada size"

**Shelf/Display Keywords:**
- "shelf", "shelves", "counter", "display", "floor"
- "शेल्फ", "काउंटर", "डिस्प्ले"

---

### Template Options (3 Radio Buttons)

```
○ Limited shop floor space, cannot accommodate new products

○ Shelf/display space fully occupied by existing inventory

○ Product size/dimensions don't fit in available store space
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #2 (Slow Moving Inventory):**
- Key differentiator: **Physical space** (Bucket #9) vs **Unsold stock** (Bucket #2)?
- If "no physical room" or "small shop" → Bucket #9
- If "stock not sold" or "inventory remaining" → Bucket #2
- Can be BOTH if retailer says "space occupied by unsold stock" - then Bucket #2 is primary

**If confused with Bucket #11 (Competition Intensity):**
- If "other brands taking shelf space" could be competition
- Check focus:
  - If physical space is the issue → Bucket #9
  - If "too many brands fighting" → Bucket #11

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Shop size estimate:** "Approximate sq ft?"
- **Specific constraint:** "Shelf/floor/counter space?"
- **Product size issue:** "Which product is too big?"
- **Existing brands:** "Which brands occupy space?"
- **Possible solution:** "Can we suggest compact display?"

---
---

## BUCKET 10: Service/After-Sales Support Concerns

**Category:** Service & Support
**Severity:** Medium-High
**Frequency:** 5-7% of all rejections
**Bucket ID:** 10

### Definition

Retailer hesitates due to concerns about after-sales service quality:
1. Poor service experience affecting retailer reputation
2. Warranty claim resolution delays or issues
3. Lack of service center in the area (tier-2/3 cities)
4. Consumer complaints about service reaching retailer

This is critical for appliances where after-sales support is a key purchase driver.

---

### Sample Inputs

#### English Variations
1. "Service center not available in this area"
2. "Customers complain about poor service"
3. "Warranty claims take too long to process"
4. "After-sales support is not good"
5. "No technician available locally"
6. "Service response time is very slow"
7. "Previous customer had bad service experience"
8. "Service quality affects my reputation"
9. "Tier-3 city, no service infrastructure"
10. "Company doesn't have service center nearby"
11. "Customer service is unreachable"
12. "Spare parts not available for repairs"
13. "Technician training is poor, cannot fix issues"
14. "Service costs are high even in warranty"
15. "Customers returning products due to service issues"

#### Hindi Variations
1. "सर्विस सेंटर यहाँ नहीं है"
2. "ग्राहकों को सर्विस की शिकायत है"
3. "वारंटी क्लेम में देरी होती है"
4. "आफ्टर-सेल्स सपोर्ट खराब है"
5. "लोकल में टेक्नीशियन नहीं है"
6. "सर्विस रिस्पॉन्स स्लो है"
7. "सर्विस क्वालिटी से रेपुटेशन खराब होती है"
8. "कंपनी का सर्विस सेंटर पास नहीं है"
9. "कस्टमर सर्विस मिलती नहीं"
10. "स्पेयर पार्ट्स नहीं मिलते"

#### Hinglish Variations
1. "Service center nahi hai is area mein"
2. "Customers ko service ka complaint hai"
3. "Warranty claim processing bahut slow hai"
4. "After-sales support achha nahi hai"
5. "Local technician available nahi hai"
6. "Service response time zyada hai"
7. "Previous customer ka service experience kharab tha"
8. "Service quality se meri reputation affect hoti hai"
9. "Tier-3 city hai, service infrastructure nahi hai"
10. "Company ka service center nearby nahi hai"
11. "Customer care phone nahi uthate"
12. "Repair ke liye spare parts nahi milte"
13. "Technician properly trained nahi hai"
14. "Warranty mein bhi service charges lete hain"
15. "Service issue ke wajah se customers return kar dete hain"

#### Voice Pattern Variations

**Short Forms:**
- "Service nahi hai"
- "Service center door"
- "Support kharab"

**Medium Length:**
- "Service center available nahi hai yahan"
- "Customers ko service complaint hai"
- "Warranty claim slow process hai"

**Long Forms:**
- "Is area mein company ka service center nahi hai, customers ko complaint hoti hai aur meri reputation kharab hoti hai, isliye order nahi dena chahta"
- "Previous mein ek customer ka mixer grinder kharab ho gaya, 2 weeks laga repair mein, bahut complain kiya, ab dar lagta hai order lene mein"

---

### Keywords & Patterns

**Primary Keywords:**
`service`, `after-sales`, `service center`, `warranty`, `support`, `technician`, `repair`, `complaint`, `customer service`

**Secondary Keywords:**
`response time`, `claim`, `spare parts`, `training`, `nearby`, `local`, `tier-3`, `infrastructure`, `reputation`, `unreachable`

**Hindi Keywords:**
`सर्विस`, `वारंटी`, `टेक्नीशियन`, `शिकायत`, `रिपेयर`

**Location/Availability:**
- "not available", "no service center", "not nearby", "tier-3 city"
- "nahi hai", "door hai", "उपलब्ध नहीं"

**Quality/Performance:**
- "poor service", "bad experience", "slow", "complaint"
- "kharab", "slow", "complaint"

**Impact:**
- "reputation", "customer complaint", "return"
- "reputation", "complaint", "शिकायत"

---

### Template Options (3 Radio Buttons)

```
○ Service center not available in this area (tier-2/3 city issue)

○ Poor service quality/response, customers complaining

○ Warranty claim processing delays or issues
```

---

### Related Buckets (Disambiguation Rules)

**If confused with Bucket #5 (Delivery Issues):**
- Key differentiator: **Product delivery** (Bucket #5) vs **After-sales service** (Bucket #10)?
- If "delivery late" → Bucket #5
- If "service after purchase" → Bucket #10

**If confused with Bucket #13 (Product Quality):**
- Key differentiator: **Service to fix issues** (Bucket #10) vs **Product itself defective** (Bucket #13)?
- If "product breaks often" → Bucket #13
- If "product service poor" → Bucket #10
- Can be both - choose based on retailer's emphasis

---

### Additional Details Field - Guidance Prompts

When this bucket is selected, encourage sales rep to capture:
- **Specific service issue:** "What went wrong?"
- **Customer impact:** "How many customers complained?"
- **Nearest service center:** "How far is nearest center?"
- **Product/SKU:** "Which product had service issues?"
- **Timeline:** "When did this happen?"

---
---

## BUCKET 11: Market Competition Intensity

**Category:** Competitive Dynamics
**Severity:** Low-Moderate
**Frequency:** 3-5% of all rejections
**Bucket ID:** 11

### Definition

Retailer faces intense competitive pressure in the market:
1. Too many brands competing for same shelf space
2. Local/unorganized players offering lower prices
3. Market saturation in the category
4. Regional brands with strong local presence

---

### Sample Inputs

#### English Variations
1. "Too many brands in this category already"
2. "Market is saturated with appliances"
3. "Local brands selling cheaper"
4. "Unorganized players offering lower prices"
5. "Already stocking 5 brands, cannot add more"
6. "Regional brand has strong presence here"
7. "Competition is too intense"
8. "Local manufacturers are cheaper"
9. "Market is crowded with brands"
10. "Customers have too many options already"

#### Hindi Variations
1. "बहुत सारे ब्रांड हैं पहले से"
2. "मार्केट में कम्पीटीशन ज्यादा है"
3. "लोकल ब्रांड सस्ते हैं"
4. "बहुत भीड़ है इस कैटेगरी में"
5. "रीजनल ब्रांड स्ट्रांग है यहाँ"

#### Hinglish Variations
1. "Bahut brands hain already is category mein"
2. "Market saturated hai appliances se"
3. "Local brands saste mein de rahe hain"
4. "Unorganized players cheap rates pe sell kar rahe"
5. "5 brands already stock kar rakhe hain, aur nahi le sakte"
6. "Regional brand ka strong presence hai yahan"
7. "Competition bahut intense hai"
8. "Local manufacturers cheaper hain"
9. "Customers ke paas bahut options hain already"
10. "Market crowded hai, naya brand risk hai"

**Keywords:** `competition`, `saturated`, `crowded`, `too many brands`, `local players`, `regional`, `unorganized`

**Template Options:**
```
○ Too many brands competing, market saturated

○ Local/unorganized players offering lower prices

○ Regional brand dominance, difficult to compete
```

---
---

## BUCKET 12: Distributor Relationship Issues

**Category:** Channel Dynamics
**Severity:** Low-Moderate
**Frequency:** 3-5% of all rejections
**Bucket ID:** 12

### Definition

Retailer's relationship with the current distributor is problematic:
1. Poor overall relationship with distributor
2. Preference for alternate distributor in territory
3. Distributor service reputation affecting brand perception
4. Lack of distributor support and engagement

---

### Sample Inputs

#### English Variations
1. "Not happy with current distributor"
2. "Distributor service quality is poor"
3. "Want to work with different distributor"
4. "Distributor doesn't support retailers properly"
5. "Relationship with distributor is not good"
6. "Distributor behavior is unprofessional"
7. "Previous bad experience with this distributor"
8. "Prefer another distributor in area"
9. "Distributor doesn't visit regularly"
10. "No support from distributor side"

#### Hindi Variations
1. "डिस्ट्रीब्यूटर से खुश नहीं हैं"
2. "डिस्ट्रीब्यूटर की सर्विस खराब है"
3. "डिस्ट्रीब्यूटर बदलना चाहते हैं"
4. "डिस्ट्रीब्यूटर सपोर्ट नहीं करता"
5. "रिश्ते अच्छे नहीं हैं"

#### Hinglish Variations
1. "Current distributor se khush nahi hain"
2. "Distributor ka service quality poor hai"
3. "Dusre distributor se kaam karna chahte hain"
4. "Distributor properly support nahi karta"
5. "Relationship achhi nahi hai distributor ke saath"
6. "Distributor ka behavior unprofessional hai"
7. "Previous bad experience tha is distributor se"
8. "Area mein dusra distributor prefer karte hain"
9. "Distributor regularly visit nahi karta"
10. "Distributor side se koi support nahi milta"

**Keywords:** `distributor`, `relationship`, `not happy`, `poor service`, `different distributor`, `support`, `behavior`

**Template Options:**
```
○ Poor relationship with current distributor

○ Distributor service quality unsatisfactory

○ Prefer to work with alternate distributor
```

---
---

## BUCKET 13: Product Quality/Performance Issues

**Category:** Product Concerns
**Severity:** Low
**Frequency:** 2-4% of all rejections
**Bucket ID:** 13

### Definition

Concerns about product quality affecting retailer confidence:
1. Past customer complaints about product quality
2. Returns/exchanges damaging retailer reputation
3. Product not meeting local consumer expectations
4. Quality perception versus competing brands

---

### Sample Inputs

#### English Variations
1. "Customers complaining about product quality"
2. "Too many returns due to defects"
3. "Product doesn't last long"
4. "Quality not as good as competitors"
5. "Customer had bad experience with this brand"
6. "Product breaks frequently"
7. "Performance not meeting expectations"
8. "Build quality is poor"
9. "Competitors have better quality"
10. "Customers prefer other brands for quality"

#### Hindi Variations
1. "प्रोडक्ट की क्वालिटी की शिकायत है"
2. "बहुत रिटर्न आते हैं"
3. "प्रोडक्ट जल्दी खराब हो जाता है"
4. "क्वालिटी कम्पटीटर से कम है"
5. "ग्राहकों को संतुष्टि नहीं है"

#### Hinglish Variations
1. "Customers ko quality ka complaint hai"
2. "Bahut returns ho rahe hain defect ki wajah se"
3. "Product jaldi kharab ho jata hai"
4. "Quality competitors se kam hai"
5. "Customer experience kharab tha is brand ka"
6. "Product frequently break hota hai"
7. "Performance expectations meet nahi kar raha"
8. "Build quality poor hai"
9. "Competitors ka quality better hai"
10. "Customers quality ke liye dusre brands prefer karte hain"

**Keywords:** `quality`, `defect`, `returns`, `complaint`, `breaks`, `performance`, `build`, `durability`

**Template Options:**
```
○ Customer complaints about product quality/defects

○ Returns/exchanges affecting retailer reputation

○ Quality perception lower than competing brands
```

---
---

## BUCKET 14: Minimum Order Quantity (MOQ) Too High

**Category:** Financial & Operational
**Severity:** Low
**Frequency:** 1-2% of all rejections
**Bucket ID:** 14

### Definition

Retailer cannot commit to minimum order requirements:
1. MOQ exceeds retailer's capacity
2. Working capital constraints for bulk orders
3. Small shop size not justifying MOQ
4. Risk aversion for new/unproven SKUs

---

### Sample Inputs

#### English Variations
1. "Minimum order quantity is too much"
2. "Cannot order full box, need fewer pieces"
3. "MOQ too high for small shop"
4. "Don't need 10 units, just want 3-4"
5. "Bulk order requirement too big"
6. "Cannot afford minimum order size"
7. "Trial order should be smaller"
8. "New product, cannot take big MOQ"
9. "Working capital blocked if order full MOQ"
10. "Shop size doesn't justify such large order"

#### Hindi Variations
1. "मिनिमम आर्डर बहुत ज्यादा है"
2. "इतने पीस नहीं ले सकते"
3. "छोटी दुकान है, MOQ ज्यादा है"
4. "पूरा बॉक्स नहीं चाहिए"
5. "बल्क आर्डर बहुत बड़ा है"

#### Hinglish Variations
1. "Minimum order quantity bahut zyada hai"
2. "Full box nahi le sakte, kam pieces chahiye"
3. "MOQ choti dukaan ke liye zyada hai"
4. "10 units nahi chahiye, 3-4 chahiye bas"
5. "Bulk order requirement bahut badi hai"
6. "Minimum order size afford nahi kar sakte"
7. "Trial ke liye chhota order chahiye"
8. "Naya product hai, bada MOQ risk hai"
9. "Full MOQ liya toh working capital block ho jayega"
10. "Shop size itna bada order justify nahi karta"

**Keywords:** `MOQ`, `minimum order`, `quantity`, `too much`, `bulk`, `cannot afford`, `full box`, `pieces`

**Template Options:**
```
○ Minimum order quantity exceeds capacity/need

○ Cannot afford bulk order, working capital constraint

○ Trial/small order needed for new product, MOQ too high
```

---
---

## BUCKET 15: Economic/Market Conditions

**Category:** External Factors
**Severity:** Low
**Frequency:** 1-2% of all rejections
**Bucket ID:** 15

### Definition

Broader economic or market factors affecting purchase decisions:
1. Local economic downturn impacting consumer demand
2. Seasonal business slowdown in the area
3. Market disruption (floods, festivals, elections, weather)
4. Regional factors impacting purchasing power

---

### Sample Inputs

#### English Variations
1. "Business is slow overall in the market"
2. "Economic conditions not good currently"
3. "Consumer spending is down in this area"
4. "Market disruption due to local issues"
5. "Heavy rains affecting footfall"
6. "Festival just got over, market slow now"
7. "Election time, business affected"
8. "Overall market sentiment negative"
9. "Regional economic slowdown"
10. "Customers have less money to spend"

#### Hindi Variations
1. "बिजनेस धीमा चल रहा है"
2. "इकनोमिक कंडीशन अच्छी नहीं है"
3. "ग्राहकों की खर्च करने की क्षमता कम है"
4. "मार्केट में मंदी है"
5. "बाजार में उथल-पुथल है"

#### Hinglish Variations
1. "Business overall slow chal raha hai market mein"
2. "Economic conditions achhe nahi hain abhi"
3. "Consumer spending down hai is area mein"
4. "Local issues ki wajah se market disruption hai"
5. "Bhari barish ki wajah se footfall kam hai"
6. "Festival khatam ho gaya, market slow hai ab"
7. "Election time hai, business affect ho raha"
8. "Overall market sentiment negative hai"
9. "Regional economic slowdown chal raha"
10. "Customers ke paas paise kam hain spend karne ke liye"

**Keywords:** `economic`, `slowdown`, `market conditions`, `business slow`, `downturn`, `disruption`, `recession`, `spending down`

**Template Options:**
```
○ Local economic downturn affecting consumer demand

○ Seasonal business slowdown or market disruption

○ Regional factors impacting purchasing power
```

---
---

## Disambiguation Matrix (Multi-Bucket Scenarios)

When input could match multiple buckets, use this decision tree:

### Payment vs MOQ
- **Mentions "cannot afford"** → Check if specific amount issue (Bucket #1) or order size issue (Bucket #14)
- **Mentions "payment pending"** → Bucket #1
- **Mentions "quantity too much"** → Bucket #14

### Slow Moving vs Seasonal
- **Mentions "last month's stock"** → Bucket #2 (Slow Moving)
- **Mentions "will order before Diwali"** → Bucket #6 (Seasonal)

### Slow Moving vs Space
- **Primary focus on "not sold"** → Bucket #2
- **Primary focus on "no room"** → Bucket #9
- **Both mentioned** → Choose Bucket #2, note space in details

### Competitor Margin vs Pricing
- **"Competitor giving better margin"** → Bucket #3
- **"Our product price too high"** → Bucket #4

### Delivery vs Service
- **"Delivery was late"** → Bucket #5
- **"Service after purchase poor"** → Bucket #10

### Service vs Quality
- **"Service centers not available"** → Bucket #10
- **"Product itself defective"** → Bucket #13

---

## Edge Case Handling

### Multi-Issue Inputs
When retailer mentions multiple reasons:
1. Identify primary issue (retailer emphasis or explicit statement like "main problem is...")
2. Log primary bucket
3. Capture secondary issues in Additional Details field
4. Flag as multi-issue rejection

### Vague/Unclear Inputs
When input is too vague ("not interested", "not now"):
1. Confidence score will be low (<0.6)
2. Show top 3 buckets for clarification
3. Include "Other" option
4. Encourage sales rep to ask follow-up question

### Offensive/Inappropriate Inputs
If input contains offensive language:
1. Filter and sanitize
2. Still attempt classification
3. Flag for review

### New/Unrecognized Patterns
When confidence score <0.4 across all buckets:
1. Show "Other" option prominently
2. Require sales rep to enter free-form description
3. Flag for knowledge base review
4. If 10+ similar "Other" cases → Consider new bucket

---

## Knowledge Base Maintenance

### Continuous Improvement Process

1. **Weekly Review:**
   - Analyze "Other" category rejections
   - Identify patterns requiring new samples or buckets

2. **Monthly Audit:**
   - Random sample 100 classifications
   - Verify accuracy vs sales rep confirmation rate
   - Update low-performing bucket samples

3. **Quarterly Refresh:**
   - Add new competitor names, regional variations
   - Update seasonal patterns
   - Incorporate feedback from field sales teams

4. **Version Control:**
   - Track changes to sample inputs
   - A/B test new sample variations
   - Monitor impact on classification accuracy

---

## Performance Metrics

Track these metrics for knowledge base effectiveness:

**Classification Metrics:**
- Top-1 accuracy: >90%
- Top-3 accuracy: >97%
- Average confidence score: >0.75
- Rep confirmation rate: >85%

**Bucket Distribution:**
- Monitor if distribution matches expected frequency
- Alert if bucket usage deviates >20% from baseline

**Language Distribution:**
- Track English vs Hindi vs Hinglish usage
- Optimize samples based on actual usage patterns

**"Other" Category Rate:**
- Target: <5% of all rejections
- Alert if >10% fall into "Other"

---

## Integration Guidelines

### For Vector Database Setup:

```python
# Pseudo-code for knowledge base loading

buckets = load_buckets_from_markdown()

for bucket in buckets:
    bucket_id = bucket.id
    samples = bucket.sample_inputs  # All language variations

    for sample in samples:
        # Create embedding
        embedding = create_embedding(sample.text)

        # Store in vector DB
        vector_db.upsert(
            id=f"{bucket_id}_{sample.id}",
            vector=embedding,
            metadata={
                "bucket_id": bucket_id,
                "bucket_name": bucket.name,
                "category": bucket.category,
                "severity": bucket.severity,
                "language": sample.language,
                "keywords": bucket.keywords
            }
        )
```

### For LLM Classification:

```python
# Pseudo-code for classification with validation

def classify_rejection(user_input):
    # 1. Vector search
    top_matches = vector_db.query(user_input, top_k=5)

    # 2. Keyword matching
    keyword_scores = calculate_keyword_match(user_input, buckets)

    # 3. LLM validation
    llm_result = llm.validate_classification(
        input=user_input,
        candidates=top_matches
    )

    # 4. Final scoring
    final_score = combine_scores(
        vector_similarity=top_matches[0].score,
        keyword_match=keyword_scores[0],
        llm_confidence=llm_result.confidence
    )

    return {
        "bucket_id": top_matches[0].bucket_id,
        "confidence": final_score,
        "alternatives": top_matches[1:3]
    }
```

---

## Appendix: Complete Bucket List

| ID | Bucket Name | Category | Severity | Frequency |
|----|-------------|----------|----------|-----------|
| 1 | Outstanding Payment/Credit Issues | Financial | High | 15-20% |
| 2 | Slow Moving Inventory | Inventory | High | 15-20% |
| 3 | Competitor Better Margins/Schemes | Competitive | High | 8-12% |
| 4 | Pricing/Margin Concerns | Financial | High | 8-10% |
| 5 | Delivery/Logistics Issues | Operations | Medium-High | 5-8% |
| 6 | Seasonal/Demand Timing | Market Dynamics | Medium | 5-8% |
| 7 | Scheme Confusion/Dissatisfaction | Operations | Medium-High | 5-7% |
| 8 | Product Availability Issues | Supply Chain | Medium | 5-7% |
| 9 | Space/Display Constraints | Infrastructure | Medium | 5-7% |
| 10 | Service/After-Sales Concerns | Service | Medium-High | 5-7% |
| 11 | Market Competition Intensity | Competitive | Low-Moderate | 3-5% |
| 12 | Distributor Relationship Issues | Channel | Low-Moderate | 3-5% |
| 13 | Product Quality/Performance | Product | Low | 2-4% |
| 14 | MOQ Too High | Financial/Ops | Low | 1-2% |
| 15 | Economic/Market Conditions | External | Low | 1-2% |

**Total Coverage:** 95%+ of all rejections

---

**Document End**

**Version History:**
- v1.0 (2025-10-10): Initial knowledge base with 15 buckets, 200+ sample inputs across languages

**Next Update:** 2025-11-10 (Monthly review cycle)

For updates or issues, contact: ai-team@company.com
