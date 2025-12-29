# Pillar 1 Development Plan: Planner + Collectors (Observe)

**Last Updated:** December 26, 2025

This document consolidates Pillar 1 requirements and implementation details from:
- `docs/HIGH_LEVEL_DEVELOPMENT_PLAN.md`
- `docs/PLATFORM_ON_AGENT_FRAMEWORK.md`
- `docs/ARCHITECTURE_DIAGRAMS.md`
- `docs/ER_DIAGRAMS.md`
- `docs/_pdf_text/2430d902-8057-4f0b-8551-f81d072a519b__GEOAI_Search_Monitoring_Platform_Comprehensive_Development_Plan.txt`
- `docs/_pdf_text/b15cbcc1-dbe7-4410-a9fe-1222e924c672__GEOAI_Search_Monitoring_Platform_Development_Plan.txt`
- `docs/_pdf_text/0371b396-b092-4867-8e5a-3b1f2d73de1c_GEOAI_Search_Monitoring__Optimization_Platform.txt`
- `docs/_pdf_text/bf849806-315b-4047-a7fe-1e116a50ec5c_GEOAI_Search_Monitoring_platform.txt`
- `docs/_pdf_text/151c6b14-43be-45bf-b177-77749936303e_GEOAI_Analytics_Platform.txt`
- `docs/_pdf_text/3f86e0ea-4c0a-490c-98bc-13ba1d000609_GEOAI_Search_Monitoring_Platform_-_Business_Journey_Diagram.txt`

---

## 1) Scope and Objectives

**Business goal:** Establish visibility into how AI search engines describe and cite the brand versus competitors.

**Core capabilities:**
- **Discover & Plan:** Prompt and topic management, cadence, budget, engine/locale targeting.
- **Monitor & Understand:** Automated collection from AI engines, evidence capture, parsing/normalization.
- **Analytics & Scoring:** Answer share, citation share, prominence, sentiment, deltas.

**Out of scope:** GEO audit crawler, LLM emulator, experiment lab (Pillars 2 and 3).

---

## 1.1 Pillar 1 Breakdown (Parts)

**Part A: Discover & Plan**
- Define topics, prompts, engines, locales, cadence, and budgets.
- Output: active plans and schedules that drive collection jobs.

**Part B: Monitor & Understand**
- Collect answers and evidence from AI engines.
- Normalize responses and resolve brands/competitors.
- Output: structured answers, citations, and evidence.

**Part C: Analytics & Scoring**
- Compute Answer Share, Citation Share, Prominence, Sentiment, and deltas.
- Output: score snapshots and trends surfaced in dashboards.

---

## 2) Architecture Summary

**Key services:**
- **Orchestration:** AI Coordination Engine (coordinations, tasks, schedules, sessions).
- **Execution:** AI Agent Core Engine (config-driven agents for collection + normalization).
- **Processing:** Agent tools for parsing, brand resolution, and scoring.
- **Storage:** S3 for raw artifacts, DynamoDB for structured data, Neo4j GraphRAG for relationships.
- **Access:** GraphQL/REST API + React/Next.js dashboard.

**Data flow (high-level):**
1. Plans -> Coordination tasks + schedules
2. Coordination sessions -> agent execution
3. Collection agents -> S3 raw artifacts
4. Parser + resolver agents -> normalized entities
5. Scoring agents -> snapshots
6. Dashboard -> KPIs + evidence

**Coordination model (from AI Coordination Engine):**
- **Coordination** defines the blueprint for a workflow.
- **Task** defines what should run; **TaskSchedule** defines when.
- **Session** is a runtime instance that tracks state and runs agents.

### 2.1 Pillar 1 Architecture Diagram

