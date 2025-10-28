# Order Rejection System - Engineering Implementation Guide

**Version:** 1.0
**Date:** 2025-10-10
**Purpose:** Detailed technical implementation specifications for engineering team
**Parent Document:** Order_Rejection_System_Design.md

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     FRONTEND LAYER                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ Mobile App   │  │ WhatsApp Bot │  │ Web Dashboard│     │
│  │ (React Native)│  │ (Twilio/WA  │  │ (Analytics)  │     │
│  └──────────────┘  │  Business API)│  └──────────────┘     │
│         │           └──────────────┘          │             │
└─────────┼──────────────────┼─────────────────┼─────────────┘
          │                  │                  │
          │                  ▼                  │
          │         ┌────────────────┐          │
          └────────►│  API GATEWAY   │◄─────────┘
                    │  (REST/GraphQL)│
                    └────────────────┘
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
┌──────────────────┐  ┌──────────────┐  ┌──────────────┐
│  ORCHESTRATION   │  │   AI/ML      │  │   DATA       │
│     SERVICE      │  │  PIPELINE    │  │  SERVICE     │
├──────────────────┤  ├──────────────┤  ├──────────────┤
│ - Input routing  │  │ - STT (Whisper)│ │ - PostgreSQL │
│ - Workflow mgmt  │  │ - Translation │  │ - Vector DB  │
│ - Error handling │  │ - RAG Engine  │  │ - Redis Cache│
│ - Auth/Security  │  │ - LLM (GPT-4)│  │ - S3 Storage │
└──────────────────┘  └──────────────┘  └──────────────┘
          │                 │                 │
          └─────────────────┼─────────────────┘
                            │
                            ▼
                ┌────────────────────────┐
                │  ANALYTICS & REPORTING │
                │  - Power BI / Tableau  │
                │  - Real-time dashboards│
                └────────────────────────┘
```

---

## API Endpoints Specification

### 1. Submit Rejection Input

```http
POST /api/v1/rejection/submit-input
Content-Type: application/json
Authorization: Bearer {token}

Request Body:
{
  "sales_rep_id": "SR_789",
  "retailer_id": "RET_Mumbai_1234",
  "input_method": "voice" | "text",
  "input_language": "en" | "hi" | "hinglish" | "auto-detect",
  "input_data": "base64_encoded_audio" | "text_string",
  "geo_location": {
    "latitude": 19.0760,
    "longitude": 72.8777
  },
  "timestamp": "2025-10-10T11:23:45Z"
}

Response:
{
  "session_id": "SESSION_12345",
  "transcription": "Dukaan mein stock pada hai...",
  "detected_language": "hinglish",
  "status": "processing"
}
```

### 2. Get Classification Results

```http
GET /api/v1/rejection/classification/{session_id}
Authorization: Bearer {token}

Response:
{
  "session_id": "SESSION_12345",
  "confidence_level": "high" | "medium" | "low",
  "confidence_score": 0.89,
  "primary_bucket": {
    "bucket_id": 2,
    "bucket_name": "Slow Moving Inventory",
    "category": "Inventory Management",
    "severity": "High"
  },
  "alternative_buckets": [
    {
      "bucket_id": 9,
      "bucket_name": "Space Constraints",
      "confidence": 0.61
    }
  ],
  "requires_clarification": false,
  "clarification_options": []
}
```

### 3. Get Template Options

```http
GET /api/v1/rejection/templates/{bucket_id}
Authorization: Bearer {token}

Response:
{
  "bucket_id": 2,
  "bucket_name": "Slow Moving Inventory",
  "templates": [
    {
      "template_id": "T2_1",
      "text": "High inventory, products not moving fast enough",
      "placeholder_fields": []
    },
    {
      "template_id": "T2_2",
      "text": "Previous order stock still available, no space for new stock",
      "placeholder_fields": []
    },
    {
      "template_id": "T2_3",
      "text": "Seasonal products from last season not yet sold",
      "placeholder_fields": []
    }
  ]
}
```

### 4. Submit Final Rejection Record

```http
POST /api/v1/rejection/record
Content-Type: application/json
Authorization: Bearer {token}

