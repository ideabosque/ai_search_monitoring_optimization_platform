# GEO/AI Search Monitoring Platform - Entity-Relationship Diagrams

**Created:** December 26, 2025
**Purpose:** Comprehensive data model and database schema documentation

**GraphRAG Alignment:** Entity relationships are designed to support Neo4j GraphRAG retrieval, with core entities and their links optimized for graph traversal and embedding-aware queries.

---

## Table of Contents

1. [Complete ER Diagram](#1-complete-er-diagram)
2. [Core Domain Entities](#2-core-domain-entities)
3. [Monitoring & Collection Entities](#3-monitoring--collection-entities)
4. [Analysis & Scoring Entities](#4-analysis--scoring-entities)
5. [Action & Optimization Entities](#5-action--optimization-entities)
6. [AI Agent Framework Entities](#6-ai-agent-framework-entities)
7. [Platform & Governance Entities](#7-platform--governance-entities)
8. [Detailed Entity Specifications](#8-detailed-entity-specifications)

---

## 1. Complete ER Diagram

### 1.1 High-Level Entity Relationships

```mermaid
erDiagram
    WORKSPACE ||--o{ USER : "has members"
    WORKSPACE ||--o{ TOPIC : contains
    WORKSPACE ||--o{ BRAND : owns
    WORKSPACE ||--o{ QUERY_PLAN : defines
    WORKSPACE ||--o{ SUBSCRIPTION : "has billing"

    USER ||--o{ QUERY_PLAN : creates
    USER ||--o{ EXPERIMENT : designs
    USER ||--o{ ALERT_SUBSCRIPTION : "subscribes to"

    TOPIC ||--o{ PROMPT : contains
    QUERY_PLAN ||--o{ PROMPT : monitors
    QUERY_PLAN ||--o{ ENGINE : targets
    QUERY_PLAN ||--o{ COLLECTION_JOB : generates

    COLLECTION_JOB ||--|| ENGINE : "queries via"
    COLLECTION_JOB ||--o{ RAW_ARTIFACT : produces
    RAW_ARTIFACT ||--|| ANSWER_RECORD : "parsed into"

    ANSWER_RECORD ||--o{ CITATION : contains
    ANSWER_RECORD }o--|| PROMPT : "response to"
    ANSWER_RECORD }o--|| ENGINE : "from"

    CITATION }o--|| DOMAIN : "links to"
    DOMAIN }o--|| BRAND : "owned by"

    BRAND ||--o{ BRAND_ALIAS : "known as"
    BRAND ||--o{ SCORE_SNAPSHOT : measured

    SCORE_SNAPSHOT }o--|| TOPIC : "for"
    SCORE_SNAPSHOT }o--|| ENGINE : "on"
    SCORE_SNAPSHOT }o--|| BRAND : "about"

    ANSWER_RECORD ||--o{ AUDIT_RESULT : triggers
    DOMAIN ||--o{ AUDIT_RESULT : audited
    AUDIT_RESULT ||--o{ AUDIT_ISSUE : identifies

    AUDIT_ISSUE ||--o{ RECOMMENDATION : generates
    RECOMMENDATION ||--o{ ACTION_ITEM : "creates"
    ACTION_ITEM }o--|| USER : "assigned to"

    EXPERIMENT ||--o{ EXPERIMENT_VARIANT : tests
    EXPERIMENT_VARIANT ||--o{ ANSWER_RECORD : collects
    EXPERIMENT ||--|| EXPERIMENT_RESULT : produces

    ALERT_RULE ||--o{ ALERT : triggers
    ALERT }o--|| USER : "notifies"
    ALERT_SUBSCRIPTION }o--|| ALERT_RULE : "subscribes to"

    WORKSPACE {
        uuid workspace_id PK
        string name
        string slug
        timestamp created_at
        timestamp updated_at
        json settings
        string tier
    }

    USER {
        uuid user_id PK
        uuid workspace_id FK
        string email
        string name
        string role
        timestamp last_login
        json preferences
    }

    TOPIC {
        uuid topic_id PK
        uuid workspace_id FK
        string name
        string description
        json metadata
        timestamp created_at
    }

    PROMPT {
        uuid prompt_id PK
        uuid topic_id FK
        string text
        string locale
        string intent
        json variants
        timestamp created_at
    }

    BRAND {
        uuid brand_id PK
        uuid workspace_id FK
        string name
        json domains
        json properties
        timestamp created_at
    }

    QUERY_PLAN {
        uuid plan_id PK
        uuid workspace_id FK
        uuid created_by FK
        string name
        json engines
        json prompts
        string cadence
        json budget_cap
        string status
        timestamp created_at
    }

    ENGINE {
        string engine_id PK
        string name
        string type
        json config
        string status
    }

    COLLECTION_JOB {
        uuid job_id PK
        uuid plan_id FK
        uuid prompt_id FK
        string engine_id FK
        string status
        timestamp scheduled_at
        timestamp started_at
        timestamp completed_at
        int retry_count
        json metadata
    }

    RAW_ARTIFACT {
        uuid artifact_id PK
        uuid job_id FK
        string s3_key
        string content_type
        bigint size_bytes
        string checksum
        timestamp created_at
    }

    ANSWER_RECORD {
        uuid answer_id PK
        uuid job_id FK
        uuid prompt_id FK
        string engine_id FK
        text answer_text
        string answer_type
        json metadata
        timestamp captured_at
    }

    CITATION {
        uuid citation_id PK
        uuid answer_id FK
        uuid domain_id FK
        string url
        int position
        boolean first_party
        string anchor_text
        json metadata
    }

    DOMAIN {
        uuid domain_id PK
        string domain_name
        uuid brand_id FK
        boolean verified
        json metadata
    }

    SCORE_SNAPSHOT {
        uuid score_id PK
        uuid workspace_id FK
        uuid brand_id FK
        uuid topic_id FK
        string engine_id FK
        string locale
        date window_start
        date window_end
        decimal answer_share
        decimal citation_share
        decimal prominence
        decimal sentiment_avg
        decimal delta_answer_share
        decimal delta_citation_share
        timestamp calculated_at
    }

    AUDIT_RESULT {
        uuid audit_id PK
        uuid workspace_id FK
        uuid domain_id FK
        timestamp started_at
        timestamp completed_at
        int pages_scanned
        int issues_found
        json summary
    }

    AUDIT_ISSUE {
        uuid issue_id PK
        uuid audit_id FK
        string url
        string issue_code
        string severity
        text rationale
        json fix_recipe
        json impacted_prompts
        string status
        timestamp detected_at
    }

    RECOMMENDATION {
        uuid recommendation_id PK
        uuid issue_id FK
        decimal priority_score
        text description
        json expected_impact
        text llm_outline
        timestamp created_at
    }

    ACTION_ITEM {
        uuid action_id PK
        uuid recommendation_id FK
        uuid assigned_to FK
        string external_id
        string status
        timestamp created_at
        timestamp completed_at
        json metadata
    }

    EXPERIMENT {
        uuid experiment_id PK
        uuid workspace_id FK
        uuid created_by FK
        string name
        string hypothesis
        string experiment_type
        json target_prompts
        json success_metrics
        date start_date
        date end_date
        string status
        timestamp created_at
    }

    EXPERIMENT_VARIANT {
        uuid variant_id PK
        uuid experiment_id FK
        string variant_name
        json configuration
        int sample_size
        timestamp deployed_at
    }

    EXPERIMENT_RESULT {
        uuid result_id PK
        uuid experiment_id FK
        uuid winner_variant_id FK
        decimal p_value
        decimal effect_size
        json confidence_interval
        json detailed_metrics
        text conclusion
        timestamp analyzed_at
    }

    ALERT_RULE {
        uuid rule_id PK
        uuid workspace_id FK
        string name
        string metric
        string condition
        decimal threshold
        string severity
        json channels
        boolean active
        timestamp created_at
    }

    ALERT {
        uuid alert_id PK
        uuid rule_id FK
        string severity
        json trigger_data
        string status
        timestamp triggered_at
        timestamp acknowledged_at
        timestamp resolved_at
    }

    ALERT_SUBSCRIPTION {
        uuid subscription_id PK
        uuid user_id FK
        uuid rule_id FK
        json channels
        json preferences
        timestamp created_at
    }

    SUBSCRIPTION {
        uuid subscription_id PK
        uuid workspace_id FK
        string plan_name
        int seats
        int runs_quota
        decimal monthly_cost
        timestamp current_period_start
        timestamp current_period_end
        string status
    }
```

**Description:**

This comprehensive ER diagram shows all major entities and their relationships in the platform. Key relationship types:

- **One-to-Many (||--o{)**: A workspace has many topics, a topic has many prompts
- **Many-to-One (}o--)**: Many citations belong to one domain
- **One-to-One (||--||)**: A collection job produces exactly one raw artifact (primary), which becomes one answer record
- **Many-to-Many**: Implemented through junction tables (e.g., Query Plan to Prompts)

---

## 2. Core Domain Entities

### 2.1 Workspace and User Management

```mermaid
erDiagram
    WORKSPACE ||--o{ USER : contains
    WORKSPACE ||--o{ WORKSPACE_ROLE : defines
    WORKSPACE ||--o{ API_KEY : issues
    WORKSPACE ||--o{ AUDIT_LOG : tracks

    USER ||--o{ USER_SESSION : has
    USER }o--o{ WORKSPACE_ROLE : "assigned"

    WORKSPACE {
        uuid workspace_id PK "Unique workspace identifier"
        string name "Display name"
        string slug "URL-safe identifier"
        timestamp created_at "Creation timestamp"
        timestamp updated_at "Last modification"
        json settings "Workspace preferences"
        string tier "Subscription tier: free/pro/enterprise"
        int user_limit "Max users allowed"
        int plan_limit "Max monitoring plans"
        boolean active "Workspace status"
    }

    USER {
        uuid user_id PK "Unique user identifier"
        uuid workspace_id FK "Associated workspace"
        string email "Email address (unique per workspace)"
        string name "Full name"
        string password_hash "Hashed password"
        string role "viewer/editor/admin/owner"
        timestamp last_login "Last successful login"
        json preferences "User UI/notification preferences"
        boolean email_verified "Email verification status"
        timestamp created_at "Account creation"
    }

    WORKSPACE_ROLE {
        uuid role_id PK
        uuid workspace_id FK
        string role_name "Custom role name"
        json permissions "Granular permissions array"
        timestamp created_at
    }

    API_KEY {
        uuid key_id PK
        uuid workspace_id FK
        uuid created_by FK
        string key_hash "Hashed API key"
        string name "Key description"
        json scopes "API permissions"
        timestamp expires_at "Expiration date"
        timestamp last_used_at "Last API call"
        boolean active
    }

    USER_SESSION {
        uuid session_id PK
        uuid user_id FK
        string token_hash "Session token hash"
        string ip_address "Client IP"
        string user_agent "Browser/client info"
        timestamp created_at
        timestamp expires_at
    }

    AUDIT_LOG {
        uuid log_id PK
        uuid workspace_id FK
        uuid user_id FK
        string action "Action performed"
        string resource_type "Entity type affected"
        uuid resource_id "Entity ID"
        json changes "Before/after values"
        string ip_address
        timestamp created_at
    }
```

**Key Relationships:**
- **Workspace → Users**: One workspace contains many users (multi-user collaboration)
- **User → Workspace**: Each user belongs to exactly one workspace (single-tenancy per user)
- **Workspace → Roles**: Custom roles for fine-grained permissions
- **API Keys**: Programmatic access tied to workspace
- **Audit Logs**: Complete activity tracking for compliance

**Data Integrity Rules:**
- User email must be unique within workspace
- Workspace owner cannot be deleted
- At least one admin must exist per workspace
- API keys auto-expire if not used for 90 days

---

### 2.2 Topic and Prompt Structure

```mermaid
erDiagram
    TOPIC ||--o{ PROMPT : contains
    TOPIC ||--o{ TOPIC_TAG : categorized
    PROMPT ||--o{ PROMPT_VERSION : versioned
    PROMPT ||--o{ PROMPT_TRANSLATION : translated

    TOPIC {
        uuid topic_id PK
        uuid workspace_id FK
        string name "Topic name (e.g., 'Herbal Ingredients')"
        text description "Detailed description"
        string category "Business category"
        json metadata "Custom fields"
        int prompt_count "Cached count"
        timestamp created_at
        timestamp updated_at
    }

    PROMPT {
        uuid prompt_id PK
        uuid topic_id FK
        text text "Primary prompt text"
        string locale "en-US, es-ES, etc."
        string intent "informational/transactional/navigational"
        json variants "Alternative phrasings"
        decimal estimated_volume "Search volume estimate"
        string priority "high/medium/low"
        boolean active
        timestamp created_at
        timestamp updated_at
    }

    TOPIC_TAG {
        uuid tag_id PK
        uuid topic_id FK
        string tag_name "Tag for categorization"
        timestamp created_at
    }

    PROMPT_VERSION {
        uuid version_id PK
        uuid prompt_id FK
        text text "Historical prompt text"
        int version_number "Version counter"
        uuid created_by FK
        timestamp created_at
    }

    PROMPT_TRANSLATION {
        uuid translation_id PK
        uuid prompt_id FK
        string locale "Target language/region"
        text translated_text "Localized prompt"
        string translation_source "human/machine"
        timestamp created_at
    }
```

**Key Features:**
- **Topic Hierarchy**: Organize prompts by business vertical or theme
- **Prompt Versioning**: Track changes over time for historical analysis
- **Multi-language Support**: Same prompt in multiple locales
- **Intent Classification**: Understand query purpose (info vs. transaction)
- **Variants**: Test different phrasings of same question

**Example Data:**
```json
{
  "topic": {
    "name": "Herbal Supplements",
    "description": "Products and suppliers in herbal supplement industry",
    "category": "Nutrition",
    "metadata": {"industry": "Healthcare", "competition_level": "high"}
  },
  "prompts": [
    {
      "text": "Who are the best suppliers of ashwagandha extract?",
      "locale": "en-US",
      "intent": "informational",
      "variants": [
        "Where can I find quality ashwagandha suppliers?",
        "Top ashwagandha extract manufacturers"
      ],
      "priority": "high"
    }
  ]
}
```

---

## 3. Monitoring & Collection Entities

### 3.1 Query Plans and Scheduling

```mermaid
erDiagram
    QUERY_PLAN ||--o{ PLAN_PROMPT : includes
    QUERY_PLAN ||--o{ PLAN_ENGINE : targets
    QUERY_PLAN ||--o{ PLAN_SCHEDULE : executes
    QUERY_PLAN ||--o{ COLLECTION_JOB : generates

    PLAN_PROMPT }o--|| PROMPT : references
    PLAN_ENGINE }o--|| ENGINE : configures

    QUERY_PLAN {
        uuid plan_id PK
        uuid workspace_id FK
        uuid created_by FK
        string name "Plan display name"
        text description "Plan purpose"
        string status "active/paused/archived"
        json budget_cap "Spending limits"
        json notification_settings "Alert preferences"
        timestamp created_at
        timestamp updated_at
        timestamp last_run_at "Last execution"
    }

    PLAN_PROMPT {
        uuid plan_prompt_id PK
        uuid plan_id FK
        uuid prompt_id FK
        boolean active "Include in next run"
        int priority_order "Execution order"
        timestamp added_at
    }

    PLAN_ENGINE {
        uuid plan_engine_id PK
        uuid plan_id FK
        string engine_id FK
        json config "Engine-specific settings"
        boolean active
        int runs_per_day "Frequency limit"
        timestamp added_at
    }

    PLAN_SCHEDULE {
        uuid schedule_id PK
        uuid plan_id FK
        string schedule_type "cron/interval/one-time"
        string cron_expression "e.g., '0 6 * * *'"
        string timezone "Execution timezone"
        timestamp next_run_at "Next scheduled execution"
        boolean active
        timestamp created_at
    }

    ENGINE {
        string engine_id PK "chatgpt/perplexity/gemini"
        string name "Display name"
        string type "ai_search/chatbot/assistant"
        json config "Default configuration"
        json rate_limits "API/scraping limits"
        decimal cost_per_query "Estimated cost"
        string status "available/degraded/unavailable"
        timestamp last_health_check
    }
```

**Query Plan Logic:**
- Plans are reusable configurations for monitoring
- Each plan can monitor multiple prompts across multiple engines
- Schedules support cron expressions for flexible timing
- Budget caps prevent runaway costs
- Plans can be paused/resumed without losing configuration

**Budget Cap Example:**
```json
{
  "budget_cap": {
    "runs_per_day": 800,
    "max_monthly_cost": 500,
    "alert_threshold": 0.8,
    "auto_pause_on_exceed": true
  }
}
```

---

### 3.2 Collection Jobs and Artifacts

```mermaid
erDiagram
    COLLECTION_JOB ||--o{ RAW_ARTIFACT : produces
    COLLECTION_JOB ||--|| ANSWER_RECORD : "parsed to"
    COLLECTION_JOB ||--o{ JOB_LOG : logs

    RAW_ARTIFACT ||--o{ ARTIFACT_VERSION : versioned

    COLLECTION_JOB {
        uuid job_id PK
        uuid plan_id FK
        uuid prompt_id FK
        string engine_id FK
        string locale "Target locale"
        string status "pending/in_progress/completed/failed"
        timestamp scheduled_at "When to run"
        timestamp started_at "Actual start"
        timestamp completed_at "Actual completion"
        int retry_count "Number of retries"
        int timeout_seconds "Max execution time"
        json collector_config "Runtime parameters"
        json error_details "Failure information"
        decimal cost_estimate "Estimated cost"
        decimal actual_cost "Actual cost incurred"
    }

    RAW_ARTIFACT {
        uuid artifact_id PK
        uuid job_id FK
        string s3_bucket "S3 bucket name"
        string s3_key "S3 object key"
        string content_type "HTML/JSON/PNG"
        bigint size_bytes "File size"
        string checksum "SHA256 hash"
        json metadata "Custom metadata"
        timestamp created_at
        timestamp expires_at "Retention policy"
    }

    JOB_LOG {
        uuid log_id PK
        uuid job_id FK
        string level "debug/info/warn/error"
        text message "Log message"
        json context "Additional data"
        timestamp created_at
    }

    ARTIFACT_VERSION {
        uuid version_id PK
        uuid artifact_id FK
        int version_number "Version sequence"
        string s3_key "Historical S3 key"
        timestamp created_at
    }
```

**Artifact Types:**
1. **HTML**: Raw page source from AI engine
2. **JSON**: Structured API responses
3. **PNG**: Screenshots for evidence
4. **Metadata**: Job configuration, timestamps, engine version

**Lifecycle:**
1. Job created and scheduled
2. Collector picks up job
3. Queries AI engine
4. Captures response + screenshot
5. Uploads to S3
6. Creates artifact records
7. Triggers parsing pipeline

**Retention Policy:**
- Raw artifacts: 90 days in standard storage
- After 90 days: Move to Glacier
- Screenshots: 1 year retention
- Parsed data: Indefinite in DynamoDB/Neo4j + GraphRAG

---

## 4. Analysis & Scoring Entities

### 4.1 Answer Records and Citations

```mermaid
erDiagram
    ANSWER_RECORD ||--o{ CITATION : contains
    ANSWER_RECORD ||--o{ ANSWER_ENTITY : mentions
    ANSWER_RECORD ||--o{ ANSWER_SENTIMENT : analyzed

    CITATION }o--|| DOMAIN : "links to"
    ANSWER_ENTITY }o--|| BRAND : references

    ANSWER_RECORD {
        uuid answer_id PK
        uuid job_id FK
        uuid prompt_id FK
        string engine_id FK
        text answer_text "Full AI response"
        text answer_markdown "Markdown formatted"
        string answer_type "summary/detailed/conversational"
        int word_count "Text length"
        json structure "Sections/bullets/paragraphs"
        decimal confidence_score "Engine confidence (0-1)"
        json metadata "Engine-specific data"
        timestamp captured_at "When collected"
        timestamp processed_at "When parsed"
    }

    CITATION {
        uuid citation_id PK
        uuid answer_id FK
        uuid domain_id FK
        string url "Full URL cited"
        int position "Position in answer (1-10)"
        boolean first_party "Owned by monitored brand"
        string anchor_text "Link text"
        string context "Surrounding text"
        string citation_type "inline/footnote/reference"
        json metadata "Additional properties"
    }

    DOMAIN {
        uuid domain_id PK
        string domain_name "example.com"
        uuid brand_id FK "Owning brand"
        boolean verified "Ownership verified"
        string category "e-commerce/blog/news/academic"
        decimal authority_score "Estimated domain authority"
        json metadata "SEO metrics, etc."
        timestamp first_seen "First citation date"
        timestamp last_seen "Most recent citation"
    }

    ANSWER_ENTITY {
        uuid entity_id PK
        uuid answer_id FK
        uuid brand_id FK
        string entity_text "Exact mention text"
        string entity_type "brand/product/person/organization"
        int mention_count "Frequency in answer"
        decimal prominence "Position weight"
        json context "Surrounding context"
    }

    ANSWER_SENTIMENT {
        uuid sentiment_id PK
        uuid answer_id FK
        uuid brand_id FK
        decimal sentiment_score "-1 to +1"
        string sentiment_label "positive/neutral/negative"
        text supporting_text "Quote supporting score"
        string analysis_method "rule-based/ml-model"
        timestamp analyzed_at
    }
```

**Answer Record Details:**
- **Full Text**: Complete AI-generated response
- **Structure Analysis**: Identifies lists, paragraphs, sections
- **Confidence**: Some engines provide confidence scores
- **Metadata**: Engine version, model name, response time

**Citation Tracking:**
- **Position**: 1st citation has highest value
- **Context**: Text around the citation for relevance
- **First-party vs Third-party**: Critical for brand visibility

**Entity Recognition:**
- Extract all brand/product mentions from answer
- Calculate prominence based on position and frequency
- Link to known brands in database

**Sentiment Analysis:**
- Positive: Brand endorsed, recommended, praised
- Neutral: Factual mention without opinion
- Negative: Criticism, warning, alternatives suggested

---

### 4.2 Scoring and Metrics

```mermaid
erDiagram
    SCORE_SNAPSHOT ||--o{ SCORE_DETAIL : "breaks down"
    SCORE_SNAPSHOT }o--|| BRAND : measures
    SCORE_SNAPSHOT }o--|| TOPIC : "for"
    SCORE_SNAPSHOT }o--|| ENGINE : "on"

    METRIC_TREND ||--o{ TREND_POINT : "time series"
    METRIC_ALERT }o--|| ALERT_RULE : triggered

    SCORE_SNAPSHOT {
        uuid score_id PK
        uuid workspace_id FK
        uuid brand_id FK
        uuid topic_id FK
        string engine_id FK
        string locale "en-US, es-ES"
        date window_start "Measurement period start"
        date window_end "Measurement period end"
        int total_prompts "Prompts evaluated"
        int answered_prompts "Prompts with answers"
        decimal answer_share "% prompts mentioning brand (0-1)"
        decimal citation_share "% citations to brand (0-1)"
        decimal prominence "Weighted position score (0-1)"
        decimal sentiment_avg "Average sentiment (-1 to +1)"
        decimal delta_answer_share "Change from previous period"
        decimal delta_citation_share "Change from previous period"
        int rank_position "Rank among competitors"
        json competitors "Competitor scores"
        timestamp calculated_at
    }

    SCORE_DETAIL {
        uuid detail_id PK
        uuid score_id FK
        uuid prompt_id FK
        boolean brand_mentioned "Brand in answer"
        int citation_count "Number of brand citations"
        decimal prominence_score "Position weighting"
        decimal sentiment_score "Sentiment for this prompt"
        int competitor_count "Competitors mentioned"
        json raw_data "Full detail"
    }

    METRIC_TREND {
        uuid trend_id PK
        uuid workspace_id FK
        uuid brand_id FK
        string metric_name "answer_share/citation_share"
        string aggregation "daily/weekly/monthly"
        string engine_id FK
        timestamp created_at
    }

    TREND_POINT {
        uuid point_id PK
        uuid trend_id FK
        timestamp timestamp "Point in time"
        decimal value "Metric value"
        json annotations "Events/notes"
    }

    METRIC_ALERT {
        uuid alert_id PK
        uuid score_id FK
        string metric_name "Which metric triggered"
        decimal threshold "Alert threshold"
        decimal actual_value "Actual value"
        decimal delta "Change amount"
        string severity "critical/high/medium/low"
        timestamp triggered_at
    }
```

**Scoring Calculations:**

**Answer Share:**
```
answer_share = (prompts_with_brand_mention / total_prompts_answered)
```

**Citation Share:**
```
citation_share = (brand_citations / total_citations_in_answers)
```

**Prominence:**
```
prominence = Σ(position_weight × mention_quality) / total_mentions
where position_weight = 1/position (1st = 1.0, 2nd = 0.5, 3rd = 0.33, ...)
```

**Sentiment:**
```
sentiment_avg = Σ(sentiment_score × mention_count) / total_mentions
Range: -1 (very negative) to +1 (very positive)
```

**Competitor Comparison:**
```json
{
  "competitors": [
    {"brand": "Competitor A", "answer_share": 0.52, "rank": 1},
    {"brand": "Your Brand", "answer_share": 0.42, "rank": 2},
    {"brand": "Competitor B", "answer_share": 0.35, "rank": 3}
  ]
}
```

---

## 5. Action & Optimization Entities

### 5.1 GEO Audit and Issues

```mermaid
erDiagram
    AUDIT_RESULT ||--o{ AUDIT_ISSUE : identifies
    AUDIT_RESULT }o--|| DOMAIN : scans
    AUDIT_ISSUE ||--o{ RECOMMENDATION : generates
    AUDIT_ISSUE ||--o{ AFFECTED_PROMPT : impacts

    AUDIT_RESULT {
        uuid audit_id PK
        uuid workspace_id FK
        uuid domain_id FK
        string audit_type "full/incremental/targeted"
        timestamp started_at
        timestamp completed_at
        int pages_scanned "Total pages crawled"
        int pages_with_issues "Pages with problems"
        int total_issues "Total issues found"
        int critical_issues "Critical count"
        int high_issues "High severity"
        int medium_issues "Medium severity"
        int low_issues "Low severity"
        json summary "Executive summary"
        decimal score "Overall health score (0-100)"
    }

    AUDIT_ISSUE {
        uuid issue_id PK
        uuid audit_id FK
        string url "Page URL with issue"
        string issue_code "FAQ_SCHEMA_MISSING/META_CLARITY/etc"
        string category "technical/content/performance"
        string severity "critical/high/medium/low"
        text rationale "Why this is an issue"
        json fix_recipe "How to fix (code/content)"
        json evidence "Screenshots, examples"
        int estimated_effort_hours "Time to fix"
        decimal expected_lift "Predicted impact (0-1)"
        string status "open/in_progress/resolved/dismissed"
        timestamp detected_at
        timestamp resolved_at
    }

    AFFECTED_PROMPT {
        uuid affected_id PK
        uuid issue_id FK
        uuid prompt_id FK
        decimal impact_score "How much this prompt suffers"
        boolean brand_losing "Brand not cited"
        string competitor_advantage "Who benefits"
    }

    RECOMMENDATION {
        uuid recommendation_id PK
        uuid issue_id FK
        decimal priority_score "0-100, higher = more urgent"
        text title "Action title"
        text description "What to do"
        json expected_impact "Metrics improvement forecast"
        text llm_outline "AI-generated content outline"
        json supporting_evidence "Competitor examples"
        string recommendation_type "content/technical/strategic"
        timestamp created_at
    }
```

**Issue Codes:**
- `FAQ_SCHEMA_MISSING`: No FAQ structured data
- `PRODUCT_SCHEMA_INCOMPLETE`: Missing product fields
- `META_TITLE_WEAK`: Title tag not optimized
- `META_DESCRIPTION_MISSING`: No meta description
- `CONTENT_THIN`: Insufficient content depth
- `SPEED_SLOW`: Page load >3 seconds
- `MOBILE_UNFRIENDLY`: Mobile UX issues
- `BROKEN_LINKS`: Dead links on page
- `DUPLICATE_CONTENT`: Canonical issues

**Priority Calculation:**
```
priority_score = (
    (impacted_prompts × 10) +
    (competitor_advantage × 20) +
    (expected_lift × 50) +
    (severity_weight × 20)
) / 100

Where:
- impacted_prompts: Number of prompts affected
- competitor_advantage: How much competitors benefit (0-1)
- expected_lift: Predicted improvement (0-1)
- severity_weight: critical=1.0, high=0.7, medium=0.4, low=0.1
```

**Fix Recipe Example:**
```json
{
  "issue_code": "FAQ_SCHEMA_MISSING",
  "fix_recipe": {
    "type": "structured_data",
    "schema": "FAQPage",
    "steps": [
      "Identify common questions from prompt data",
      "Write clear, concise answers",
      "Add FAQPage schema.org markup",
      "Test with Google Rich Results Test"
    ],
    "code_example": "<script type=\"application/ld+json\">...</script>",
    "estimated_time": "2-4 hours",
    "difficulty": "medium"
  }
}
```

---

### 5.2 Action Items and Integrations

```mermaid
erDiagram
    ACTION_ITEM }o--|| RECOMMENDATION : "implements"
    ACTION_ITEM }o--|| USER : "assigned to"
    ACTION_ITEM ||--o{ ACTION_UPDATE : "status tracked"
    ACTION_ITEM }o--o{ EXTERNAL_TASK : synchronized

    INTEGRATION_CONFIG ||--o{ EXTERNAL_TASK : connects
    INTEGRATION_CONFIG }o--|| WORKSPACE : "configured for"

    ACTION_ITEM {
        uuid action_id PK
        uuid recommendation_id FK
        uuid workspace_id FK
        uuid assigned_to FK
        string title "Action title"
        text description "Detailed task"
        string status "open/in_progress/blocked/completed/cancelled"
        string priority "critical/high/medium/low"
        date due_date "Deadline"
        json metadata "Custom fields"
        timestamp created_at
        timestamp started_at
        timestamp completed_at
    }

    ACTION_UPDATE {
        uuid update_id PK
        uuid action_id FK
        uuid user_id FK
        text comment "Status update"
        string old_status "Previous status"
        string new_status "Current status"
        json changes "Field changes"
        timestamp created_at
    }

    EXTERNAL_TASK {
        uuid external_task_id PK
        uuid action_id FK
        uuid integration_id FK
        string external_id "ID in external system (Jira, Notion)"
        string external_url "Link to external task"
        string sync_status "synced/pending/error"
        timestamp last_synced_at
        json sync_metadata "Sync details"
    }

    INTEGRATION_CONFIG {
        uuid integration_id PK
        uuid workspace_id FK
        string integration_type "jira/notion/asana/linear"
        json credentials "API keys/tokens (encrypted)"
        json mapping "Field mappings"
        boolean active
        timestamp created_at
        timestamp last_sync_at
    }
```

**Supported Integrations:**
1. **Jira**: Create issues, sync status, attach evidence
2. **Notion**: Create pages in databases, update properties
3. **Asana**: Create tasks, update progress
4. **Linear**: Create issues with labels
5. **Slack**: Post to channels, thread discussions
6. **Custom Webhooks**: JSON payload to any endpoint

**Integration Workflow:**
1. User selects recommendation
2. Platform generates action item
3. Integration creates task in external system
4. Bidirectional sync keeps status updated
5. Comments flow between systems
6. Completion triggers verification

---

## 6. AI Agent Framework Entities

> **For complete framework documentation**, see [PLATFORM_ON_AGENT_FRAMEWORK.md](PLATFORM_ON_AGENT_FRAMEWORK.md)

### 6.1 Agent Configuration and Execution

This section is intentionally brief. Agent configuration and execution are managed by the two foundation modules:
- https://github.com/ideabosque/ai_agent_core_engine
- https://github.com/ideabosque/ai_coordination_engine

For schema details and runtime semantics, refer to those repositories and the platform framework documentation.

### 6.2 Agent Learning and Optimization

```mermaid
erDiagram
    WORKFLOW_EXECUTION ||--o{ WORKFLOW_FEEDBACK : receives
    WORKFLOW_EXECUTION ||--o{ WORKFLOW_METRIC : measures

    WORKFLOW_FEEDBACK {
        uuid feedback_id PK
        uuid workflow_execution_id FK
        uuid user_id FK
        int rating "1-5 stars"
        text comment "User feedback"
        json corrections "Human edits"
        timestamp created_at
    }

    WORKFLOW_METRIC {
        uuid metric_id PK
        uuid workflow_execution_id FK
        string metric_name "quality/cost/latency"
        decimal value
        timestamp measured_at
    }
```

**Description:**

This lightweight schema captures learning signals for the coordination layer:
- https://github.com/ideabosque/ai_coordination_engine

It keeps feedback and metrics at the workflow level so orchestration can improve without exposing internal agent step detail.


## 7. Platform & Governance Entities

### 7.1 Experiments and Testing

```mermaid
erDiagram
    EXPERIMENT ||--o{ EXPERIMENT_VARIANT : tests
    EXPERIMENT ||--o{ EXPERIMENT_PROMPT : measures
    EXPERIMENT }o--|| USER : "designed by"
    EXPERIMENT_VARIANT ||--o{ VARIANT_MEASUREMENT : collects
    EXPERIMENT ||--|| EXPERIMENT_RESULT : produces

    EXPERIMENT {
        uuid experiment_id PK
        uuid workspace_id FK
        uuid created_by FK
        string name "Experiment name"
        text hypothesis "What we're testing"
        string experiment_type "ab_test/pre_post/multivariate"
        json target_prompts "Prompts to measure"
        json success_metrics "Primary/secondary metrics"
        date start_date "Experiment start"
        date end_date "Planned end"
        int min_sample_size "Required samples"
        decimal significance_threshold "P-value threshold (0.05)"
        string status "draft/running_baseline/running_treatment/analyzing/complete"
        timestamp created_at
    }

    EXPERIMENT_VARIANT {
        uuid variant_id PK
        uuid experiment_id FK
        string variant_name "control/treatment/variant_a"
        text description "What's different"
        json configuration "Settings/content"
        decimal traffic_allocation "% of traffic (0-1)"
        int sample_size_actual "Samples collected"
        timestamp deployed_at
    }

    EXPERIMENT_PROMPT {
        uuid exp_prompt_id PK
        uuid experiment_id FK
        uuid prompt_id FK
        boolean is_primary "Primary metric"
    }

    VARIANT_MEASUREMENT {
        uuid measurement_id PK
        uuid variant_id FK
        uuid answer_id FK
        decimal answer_share "Measured metric"
        decimal citation_share
        decimal prominence
        decimal sentiment
        timestamp measured_at
    }

    EXPERIMENT_RESULT {
        uuid result_id PK
        uuid experiment_id FK
        uuid winner_variant_id FK "Winning variant (if any)"
        decimal p_value "Statistical significance"
        decimal effect_size "Cohen's d or similar"
        json confidence_interval "95% CI for lift"
        json metric_comparison "All metrics compared"
        decimal power "Statistical power achieved"
        text conclusion "Interpretation"
        text recommendation "What to do next"
        boolean statistically_significant
        boolean practically_significant "Meaningful business impact"
        timestamp analyzed_at
    }
```

**Experiment Types:**

1. **A/B Test**:
   - Simultaneous comparison
   - Traffic split 50/50 (or custom)
   - Randomized assignment

2. **Pre/Post**:
   - Before measurement (baseline)
   - Deploy change
   - After measurement (treatment)
   - Compare periods

3. **Multivariate**:
   - Test multiple variables simultaneously
   - Factorial design
   - Identify interactions

**Sample Size Calculation:**
```
n = (Z_α/2 + Z_β)² × 2σ² / δ²

Where:
- Z_α/2: Z-score for significance level (1.96 for 95%)
- Z_β: Z-score for power (0.84 for 80% power)
- σ: Standard deviation of metric
- δ: Minimum detectable effect
```

**Statistical Analysis:**
```json
{
  "primary_metric": "answer_share",
  "control": {
    "mean": 0.32,
    "std": 0.08,
    "n": 120
  },
  "treatment": {
    "mean": 0.47,
    "std": 0.09,
    "n": 120
  },
  "results": {
    "difference": 0.15,
    "relative_lift": 0.47,
    "p_value": 0.003,
    "confidence_interval": [0.06, 0.24],
    "cohens_d": 1.79,
    "statistically_significant": true,
    "recommendation": "Deploy treatment variant"
  }
}
```

---

### 7.2 Alerts and Notifications

```mermaid
erDiagram
    ALERT_RULE ||--o{ ALERT : triggers
    ALERT_RULE ||--o{ ALERT_SUBSCRIPTION : "subscribed by"
    ALERT }o--|| USER : notifies
    ALERT ||--o{ NOTIFICATION : generates
    NOTIFICATION }o--|| NOTIFICATION_CHANNEL : "sent via"

    ALERT_RULE {
        uuid rule_id PK
        uuid workspace_id FK
        uuid created_by FK
        string name "Rule name"
        text description "What this rule does"
        string metric "answer_share/citation_share/etc"
        string condition "drop/spike/threshold/change"
        decimal threshold "Trigger value"
        int window_minutes "Evaluation period"
        string severity "critical/high/medium/low"
        json filters "Engine/topic/brand filters"
        json actions "What to do when triggered"
        boolean active
        timestamp created_at
        timestamp last_triggered_at
    }

    ALERT {
        uuid alert_id PK
        uuid rule_id FK
        string severity
        json trigger_data "Metric values, context"
        string status "triggered/acknowledged/in_progress/resolved/auto_resolved"
        timestamp triggered_at
        timestamp acknowledged_at
        uuid acknowledged_by FK
        timestamp resolved_at
        uuid resolved_by FK
        text resolution_note
    }

    ALERT_SUBSCRIPTION {
        uuid subscription_id PK
        uuid user_id FK
        uuid rule_id FK
        json channels "email/slack/sms/webhook"
        json preferences "Frequency, quiet hours"
        boolean active
        timestamp created_at
    }

    NOTIFICATION {
        uuid notification_id PK
        uuid alert_id FK
        uuid channel_id FK
        string status "pending/sent/delivered/failed"
        json payload "Message content"
        int retry_count
        text error_message
        timestamp sent_at
        timestamp delivered_at
    }

    NOTIFICATION_CHANNEL {
        uuid channel_id PK
        uuid workspace_id FK
        string channel_type "email/slack/webhook/sms"
        string name "Channel name"
        json config "API keys, URLs, etc"
        boolean active
        timestamp created_at
    }
```

**Alert Rule Examples:**

**Critical Drop:**
```json
{
  "name": "Critical Answer Share Drop",
  "metric": "answer_share",
  "condition": "drop",
  "threshold": 0.10,
  "window_minutes": 1440,
  "severity": "critical",
  "filters": {
    "engines": ["chatgpt", "perplexity"],
    "topics": ["herbal_ingredients"]
  },
  "actions": {
    "notify": ["slack", "email", "sms"],
    "create_task": true,
    "escalate_after_hours": 2
  }
}
```

**Competitor Surge:**
```json
{
  "name": "Competitor Gain Alert",
  "metric": "competitor_answer_share",
  "condition": "spike",
  "threshold": 0.15,
  "severity": "high",
  "actions": {
    "notify": ["slack"],
    "attach_evidence": true,
    "suggested_actions": true
  }
}
```

---

### 7.3 Billing and Usage

```mermaid
erDiagram
    WORKSPACE ||--|| SUBSCRIPTION : has
    SUBSCRIPTION ||--o{ INVOICE : generates
    SUBSCRIPTION ||--o{ USAGE_RECORD : tracks
    INVOICE ||--o{ INVOICE_LINE_ITEM : contains
    USAGE_RECORD }o--|| USAGE_CATEGORY : categorized

    SUBSCRIPTION {
        uuid subscription_id PK
        uuid workspace_id FK
        string plan_name "free/professional/enterprise"
        string billing_period "monthly/annual"
        int seats_included "User seats"
        int seats_used "Active users"
        int runs_quota "Monthly runs limit"
        int runs_used "Runs consumed"
        decimal monthly_cost "Base price"
        decimal overage_rate "Cost per extra run"
        string payment_method_id "Stripe payment method"
        timestamp current_period_start
        timestamp current_period_end
        string status "active/past_due/cancelled"
        timestamp cancelled_at
    }

    INVOICE {
        uuid invoice_id PK
        uuid subscription_id FK
        string invoice_number "Unique invoice #"
        date period_start
        date period_end
        decimal subtotal
        decimal tax_amount
        decimal total_amount
        string currency "USD/EUR/etc"
        string status "draft/open/paid/void"
        timestamp issued_at
        timestamp paid_at
        timestamp due_at
    }

    INVOICE_LINE_ITEM {
        uuid line_item_id PK
        uuid invoice_id FK
        string description "Line item description"
        int quantity
        decimal unit_price
        decimal amount
        string item_type "subscription/overage/addon"
    }

    USAGE_RECORD {
        uuid usage_id PK
        uuid subscription_id FK
        uuid usage_category_id FK
        date usage_date
        int quantity "Count of usage"
        decimal cost "Cost incurred"
        json metadata "Details"
        timestamp recorded_at
    }

    USAGE_CATEGORY {
        uuid category_id PK
        string name "collection_run/emulator_test/api_call"
        string unit "runs/tests/requests"
        decimal base_rate "Cost per unit"
        string billing_model "metered/flat/tiered"
    }
```

**Pricing Tiers:**

**Free:**
- 1 user
- 1 monitoring plan
- 100 runs/month
- Email support
- $0/month

**Professional:**
- 5 users
- Unlimited plans
- 2,000 runs/month
- All AI engines
- Priority support
- $299/month

**Enterprise:**
- Unlimited users
- Unlimited plans
- Custom run limits
- Dedicated support
- SLA guarantee
- Custom pricing

**Overage Billing:**
```
runs_over_quota = runs_used - runs_quota
overage_cost = runs_over_quota × overage_rate

Example:
Quota: 2,000 runs
Used: 2,450 runs
Rate: $0.10/run
Overage: 450 × $0.10 = $45
```

---

## 8. Detailed Entity Specifications

### 8.1 Indexes and Constraints

**Primary Indexes:**
```sql
-- High-traffic query patterns
CREATE INDEX idx_answer_records_prompt_engine
  ON answer_records(prompt_id, engine_id, captured_at DESC);

CREATE INDEX idx_citations_domain_date
  ON citations(domain_id, created_at DESC);

CREATE INDEX idx_scores_brand_topic_date
  ON score_snapshots(brand_id, topic_id, window_start DESC);

CREATE INDEX idx_jobs_status_scheduled
  ON collection_jobs(status, scheduled_at);

CREATE INDEX idx_alerts_workspace_status
  ON alerts(workspace_id, status, triggered_at DESC);
```

**Unique Constraints:**
```sql
-- Prevent duplicates
ALTER TABLE users
  ADD CONSTRAINT unique_email_per_workspace
  UNIQUE (workspace_id, email);

ALTER TABLE domains
  ADD CONSTRAINT unique_domain_name
  UNIQUE (domain_name);

ALTER TABLE prompts
  ADD CONSTRAINT unique_text_per_topic_locale
  UNIQUE (topic_id, text, locale);
```

**Foreign Key Constraints:**
```sql
-- Referential integrity
ALTER TABLE answer_records
  ADD CONSTRAINT fk_job
  FOREIGN KEY (job_id) REFERENCES collection_jobs(job_id)
  ON DELETE CASCADE;

ALTER TABLE citations
  ADD CONSTRAINT fk_answer
  FOREIGN KEY (answer_id) REFERENCES answer_records(answer_id)
  ON DELETE CASCADE;

ALTER TABLE score_snapshots
  ADD CONSTRAINT fk_brand
  FOREIGN KEY (brand_id) REFERENCES brands(brand_id)
  ON DELETE RESTRICT;
```

---

### 8.2 Data Retention Policies

**Retention Rules by Entity:**

| Entity | Retention | Archival Strategy |
|--------|-----------|-------------------|
| RAW_ARTIFACT | 90 days hot, then Glacier | S3 lifecycle policy |
| ANSWER_RECORD | Indefinite | Indexed in Neo4j + GraphRAG |
| SCORE_SNAPSHOT | Indefinite | Aggregated over time |
| COLLECTION_JOB | 1 year | Archive to cold storage |
| AUDIT_LOG | 7 years | Compliance requirement |
| ALERT | 1 year | Summarized for reporting |
| EXPERIMENT_RESULT | Indefinite | Learning repository |

**Purge Policies:**
```sql
-- Delete old job logs
DELETE FROM job_logs
WHERE created_at < NOW() - INTERVAL '90 days';

-- Archive old alerts
INSERT INTO alerts_archive
SELECT * FROM alerts
WHERE triggered_at < NOW() - INTERVAL '1 year';

DELETE FROM alerts
WHERE triggered_at < NOW() - INTERVAL '1 year';
```

---

### 8.3 Data Volume Estimates

**Typical Workspace (Professional Tier):**

| Entity | Daily Records | Monthly Total | Storage/Record | Monthly Storage |
|--------|--------------|---------------|----------------|-----------------|
| COLLECTION_JOB | 2,000 | 60,000 | 2 KB | 120 MB |
| RAW_ARTIFACT | 2,000 | 60,000 | 50 KB | 3 GB |
| ANSWER_RECORD | 2,000 | 60,000 | 5 KB | 300 MB |
| CITATION | 10,000 | 300,000 | 500 B | 150 MB |
| SCORE_SNAPSHOT | 100 | 3,000 | 2 KB | 6 MB |
| AUDIT_ISSUE | 50 | 1,500 | 3 KB | 4.5 MB |
| **TOTAL** | | | | **~3.6 GB/month** |

**Scaling Projections:**

- **100 Workspaces**: ~360 GB/month
- **1,000 Workspaces**: ~3.6 TB/month
- **10,000 Workspaces**: ~36 TB/month

---

## Summary

This ER diagram documentation provides a complete data model for the GEO/AI Search Monitoring Platform:

1. **Complete ER Diagram**: Shows all entities and relationships
2. **Core Domain**: Workspace, users, topics, prompts, brands
3. **Monitoring**: Query plans, collection jobs, artifacts
4. **Analysis**: Answer records, citations, scoring
5. **Action**: Audits, recommendations, action items
6. **Platform**: Experiments, alerts, billing

**Key Design Principles:**
- **Normalization**: Minimize redundancy
- **Foreign Keys**: Maintain referential integrity
- **Indexes**: Optimize query performance
- **Retention**: Balance cost and compliance
- **Scalability**: Support multi-tenancy efficiently

---

**Last Updated:** December 26, 2025
**Maintained By:** Data Architecture Team