```mermaid
graph TB
    subgraph "Plan & Discovery"
        PromptManager[Prompt & Topic Manager]
        PlanService[Plan Service]
        Schedule[Coordination Scheduler]
    end

    subgraph "AI Orchestration"
        Orchestrator[AI Coordination Engine]
        Sessions[Coordination Sessions]
    end

    subgraph "Agent Execution"
        AgentCore[AI Agent Core Engine]
        CollectorAgent[Collector Agent]
        Engines[AI Engines<br/>ChatGPT/Perplexity/Gemini]
    end

    subgraph "Evidence & Processing Agents"
        S3[S3 Raw Artifacts<br/>HTML/JSON/PNG]
        Parser[Parser Agent]
        Resolver[Brand Resolver Agent]
        Scorer[Scoring Agent]
    end

    subgraph "Data Stores"
        DDB[DynamoDB<br/>Plans/Jobs/Answers/Citations/Scores]
        Graph[Neo4j GraphRAG]
    end

    subgraph "Experience Layer"
        API[API Gateway + GraphQL/REST]
        Dashboard[Dashboard & Evidence Viewer]
    end

    PromptManager --> PlanService
    PlanService --> Schedule
    Schedule --> Orchestrator
    Orchestrator --> Sessions
    Sessions --> AgentCore
    AgentCore --> CollectorAgent
    CollectorAgent --> Engines
    CollectorAgent --> S3
    S3 --> Parser
    Parser --> DDB
    Parser --> Resolver
    Resolver --> DDB
    Resolver --> Graph
    Resolver --> Scorer
    Scorer --> DDB
    DDB --> API
    Graph --> API
    API --> Dashboard
```

---

## 3) Data Model (Pillar 1)

**Planning:**
- `topics`, `prompts`, `plans`
- `plan_prompt`, `plan_engine`, `plan_schedule`

**Collection & evidence:**
- `collection_job`, `raw_artifact`, `job_log`, `artifact_version`

**Normalized data:**
- `answer_record`, `citation`

**Competitive mapping:**
- `brands`, `brand_aliases`, `domain`

**Metrics:**
- `score_snapshot`

**References:** `docs/ER_DIAGRAMS.md` sections 2-4.

### 3.1 Pillar 1 ER Diagram (Focused)

```mermaid
erDiagram
    WORKSPACE ||--o{ TOPIC : contains
    TOPIC ||--o{ PROMPT : contains
    WORKSPACE ||--o{ QUERY_PLAN : defines
    QUERY_PLAN ||--o{ COLLECTION_JOB : generates
    COLLECTION_JOB ||--o{ RAW_ARTIFACT : produces
    RAW_ARTIFACT ||--|| ANSWER_RECORD : "parsed into"
    ANSWER_RECORD ||--o{ CITATION : contains
    CITATION }o--|| DOMAIN : links_to
    DOMAIN }o--|| BRAND : owned_by
    BRAND ||--o{ SCORE_SNAPSHOT : measured
    SCORE_SNAPSHOT }o--|| TOPIC : for
    SCORE_SNAPSHOT }o--|| ENGINE : on

    TOPIC {
        uuid topic_id PK
        uuid workspace_id FK
        string name
        text description
        json metadata
        timestamp created_at
    }

    PROMPT {
        uuid prompt_id PK
        uuid topic_id FK
        text text
        string locale
        string intent
        json variants
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

    BRAND {
        uuid brand_id PK
        uuid workspace_id FK
        string name
        json domains
        json properties
        timestamp created_at
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

    ENGINE {
        string engine_id PK
        string name
        string type
        json config
        string status
    }
```

---

## 4) Pillar 1 Workflow

1. **Plan creation**: Users define topics, prompts, engines, locales, cadence, and budget.
2. **Scheduling**: AI Coordination Engine creates tasks and sessions.
3. **Collection**: Collector agents query AI engines and capture answer + citations + screenshots.
4. **Parsing**: Parser agents normalize outputs into `answer_record` + `citation`.
5. **Brand resolution**: Alias/domain matching tags first-party vs competitor.
6. **Scoring**: Nightly batch job computes shares, prominence, sentiment, and deltas.
7. **Dashboard**: KPIs, evidence viewer, and comparisons.