Request Body:
{
  "session_id": "SESSION_12345",
  "sales_rep_id": "SR_789",
  "retailer_id": "RET_Mumbai_1234",
  "bucket_id": 2,
  "template_id": "T2_2",
  "additional_details": "10 units mixer grinder MX-500",
  "timestamp": "2025-10-10T11:24:15Z"
}

Response:
{
  "rejection_id": "REJ_2025101001234",
  "status": "recorded",
  "confirmation_message": "Rejection logged successfully for Sharma Electronics"
}
```

### 5. Analytics Query API

```http
POST /api/v1/analytics/rejections
Content-Type: application/json
Authorization: Bearer {token}

Request Body:
{
  "filters": {
    "date_range": {
      "start": "2025-10-01",
      "end": "2025-10-10"
    },
    "sales_rep_id": ["SR_789", "SR_790"],
    "bucket_ids": [1, 2, 3],
    "territory": "Mumbai_West"
  },
  "group_by": ["bucket_id", "date"],
  "metrics": ["count", "percentage", "trend"]
}

Response:
{
  "results": [
    {
      "date": "2025-10-10",
      "bucket_id": 1,
      "bucket_name": "Outstanding Payment/Credit Issues",
      "count": 45,
      "percentage": 22.5,
      "trend": "+5% vs last week"
    }
  ],
  "summary": {
    "total_rejections": 200,
    "top_3_buckets": [1, 2, 3],
    "avg_processing_time_ms": 1850
  }
}
```

---

## Database Schema

### Table: `order_rejections`

```sql
CREATE TABLE order_rejections (
    rejection_id VARCHAR(50) PRIMARY KEY,
    session_id VARCHAR(50) NOT NULL,

    -- Relationships
    sales_rep_id VARCHAR(50) NOT NULL,
    retailer_id VARCHAR(50) NOT NULL,
    distributor_id VARCHAR(50),
    territory_id VARCHAR(50),

    -- Timestamps & Location
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    visit_timestamp TIMESTAMP NOT NULL,
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),

    -- Input Data
    input_method VARCHAR(20), -- voice, text
    input_language VARCHAR(20), -- en, hi, hinglish
    raw_input TEXT,
    transcription TEXT,

    -- Classification
    bucket_id INT,
    bucket_name VARCHAR(100),
    bucket_category VARCHAR(50),
    template_id VARCHAR(20),
    template_text TEXT,
    additional_details TEXT,

    -- AI Metrics
    confidence_score DECIMAL(3, 2),
    confidence_level VARCHAR(20), -- high, medium, low
    clarification_required BOOLEAN DEFAULT FALSE,
    alternative_buckets JSONB,

    -- Processing Metrics
    processing_time_ms INT,
    stt_time_ms INT,
    rag_time_ms INT,

    -- Flags
    requires_manual_review BOOLEAN DEFAULT FALSE,
    flagged_for_kb_update BOOLEAN DEFAULT FALSE,
    is_multi_issue BOOLEAN DEFAULT FALSE,
    secondary_bucket_id INT,

    -- Indexes
    INDEX idx_sales_rep (sales_rep_id),
    INDEX idx_retailer (retailer_id),
    INDEX idx_bucket (bucket_id),
    INDEX idx_date (visit_timestamp),
    INDEX idx_territory (territory_id),

    -- Foreign Keys
    FOREIGN KEY (sales_rep_id) REFERENCES sales_reps(rep_id),
    FOREIGN KEY (retailer_id) REFERENCES retailers(retailer_id),
    FOREIGN KEY (bucket_id) REFERENCES rejection_buckets(bucket_id)
);
```

### Table: `rejection_buckets`

```sql
CREATE TABLE rejection_buckets (
    bucket_id INT PRIMARY KEY,
    bucket_name VARCHAR(100) NOT NULL,
    category VARCHAR(50) NOT NULL,
    severity VARCHAR(20),
    frequency_percentage DECIMAL(5, 2),
    description TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### Table: `rejection_templates`

```sql
CREATE TABLE rejection_templates (
    template_id VARCHAR(20) PRIMARY KEY,
    bucket_id INT NOT NULL,
    template_text TEXT NOT NULL,
    display_order INT,
    is_active BOOLEAN DEFAULT TRUE,
    usage_count INT DEFAULT 0,
    FOREIGN KEY (bucket_id) REFERENCES rejection_buckets(bucket_id)
);
```

---

## AI/ML Pipeline Components

### Component 1: Speech-to-Text (STT)

**Technology:** OpenAI Whisper API or Google Speech-to-Text

```python
# Pseudo-code
def transcribe_audio(audio_data, language_hint=None):
    """
    Convert voice input to text
    """
    response = whisper_api.transcribe(
        audio=audio_data,
        language=language_hint,  # 'hi' for Hindi, 'en' for English
        task='transcribe',
        temperature=0.2  # Lower temperature for consistency
    )

    return {
        'transcription': response.text,
        'detected_language': response.language,
        'confidence': response.confidence,
        'duration_ms': response.duration
    }
```

**Supported Languages:**
- English
- Hindi
- Hinglish (auto-detected, processed as Hindi then translated)

### Component 2: Translation (if needed)

```python
def translate_if_needed(text, source_lang, target_lang='en'):
    """
    Translate non-English input to English for RAG processing
    """
    if source_lang != 'en':
        translation = azure_translate.translate(
            text=text,
            source=source_lang,
            target=target_lang
        )
        return {
            'original': text,
            'translated': translation.text,
            'confidence': translation.confidence
        }
    return {'translated': text}
```

### Component 3: RAG Vector Search

```python
def search_rejection_buckets(input_text, top_k=5):
    """
    Vector similarity search against knowledge base
    """
    # 1. Embed input
    input_embedding = openai.embeddings.create(
        model="text-embedding-3-large",
        input=input_text
    ).data[0].embedding

    # 2. Query vector database
    results = vector_db.query(
        collection="rejection_buckets",
        vector=input_embedding,
        top_k=top_k,
        include_metadata=True
    )

    # 3. Calculate keyword match bonus
    for result in results:
        keyword_score = calculate_keyword_match(
            input_text,
            result.metadata['keywords']
        )
        result.confidence = (0.7 * result.similarity) + (0.3 * keyword_score)

    return sorted(results, key=lambda x: x.confidence, reverse=True)
```

### Component 4: LLM Classification & Validation

```python
def validate_classification(input_text, top_buckets):
    """
    Use LLM to validate and explain classification
    """
    prompt = f"""
    A sales rep reported why a retailer rejected an order:
    "{input_text}"

    Top matching rejection reasons from our database:
    1. {top_buckets[0].name} (confidence: {top_buckets[0].confidence})
    2. {top_buckets[1].name} (confidence: {top_buckets[1].confidence})
    3. {top_buckets[2].name} (confidence: {top_buckets[2].confidence})

    Tasks:
    1. Validate which reason best matches (1, 2, 3, or none)
    2. If multiple issues mentioned, identify primary vs secondary
    3. Provide brief reasoning

    Respond in JSON format.
    """

    response = openai.chat.completions.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.1,
        response_format={"type": "json_object"}
    )

    return json.loads(response.choices[0].message.content)
```

---

## Confidence Score Logic (Detailed)

```python
def calculate_final_confidence(vector_similarity, keyword_match, llm_validation):
    """
    Multi-factor confidence scoring
    """
    # Base score from vector search
    vector_score = vector_similarity  # 0-1

    # Keyword match bonus
    keyword_score = keyword_match  # 0-1

    # LLM validation adjustment
    llm_confidence = llm_validation.get('confidence', 0.5)  # 0-1

    # Weighted combination
    final_score = (
        0.5 * vector_score +
        0.2 * keyword_score +
        0.3 * llm_confidence
    )

    # Categorize confidence level
    if final_score >= 0.8:
        level = 'high'
        action = 'auto_select'
    elif final_score >= 0.6:
        level = 'medium'
        action = 'show_top_2'
    else:
        level = 'low'
        action = 'show_top_3_plus_other'

    return {
        'score': round(final_score, 2),
        'level': level,
        'action': action,
        'breakdown': {
            'vector': vector_score,
            'keyword': keyword_score,
            'llm': llm_confidence
        }
    }
```

---

## Error Handling & Edge Cases

```python
# Pseudo-code for error handling

def handle_rejection_input(request):
    try:
        # 1. Input validation
        if not validate_input(request):
            return error_response("Invalid input format")

        # 2. STT with retry logic
        transcription = retry_with_backoff(
            transcribe_audio,
            max_retries=3,
            audio_data=request.audio
        )

        # 3. RAG search with timeout
        try:
            search_results = timeout_wrapper(
                search_rejection_buckets,
                timeout_seconds=5,
                input_text=transcription.text
            )
        except TimeoutError:
            # Fallback: Show all buckets for manual selection
            return show_manual_bucket_selection()

        # 4. Handle low confidence
        if search_results[0].confidence < 0.4:
            # Too ambiguous - ask rep to rephrase or select manually
            return {
                'status': 'clarification_needed',
                'message': 'Could not understand. Please provide more details.',
                'fallback': 'manual_selection'
            }

        # 5. Success path
        return classification_response(search_results)

    except Exception as e:
        # Log error, notify team, provide graceful fallback
        log_error(e, request)
        notify_engineering_team(e)
        return {
            'status': 'error',
            'message': 'System error. Please manually select rejection reason.',
            'fallback_ui': render_manual_selection_ui()
        }
```

---

## Testing & Validation

### Test Cases by Component

#### 1. Speech-to-Text Accuracy

**Test Scenarios:**
- Clear English input (accent variations)
- Clear Hindi input
- Hinglish mix
- Noisy environment (background noise)
- Multiple speakers
- Different audio quality (phone mic vs professional mic)

**Acceptance Criteria:**
- >95% accuracy for clear audio
- >85% accuracy for noisy environment
- <2 seconds processing time for 10-second audio

#### 2. RAG Classification Accuracy

**Test Dataset:** 500 pre-labeled real rejection reasons

**Metrics:**
- **Accuracy:** % of correct bucket classifications
- **Top-3 Accuracy:** % where correct bucket is in top 3
- **Confidence Calibration:** How well confidence scores match actual accuracy

**Acceptance Criteria:**
- >90% Top-1 accuracy
- >97% Top-3 accuracy
- Confidence score correlation >0.85

**Test Cases by Bucket:**

| Bucket ID | Bucket Name | Test Cases | Expected Accuracy |
|-----------|-------------|------------|-------------------|
| 1 | Payment/Credit Issues | 50 | >95% |
| 2 | Slow Moving Inventory | 50 | >90% |
| 3 | Competitor Margins | 40 | >85% |
| 4 | Pricing Concerns | 40 | >85% |
| 5 | Delivery/Logistics | 35 | >85% |
| 6 | Seasonal Timing | 30 | >80% |
| 7 | Scheme Confusion | 30 | >80% |
| 8 | Product Availability | 30 | >85% |
| 9 | Space Constraints | 30 | >85% |
| 10 | Service Concerns | 30 | >85% |
| 11-15 | Edge Cases | 35 | >75% |

#### 3. End-to-End User Flow Testing

**Scenario Tests:**
1. **Happy path** - Clear input, high confidence, successful recording
2. **Ambiguous input** - Low confidence, clarification flow
3. **Multi-issue input** - Multiple reasons, primary identification
4. **Voice input variations** - Different languages, accents
5. **Error recovery** - Network failure, timeout, retry logic
6. **Edge cases** - Completely new reason, offensive input, gibberish

**Performance Benchmarks:**
- End-to-end completion time: <15 seconds (median)
- API response time: <500ms (p95)
- STT processing: <2 seconds
- RAG classification: <1 second
- Database write: <200ms

#### 4. Load Testing

**Scenarios:**
- **Peak load:** 1000 concurrent users (100 sales reps × 10 visits/hour)
- **Sustained load:** 500 concurrent users for 8 hours
- **Spike test:** 0 to 2000 users in 1 minute

**Acceptance Criteria:**
- No degradation in response time under normal load
- <5% increase in response time under 2x load
- Graceful degradation under extreme load (queue instead of fail)

#### 5. Data Quality Validation

**Audit Process:**
- Random sample 100 rejections daily
- Manual review by sales manager
- Compare AI classification vs human judgment
- Flag discrepancies for knowledge base improvement

**Quality Metrics:**
- Agreement rate: >90%
- Additional details fill rate: >70%
- Actionability score: >85% (rejection data leads to specific action)

---

**Document End**

For questions or feedback, contact: engineering-team@company.com