### 4.0 Data Hierarchy Diagram (Pillar 1)

```mermaid
flowchart TD
    Workspace[Workspace] --> Topic[Topic]
    Topic --> Prompt[Prompt]
    Workspace --> Plan[Query Plan]
    Plan --> PlanPrompt[Plan Prompt]
    Plan --> PlanEngine[Plan Engine]
    Plan --> Schedule[Plan Schedule]
    Schedule --> Job[Collection Job]
    Job --> Artifact[Raw Artifact]
    Artifact --> Answer[Answer Record]
    Answer --> Citation[Citation]
    Citation --> Domain[Domain]
    Domain --> Brand[Brand]
    Answer --> Score[Score Snapshot]
```

### 4.0.1 Data Hierarchy (Analytics View)

```mermaid
flowchart TD
    Topic[Topic] --> Prompt[Prompt]
    Prompt --> Answer[Answer Record]
    Answer --> Citation[Citation]
    Citation --> Domain[Domain]
    Domain --> Brand[Brand]
    Answer --> Score[Score Snapshot]
    Score --> Trend[Trend Analysis]
```

### 4.0.2 Data Hierarchy (Storage View)

```mermaid
flowchart TD
    Plan[Query Plan] --> Job[Collection Job]
    Job --> S3[S3 Raw Artifacts]
    S3 --> Parser[Parser Agent]
    Parser --> DDB[DynamoDB Records]
    DDB --> Graph[Neo4j GraphRAG]
    DDB --> Dashboard[Dashboard APIs]
    Graph --> Dashboard
```

### 4.1 Collection Sequence Diagram

```mermaid
sequenceDiagram
    participant User
    participant Planner
    participant Coordination
    participant Agents
    participant Processing
    participant Dashboard

    User->>Planner: Define topics, prompts, cadence
    Planner->>Coordination: Create tasks + schedules
    Coordination->>Agents: Run collection tasks
    Agents->>Processing: Store artifacts, normalize, score
    Processing->>Dashboard: Publish KPIs + evidence
```

### 4.2 Collection Activity Diagram

```mermaid
flowchart TD
    Start([Schedule Trigger]) --> LoadPlans[Load Active Plans]
    LoadPlans --> CheckBudget{Within Budget?}
    CheckBudget -->|No| PausePlan[Pause Plan + Alert]
    CheckBudget -->|Yes| CreateTasks[Create Coordination Tasks]
    CreateTasks --> StartSession[Start Coordination Session]
    StartSession --> RunCollector[Collector Agent Runs Prompt]
    RunCollector --> Capture[Capture Answer + Citations + Screenshot]
    Capture --> Upload[Upload Artifacts to S3]
    Upload --> Parse[Parser Agent Normalizes Response]
    Parse --> Resolve[Brand Resolver Agent Tags Entities]
    Resolve --> Score[Scoring Agent Aggregation]
    Score --> End([Dashboard Updated])
```

### 4.3 Collection Job State Diagram

```mermaid
stateDiagram-v2
    [*] --> Pending
    Pending --> Scheduled: Task Scheduled
    Scheduled --> InProgress: Agent Acquired
    InProgress --> Collecting: Browser Launched
    Collecting --> Processing: Response Received
    Processing --> Uploading: Data Extracted
    Uploading --> Completed: S3 Upload Success
    InProgress --> Retrying: Timeout
    Collecting --> Retrying: Engine Error
    Processing --> Retrying: Parse Error
    Uploading --> Retrying: Upload Failed
    Retrying --> Scheduled: Retry Queued
    Retrying --> Failed: Max Retries Exceeded
    Scheduled --> Paused: Budget Exceeded
    Paused --> Scheduled: Resume Requested
    Completed --> [*]
    Failed --> [*]
```

### 4.4 Plan Creation Activity Diagram

```mermaid
flowchart TD
    Start([User Opens Planner]) --> CreateTopic[Create Topic]
    CreateTopic --> AddPrompts[Add Prompts + Variants]
    AddPrompts --> SelectEngines[Select Engines + Locales]
    SelectEngines --> SetCadence[Set Cadence + Timezone]
    SetCadence --> SetBudget[Set Budget Caps]
    SetBudget --> ReviewPlan[Review Plan Summary]
    ReviewPlan --> Activate{Activate Plan?}
    Activate -->|No| EditPlan[Edit Details]
    EditPlan --> ReviewPlan
    Activate -->|Yes| SavePlan[Persist Plan + Schedule]
    SavePlan --> ConfirmRun[Confirm Next Run Time]
    ConfirmRun --> End([Plan Active])
```

### 4.5 Part A Sequence Diagram (Discover & Plan)

```mermaid
sequenceDiagram
    participant User
    participant Planner
    participant PlanStore
    participant Coordination

    User->>Planner: Define topics, prompts, engines, cadence
    Planner->>PlanStore: Save plan + prompt configuration
    Planner->>Coordination: Register task schedules
    Coordination-->>User: Confirm next run time
```

### 4.6 Part B Sequence Diagram (Monitor & Understand)

```mermaid
sequenceDiagram
    participant Coordination
    participant Agents
    participant Engines
    participant Processing
    participant EvidenceStore

    Coordination->>Agents: Dispatch collection tasks
    Agents->>Engines: Query AI engines
    Engines-->>Agents: Return answers
    Agents->>EvidenceStore: Save artifacts + screenshots
    Agents->>Processing: Trigger normalization + tagging
```

### 4.7 Part C Sequence Diagram (Analytics & Scoring)

```mermaid
sequenceDiagram
    participant Processing
    participant Scoring
    participant AnalyticsStore
    participant Dashboard

    Processing->>Scoring: Provide normalized answers + citations
    Scoring->>AnalyticsStore: Write score snapshots
    AnalyticsStore->>Dashboard: Serve KPIs + trends
```

---

## 5) Phase Roadmap

### Phase 1 (Months 1-3): Planner + Collectors MVP

**Infrastructure & DevOps**
- AWS CDK/Terraform baseline (S3, DynamoDB, IAM, KMS, API Gateway).
- CI/CD with GitHub Actions + CloudWatch observability.

**Prompt & Topic Manager**
- CRUD for `topics`, `prompts`, `plans`.
- Configure cadence, budgets, engines, locales, and competitor tracking.
- API endpoints: `/plans`, `/prompts`, `/topics`.
- React UI for creation and management.
  - **Plan creation flow (user):**
    - Create topic (business vertical) and add prompts.
    - Select target engines, locales, and competitors.
    - Set cadence (daily/weekly/monthly) and budget caps.
    - Review and activate the plan.
  - **Plan creation tasks (technical):**
    - Validate inputs (prompt uniqueness per topic/locale, cadence schema).
    - Persist `topics`, `prompts`, `plans`, `plan_prompt`, `plan_engine`, `plan_schedule`.
    - Create a versioned plan snapshot for audit/history.
    - Emit a plan-activated event for scheduling.

**Orchestration**
- AI Coordination Engine schedules tasks and instantiates sessions.
- Budget guardrails and plan pause on exceed.
  - **Plan creation flow (user):**
    - Choose schedule type (cron/interval) and timezone.
    - Save schedule and confirm next run time.
  - **Plan creation tasks (technical):**
    - Create Task + TaskSchedule for the plan.
    - Instantiate Sessions (prompt x engine x locale).
    - Persist `collection_job` records with `scheduled_at`.
    - Enforce budget caps before scheduling; auto-pause on exceed.

**Collectors**
- Collector agents for ChatGPT + Perplexity.
- Evidence capture (HTML, JSON, screenshots) to S3 `/raw/{engine}/{date}`.
- Retries, rate limiting, proxy rotation.
  - **Plan creation flow (user):**
    - Confirm which engines run per plan and set run limits.
    - Optionally enable manual re-run for specific prompts.
  - **Plan creation tasks (technical):**
    - Store engine-specific collector config in `plan_engine.config`.
    - Seed sessions with tasks derived from the plan schedule.
    - Track per-plan run quotas and enforce per-engine rate limits.

**Parser & Normalizer**
- Parser agent on artifact arrival.
- Extract answer text + citations + metadata.
- Store normalized records in DynamoDB.
  - **Plan creation flow (user):**
    - No direct user action; implied by plan activation.
  - **Plan creation tasks (technical):**
    - Ensure parsing pipeline is registered for plan-specific S3 prefixes.
    - Validate normalized schema alignment per engine.

**Dashboard MVP**
- Answer share, citation share, evidence viewer.
- Basic filters and prompt explorer.
  - **Plan creation flow (user):**
    - Select plan to view; filter by engine, locale, or topic.
  - **Plan creation tasks (technical):**
    - Index prompt/plan metadata for fast filtering.
    - Ensure evidence links map to plan/job IDs.

**Success metrics**
- Collector success rate >97%
- Data freshness <60 minutes
- Dashboard load time <1.5s
- Prompt coverage 90%

---

### Phase 2 (Months 4-6): Competitive + Scoring Depth

**Brand & Competitor Resolver**
- `brands`, `brand_aliases`, `domain` tables.
- Fuzzy matching and domain parsing.
- Neo4j GraphRAG edges: Brand -> Domain -> Topic -> Citation.

**Scoring & Trends**
- Agent-driven batch aggregations to `score_snapshot`.
- Metrics: answer share, citation share, prominence, sentiment, deltas.
- Store snapshots for time-series queries.

**Dashboard v2**
- Competitor share graphs and multi-engine comparisons.
- Drill-down filters by engine, topic, locale, timeframe.

**Success metrics**
- Competitor coverage 90%+
- Weekly engagement >70%
- Action adoption tracking enabled

---

## 6) APIs (Pillar 1)

**Planning**
- `POST /plans`
- `POST /prompts`
- `POST /topics`
- `GET /plans/:id`

**Data access**
- `GET /answers`
- `GET /citations`
- `GET /scores`

**Evidence**
- Signed S3 URLs for screenshots and raw artifacts.

---

## 7) Operational Requirements

**Reliability**
- 99.9% dashboard availability
- >97% successful runs/day

**Performance**
- p95 query <1.5s
- Ingestion to dashboard <60 minutes

**Cost control**
- Per-workspace budgets and quotas
- Queue backpressure
- Auto-pause on budget exceed

**Security**
- KMS at rest, TLS in transit
- IAM least-privilege
- Signed evidence URLs

**Retention**
- Raw artifacts: 90 days hot -> Glacier
- Screenshots: 1 year retention
- Parsed data: indefinite

---

## 8) Risks and Mitigations

**Collector fragility**
- Engine UI changes can break scraping.
- Mitigation: per-engine adapter versioning + canary runs.

**Anti-bot and captcha**
- Mitigation: proxy rotation, human-like pacing, retry and fallback paths.

**Cost spikes**
- Mitigation: budget caps, run quotas, auto-pause, per-engine cost estimates.

**Data drift**
- Mitigation: schema validation in parser, QA sampling, alert on parse errors.

---

## 9) Deliverables Checklist

- Planner + collectors MVP (plans -> jobs -> answers -> dashboard)
- Engine connectors for 2 engines (ChatGPT + Perplexity)
- Parser + normalization pipeline
- Evidence capture (HTML/JSON/PNG)
- Brand resolver + scoring engine
- Dashboard v1 + v2 analytics
- APIs for plans and data retrieval
- Observability dashboards (success rate, latency, cost/run)

---

## 10) KPIs (Pillar 1)

- Collector success rate: >97%
- Data freshness: <60 minutes
- Dashboard load time: <1.5s
- Prompt coverage: 90%
- Competitor coverage: 90%+ (Phase 2)
- Weekly engagement: >70%
