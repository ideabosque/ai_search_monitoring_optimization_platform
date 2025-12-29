# GEO/AI Search Monitoring Platform - Architecture Diagrams

**Created:** December 26, 2025
**Purpose:** Comprehensive visual documentation of system architecture, workflows, and state management

---

## Table of Contents

1. [System Architecture Diagrams](#1-system-architecture-diagrams)
2. [Sequence Diagrams](#2-sequence-diagrams)
3. [Activity Diagrams](#3-activity-diagrams)
4. [State Diagrams](#4-state-diagrams)

---

## 1. System Architecture Diagrams

### 1.1 High-Level System Architecture

This diagram shows the three major pillars (Observe, Diagnose, Predict & Prove) supported by shared platform services and the AI Agent Framework.

```mermaid
graph TB
    subgraph "Business Users"
        Marketing[Marketing & Growth Teams]
        SEO[SEO & Content Teams]
        Execs[Executives & Stakeholders]
    end

    subgraph "Experience Layer"
        Dashboard[Dashboard & Evidence Viewer]
        ActionCenter[Action Center]
        API[Partner & Customer API]
    end

    subgraph "Three Core Pillars"
        direction TB
        Observe[Pillar 1: Observe<br/>Planner + Collectors]
        Diagnose[Pillar 2: Diagnose<br/>GEO Audit Crawler]
        Predict[Pillar 3: Predict & Prove<br/>LLM Emulator + Experiment Lab]
    end

    subgraph "AI Agent Framework - Intelligence Core"
        CoordEngine[AI Coordination Engine<br/>ideabosque/ai_coordination_engine]
        AgentCore[AI Agent Core Engine<br/>ideabosque/ai_agent_core_engine]
    end

    subgraph "Knowledge & Evidence Layer"
        GraphRAG[Neo4j GraphRAG<br/>neo4j-graphrag-python]
        Evidence[Evidence Store<br/>Snapshots, Citations, Artifacts]
    end

    subgraph "External Ecosystem"
        AIEngines[AI Engines<br/>ChatGPT, Gemini, Perplexity]
        Tools[Work Management & Comms<br/>Jira, Slack, Webhooks]
    end

    Marketing --> Dashboard
    SEO --> Dashboard
    Execs --> Dashboard

    Dashboard --> Observe
    Dashboard --> Diagnose
    Dashboard --> Predict
    ActionCenter --> Tools
    API --> Observe

    Observe --> AIEngines
    Observe --> Evidence
    Diagnose --> Evidence
    Predict --> Evidence

    Observe --> CoordEngine
    Diagnose --> CoordEngine
    Predict --> CoordEngine
    CoordEngine --> AgentCore
    AgentCore --> GraphRAG

    Evidence --> GraphRAG
    GraphRAG --> Dashboard

    %% Styling
    style CoordEngine fill:#c5cae9,stroke:#5c6bc0,stroke-width:2px
    style AgentCore fill:#e1bee7,stroke:#8e24aa,stroke-width:2px
    style GraphRAG fill:#fce4ec
```

**Description:**

This business-level view emphasizes outcomes over implementation detail:

- **Who**: Marketing, SEO, and executive teams use the experience layer
- **How**: Observe -> Diagnose -> Predict & Prove
- **Intelligence**: ai_coordination_engine + ai_agent_core_engine
- **Knowledge**: Neo4j GraphRAG unifies evidence and retrieval

**Principles:** clarity, evidence traceability, and a closed improvement loop.

---

### 1.1a Three-Pillar Summary (Presentation-Friendly)

A compact diagram that highlights only the three major pillars and how they relate to each other.

```mermaid
graph LR
    P1[Pillar 1: Planner + Collectors<br/>Observe]
    P2[Pillar 2: GEO Audit Crawler<br/>Diagnose]
    P3[Pillar 3: LLM Emulator + Experiment Lab<br/>Predict & Prove]

    P1 --> P2
    P2 --> P3
    P3 --> P1
```

---

### 1.2 AI Agent Framework Architecture

This diagram shows the AI Agent Core Engine and Coordination Engine that power intelligent automation across the platform.

> **For complete framework documentation**, see [PLATFORM_ON_AGENT_FRAMEWORK.md](PLATFORM_ON_AGENT_FRAMEWORK.md)

```mermaid
graph TB
    subgraph "AI Coordination Engine"
        WorkflowOrchestrator[Workflow Orchestrator<br/>ideabosque/ai_coordination_engine<br/>Multi-Agent Workflows]
        TaskDispatcher[Task Dispatcher<br/>Agent Assignment]
        CommBus[Communication Bus<br/>Agent Messaging]
        StateManager[State Manager<br/>Workflow Progress]
        ResultSynthesizer[Result Synthesizer<br/>Output Aggregation]
    end

    subgraph "AI Agent Core Engine"
        AgentExecutor[Agent Executor<br/>ideabosque/ai_agent_core_engine<br/>LLM Reasoning Engine]
        ToolRegistry[Tool Registry<br/>Available Actions]
        MemoryManager[Memory Manager<br/>Context Retention]
        PromptTemplates[Prompt Templates<br/>Task Definitions]
        ResponseParser[Response Parser<br/>Structure Extraction]
    end

    subgraph "Specialized Agents"
        InsightAgent[Insight Generation Agent<br/>Recommendation Analysis]
        ContentAgent[Content Outline Agent<br/>SEO Optimization]
        FixRecipeAgent[Fix Recipe Agent<br/>Technical Solutions]
        CompetitiveAgent[Competitive Analysis Agent<br/>Market Intelligence]
        AuditAgent[Audit Analysis Agent<br/>Issue Detection]
        ExperimentAgent[Experiment Design Agent<br/>Test Planning]
    end

    subgraph "Platform Integration"
        AuditCrawler[Audit Crawler]
        InsightsEngine[Insights Engine]
        Emulator[LLM Emulator]
        BrandResolver[Brand Resolver]
        Scorer[Scoring Engine]
    end

    subgraph "External Services"
        OpenAI[OpenAI API]
        Anthropic[Anthropic Claude API]
        DynamoDB[(DynamoDB)]
        S3[(S3 Storage)]
    end

    WorkflowOrchestrator --> TaskDispatcher
    TaskDispatcher --> InsightAgent
    TaskDispatcher --> ContentAgent
    TaskDispatcher --> FixRecipeAgent
    TaskDispatcher --> CompetitiveAgent
    TaskDispatcher --> AuditAgent
    TaskDispatcher --> ExperimentAgent

    InsightAgent --> AgentExecutor
    ContentAgent --> AgentExecutor
    FixRecipeAgent --> AgentExecutor
    CompetitiveAgent --> AgentExecutor
    AuditAgent --> AgentExecutor
    ExperimentAgent --> AgentExecutor

    AgentExecutor --> ToolRegistry
    AgentExecutor --> MemoryManager
    AgentExecutor --> PromptTemplates
    AgentExecutor --> ResponseParser

    ToolRegistry --> DynamoDB
    ToolRegistry --> S3
    MemoryManager --> DynamoDB

    AgentExecutor --> OpenAI
    AgentExecutor --> Anthropic

    InsightAgent --> ResultSynthesizer
    ContentAgent --> ResultSynthesizer
    FixRecipeAgent --> ResultSynthesizer
    CompetitiveAgent --> ResultSynthesizer
    AuditAgent --> ResultSynthesizer
    ExperimentAgent --> ResultSynthesizer

    ResultSynthesizer --> CommBus
    CommBus --> StateManager
    StateManager --> WorkflowOrchestrator

    AuditCrawler --> WorkflowOrchestrator
    InsightsEngine --> InsightAgent
    Emulator --> ContentAgent
    BrandResolver --> CompetitiveAgent
    Scorer --> InsightAgent

    style WorkflowOrchestrator fill:#e3f2fd
    style AgentExecutor fill:#fff3e0
    style InsightAgent fill:#f3e5f5
    style ContentAgent fill:#e8f5e9
    style ToolRegistry fill:#fce4ec
```

**Description:**

The AI Agent Framework is split into two engines with clear roles:

- **AI Coordination Engine** (ideabosque/ai_coordination_engine): orchestrates multi-agent workflows
- **AI Agent Core Engine** (ideabosque/ai_agent_core_engine): executes reasoning and tool calls
- **Specialized agents** plug in as configs to deliver recommendations

The framework integrates with platform services and external LLMs while keeping core logic centralized.

---

### 1.3 Data Architecture & Flow

This diagram illustrates the polyglot persistence strategy and complete data lifecycle from ingestion to archival.

```mermaid
graph TB
    subgraph "Data Ingestion Layer"
        Collectors[Collector Service<br/>Agent Runtime Containers]
        RawArtifacts[S3 Standard Bucket<br/>/raw/engine/date/job_id/]
        ArtifactEvents[S3 Event Notifications<br/>ObjectCreated:*]
    end

    subgraph "Event-Driven Processing Pipeline"
        ParserAgent[Parser Agent<br/>Extract & Normalize]
        BrandResolverAgent[Brand Resolver Agent<br/>Fuzzy Entity Matching]
        ScoringAgent[Scoring Agent<br/>Metrics Calculation]
        CoordinationEngine[AI Coordination Engine<br/>ideabosque/ai_coordination_engine<br/>Orchestration & Retry Logic]
    end

    subgraph "Polyglot Persistence - Write Optimized"
        DynamoDB[(DynamoDB Tables<br/>--------------<br/>Plans -> Jobs -> Answers<br/>Citations -> Scores<br/>Audits -> Experiments<br/>--------------<br/>OLTP - Fast Writes)]


        Neo4j[(Neo4j + GraphRAG<br/>neo4j-graphrag-python<br/>github.com/neo4j/neo4j-graphrag-python<br/>--------------<br/>Brand -> Domain<br/>Domain -> Citation<br/>Topic -> Prompt<br/>--------------<br/>Relationship Queries)]

    end

    subgraph "Caching & Session Layer"
        Redis[(Redis/ElastiCache<br/>--------------<br/>API Rate Limits<br/>Hot Query Results<br/>User Sessions<br/>--------------<br/>TTL: 5min - 24hr)]
    end

    subgraph "Archival & Compliance"
        Glacier[(S3 Glacier Deep Archive<br/>--------------<br/>Raw artifacts 90+ days<br/>Audit logs 7 years<br/>Compliance retention<br/>--------------<br/>$1/TB/month)]
        LifecyclePolicy[S3 Lifecycle Rules<br/>Standard -> Glacier: 90 days<br/>Glacier -> Delete: 7 years]
    end

    subgraph "Data Access Layer - Read Optimized"
        GraphQL[GraphQL API<br/>Flexible Queries<br/>DynamoDB + Neo4j]
        Streaming[Streaming API<br/>Real-time Updates<br/>WebSockets]
        Export[Export Service<br/>CSV, PDF, JSON<br/>Bulk Downloads]
    end

    subgraph "External Consumers"
        Dashboard[Web Dashboard<br/>React + Charts]
        Webhooks[Customer Webhooks<br/>Event Subscriptions]
        Integrations[Third-Party Tools<br/>Jira, Slack, BI Tools]
    end

    %% Data ingestion flow
    Collectors -->|Raw HTML/JSON<br/>Screenshots| RawArtifacts
    RawArtifacts -->|Event Trigger| ArtifactEvents
    ArtifactEvents --> CoordinationEngine

    %% Processing pipeline
    CoordinationEngine --> ParserAgent
    ParserAgent -->|Normalized Records| DynamoDB
    ParserAgent --> BrandResolverAgent

    BrandResolverAgent -->|Brand Entities| Neo4j
    BrandResolverAgent -->|Tagged Records| DynamoDB
    BrandResolverAgent --> ScoringAgent

    ScoringAgent -->|Score Snapshots| DynamoDB

    %% Archival lifecycle
    RawArtifacts -->|After 90 days| LifecyclePolicy
    LifecyclePolicy --> Glacier

    %% Data access patterns
    DynamoDB --> GraphQL
    Neo4j --> GraphQL
    DynamoDB --> Redis

    GraphQL --> Dashboard
    Streaming --> Dashboard
    Export --> Dashboard

    GraphQL --> Webhooks
    GraphQL --> Integrations

    %% Styling
    style DynamoDB fill:#4db6ac,color:#000
    style Neo4j fill:#ef5350,color:#fff
    style Redis fill:#ec407a,color:#fff
    style RawArtifacts fill:#90a4ae,color:#fff
    style Glacier fill:#546e7a,color:#fff
    style ParserAgent fill:#66bb6a,color:#000
    style Dashboard fill:#42a5f5,color:#fff
```

**Description:**

GraphRAG-first data flow that prioritizes evidence traceability:

- Collect -> normalize -> resolve -> score
- Persist core entities in DynamoDB and relationships/embeddings in Neo4j GraphRAG
- Access via GraphQL, Streaming, and Export

This keeps retrieval grounded in graph context while preserving operational data in DynamoDB.

| Store | Purpose | Data Examples | Access Pattern |
| --- | --- | --- | --- |
| **DynamoDB** | Transactional OLTP, fast writes/reads by key | Plans, Jobs, Answers, Citations, Scores | Get by ID, Query by index |
| **Neo4j + GraphRAG** | Graph + vector retrieval (hybrid) | Brand->Domain->Citation networks + embeddings | Graph traversal + semantic search |
| **Redis** | Hot cache, sessions, rate limiting | Query results (TTL 5-60min), user sessions | Fast key-value access |
| **S3 Standard** | Raw artifacts, unstructured data | HTML responses, screenshots, JSON | Object storage, versioned |
| **S3 Glacier** | Cold archival, compliance retention | Raw data 90+ days old, audit logs 7yr | Rare access, regulatory |

**Data Lifecycle:**

1. **Ingestion**: Collectors capture raw artifacts -> S3 Standard
2. **Event Trigger**: S3 ObjectCreated event -> Triggers AI Coordination Engine
3. **Processing**: Parser -> Brand Resolver -> Scorer (orchestrated by AI Coordination Engine)
4. **Multi-Write**: Each stage writes to appropriate database(s)
   - DynamoDB: Metadata, entities, scores
   - Neo4j + GraphRAG: Relationship edges + vector search
5. **Caching**: Hot queries cached in Redis (5min-24hr TTL)
6. **Access**: GraphQL (flexible), Streaming (real-time), Export (bulk)
7. **Archival**: S3 Lifecycle -> Glacier after 90 days -> Delete after 7 years

**Data Retention Policy:**

- **Raw Artifacts (S3)**: 90 days hot -> Glacier indefinitely
- **Screenshots**: 1 year -> Delete
- **Parsed Data (DynamoDB/Neo4j + GraphRAG)**: Indefinite retention
- **Audit Logs**: 7 years (compliance requirement)
- **Experiment Results**: Indefinite (learning repository)
- **Cache (Redis)**: 5min-24hr TTL based on data type

**Consistency Model:**

- **Eventual Consistency**: Across DynamoDB and Neo4j + GraphRAG (acceptable lag: <5 seconds)
- **Strong Consistency**: DynamoDB reads when required (e.g., billing)
- **Cache Invalidation**: Redis TTL-based + explicit invalidation on writes

---

### 1.4 Service Decomposition & Deployment Architecture

Detailed view of service deployment, scaling strategy, and inter-service communication patterns.

```mermaid
graph TB
    subgraph "Serverless - Agent Functions"
        direction LR
        ParserAgent[Parser Service<br/>--------<br/>Runtime: Python 3.11<br/>Memory: 512MB<br/>Timeout: 5min<br/>--------<br/>Trigger: S3 Events]

        ScorerAgent[Scorer Service<br/>--------<br/>Runtime: Python 3.11<br/>Memory: 1024MB<br/>Timeout: 15min<br/>--------<br/>Trigger: Coordination Scheduler Cron]

        BrandResolverAgent[Brand Resolver<br/>--------<br/>Runtime: Python 3.11<br/>Memory: 512MB<br/>Timeout: 3min<br/>--------<br/>Trigger: Task Dispatch Queue]

        APIAgents[API Handlers<br/>--------<br/>Runtime: Node.js 18<br/>Memory: 256MB<br/>Timeout: 30s<br/>--------<br/>Trigger: API Gateway]
    end

    subgraph "Containerized - ECS Agent Runtime"
        direction LR
        CollectorAgents[Collector Service<br/>--------<br/>Image: collector:latest<br/>CPU: 2 vCPU<br/>Memory: 4 GB<br/>--------<br/>Playwright + Chrome<br/>Autoscale: 1-50 tasks]

        AuditCrawlerTasks[Audit Crawler<br/>--------<br/>Image: audit-crawler:latest<br/>CPU: 4 vCPU<br/>Memory: 8 GB<br/>--------<br/>Deep Site Analysis<br/>Autoscale: 1-10 tasks]

        AgentRuntime[AI Agent Runtime<br/>--------<br/>Image: agent-core:latest<br/>CPU: 2 vCPU<br/>Memory: 4 GB<br/>--------<br/>ai_agent_core_engine<br/>Autoscale: 2-20 tasks]
    end

    subgraph "Orchestration & Workflow"
        CoordinationEngine[AI Coordination Engine<br/>ideabosque/ai_coordination_engine<br/>--------<br/>Parser Pipeline<br/>Scoring Workflow<br/>Experiment Workflow<br/>--------<br/>Workflows + Retry Logic]

        CoordinationScheduler[Amazon Coordination Scheduler<br/>--------<br/>Scheduled Rules: Cron<br/>Event Patterns: S3, DynamoDB<br/>Event Bus: Custom Events<br/>--------<br/>Event Router]

        TaskDispatch[Amazon Task Dispatch<br/>--------<br/>collection-jobs-queue<br/>parsing-tasks-queue<br/>brand-resolution-queue<br/>--------<br/>Visibility: 60s, DLQ enabled]
    end

    subgraph "Configuration & Service Discovery"
        direction LR
        PlanService[Plan Service<br/>--------<br/>Deployment: Agent<br/>Database: DynamoDB<br/>--------<br/>CRUD Plans<br/>Schedule Management]

        PromptManager[Prompt Manager<br/>--------<br/>Deployment: Agent<br/>Database: DynamoDB<br/>--------<br/>Versioning<br/>A/B Testing Config]

        TenantService[Tenant Service<br/>--------<br/>Deployment: Agent<br/>Database: DynamoDB<br/>--------<br/>Multi-Tenancy<br/>Quota Enforcement]
    end

    subgraph "AI-Powered Action Services"
        direction LR
        InsightsEngine[Insights Engine<br/>--------<br/>Deployment: Agent Runtime<br/>Agent: Insight Generation<br/>--------<br/>Recommendation<br/>Prioritization]

        EmulatorService[LLM Emulator<br/>--------<br/>Deployment: Agent Runtime<br/>Agent: Content Outline<br/>--------<br/>A/B Testing<br/>Judge Model]

        ExperimentService[Experiment Service<br/>--------<br/>Deployment: Agent<br/>Agent: Experiment Design<br/>--------<br/>Stats Analysis<br/>Bayesian Inference]
    end

    subgraph "Communication & Integration"
        direction LR
        AlertService[Alert Service<br/>--------<br/>Deployment: Agent<br/>Trigger: Coordination Scheduler<br/>--------<br/>Rule Evaluation<br/>Multi-Channel Routing]

        WebhookService[Webhook Service<br/>--------<br/>Deployment: Agent<br/>Trigger: SNS<br/>--------<br/>Event Publishing<br/>Retry + Signature]

        IntegrationService[Integration Service<br/>--------<br/>Deployment: Agent<br/>APIs: Jira, Slack, Notion<br/>--------<br/>Bidirectional Sync<br/>OAuth Management]
    end

    subgraph "Platform Cross-Cutting"
        direction LR
        AuthService[Auth Service<br/>--------<br/>Deployment: Agent<br/>Provider: AWS Cognito<br/>--------<br/>JWT Validation<br/>RBAC Enforcement]

        BillingService[Billing Service<br/>--------<br/>Deployment: Agent<br/>Provider: Stripe<br/>--------<br/>Usage Metering<br/>Invoice Generation]
    end

    %% Connections - Discovery to Orchestration
    PlanService --> CoordinationScheduler
    PromptManager --> CoordinationScheduler
    CoordinationScheduler --> TaskDispatch

    %% Connections - Collection Pipeline
    TaskDispatch --> CollectorAgents
    CollectorAgents --> CoordinationEngine
    CoordinationEngine --> ParserAgent
    ParserAgent --> BrandResolverAgent
    BrandResolverAgent --> ScorerAgent

    %% Connections - AI Agents
    InsightsEngine --> AgentRuntime
    EmulatorService --> AgentRuntime
    ExperimentService --> AgentRuntime
    AuditCrawlerTasks --> AgentRuntime

    %% Connections - Alerts and Notifications
    ScorerAgent --> AlertService
    AlertService --> WebhookService
    InsightsEngine --> IntegrationService

    %% Connections - Platform Services
    APIAgents --> AuthService
    APIAgents --> TenantService
    APIAgents --> BillingService
    PlanService --> TenantService

    %% Styling
    style AgentRuntime fill:#e1bee7,stroke:#8e24aa,stroke-width:3px
    style CollectorAgents fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style AuditCrawlerTasks fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style ParserAgent fill:#e8f5e9,stroke:#43a047,stroke-width:2px
    style ScorerAgent fill:#e8f5e9,stroke:#43a047,stroke-width:2px
    style InsightsEngine fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px
    style EmulatorService fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px
    style CoordinationEngine fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px
    style CoordinationScheduler fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px
```

**Description:**

Services are intentionally thin and aligned to one responsibility:

- **Serverless** for bursty workflows and APIs
- **Agent Runtime** for long-running collectors and agents
- **AI Coordination Engine** for workflow control

This supports scale, cost control, and clear ownership boundaries.

| Service Class | Runtime | Scale Unit | Responsibilities |
| --- | --- | --- | --- |
| Stateless APIs | Agent | Concurrent executions (1-1000) | HTTP request handlers |
| Event Processors | Agent | Task Dispatch/S3 event triggers | Parser, Scorer, Brand Resolver |
| Heavy Compute | Agent Runtime | Task count (1-50) | Collectors (browser automation) |
| Long-Running | Agent Runtime | Task count (2-20) | AI Agent Runtime, Audit Crawler |
| Orchestration | AI Coordination Engine | Workflows | Multi-step pipelines with retry |
| Scheduling | Coordination Scheduler | Cron rules + event patterns | Daily collection, scoring jobs |

**Service Communication Patterns:**

1. **Synchronous (Request/Response)**:
   - API Gateway → Agent handlers
  - Agent → DynamoDB/Neo4j GraphRAG queries
   - Agent Runtime → OpenAI API calls

2. **Asynchronous (Event-Driven)**:
   - S3 ObjectCreated → Parser Agent
   - Task Dispatch Queue → Collector Agent Runtime tasks
   - Coordination Scheduler Cron → Scorer Agent
   - SNS Topic → Webhook Service

3. **Orchestrated (AI Coordination Engine)**:
   - Collection Pipeline: Collector → Parser → Brand Resolver → Scorer
   - Experiment Workflow: Setup → Baseline → Treatment → Analysis
   - Retry Logic: Exponential backoff for failed steps

**Service Sizing Guidelines:**

**Agent Functions:**
- **Parser**: 512MB, 5min timeout (handles large HTML)
- **Scorer**: 1024MB, 15min timeout (aggregates large datasets)
- **API Handlers**: 256MB, 30s timeout (fast response)
- **Brand Resolver**: 512MB, 3min timeout (fuzzy matching)

**Agent Runtime Tasks:**
- **Collector**: 2 vCPU, 4GB RAM (Playwright browser automation)
- **Audit Crawler**: 4 vCPU, 8GB RAM (deep site analysis, many pages)
- **AI Agent Runtime**: 2 vCPU, 4GB RAM (LLM calls, tool execution)

**Key Design Principles:**

1. **Thin Services (50-100 LOC)**: Business logic in configs, not code
2. **Single Responsibility**: Each service has one clear job
3. **Independently Deployable**: No shared code, versioned APIs
4. **Resilient**: Retry logic, DLQs, circuit breakers
5. **Observable**: Structured logs, traces, metrics per service
6. **Cost-Optimized**: Serverless scales to zero when idle

---

## 2. Sequence Diagrams

### 2.1 Daily Monitoring Flow

Complete sequence from plan creation to dashboard visualization, showing the event-driven pipeline.

```mermaid
sequenceDiagram
    actor User
    participant Dashboard
    participant Platform as "Platform Services"
    participant Collectors as "Planner + Collectors"
    participant Diagnostics as "Processing + Scoring"
    participant Insights as "Alerts + Insights"
    participant AICoord as "AI Coordination Engine\nideabosque/ai_coordination_engine"
    participant AgentCore as "AI Agent Core Engine\nideabosque/ai_agent_core_engine"
    participant Slack

    Note over User,Slack: PHASE 1: Plan Creation & Scheduling
    User->>Dashboard: Define monitoring plan<br/>(topics, prompts, cadence)
    Dashboard->>Platform: Save plan + schedule
    Platform-->>Dashboard: Plan active (daily run)
    Dashboard-->>User: Confirmation + next run time

    Note over User,Slack: PHASE 2: Scheduled Execution (Daily Trigger)
    Platform->>Platform: Trigger daily run
    Platform->>Collectors: Generate collection tasks

    Note over User,Slack: PHASE 3: Collection Execution (Auto-Scaling)
    Collectors->>Collectors: Query AI engines + capture evidence
    Collectors-->>Diagnostics: Raw answers + artifacts available

    Note over User,Slack: PHASE 4: Processing Pipeline (Event-Driven)
    Diagnostics->>Diagnostics: Parse, resolve brands, score visibility
    Diagnostics-->>Insights: Metrics + deltas published
    Insights->>AICoord: Request summary + insights
    AICoord->>AgentCore: Execute analysis
    AgentCore-->>Insights: Recommendations ready

    Note over User,Slack: PHASE 5: Alerting & Notifications
    Insights->>Insights: Evaluate thresholds
    alt Alert threshold crossed
        Insights->>Slack: Send alert notification
    end
    Insights->>Dashboard: Push updated metrics

    Note over User,Slack: PHASE 6: User Visualization
    User->>Dashboard: View daily report
    Dashboard-->>User: Display metrics, charts, evidence
```

**Description:**

A concise daily loop from plan to insights:

- Define monitoring plan and schedule
- Collect AI answers and evidence
- Score visibility and publish metrics
- Notify on changes and visualize results

---

### 2.2 GEO Audit to Action Center Flow with AI Agent Orchestration

This sequence demonstrates how the AI Coordination Engine orchestrates multiple specialized agents to transform audit results into prioritized, actionable recommendations.

```mermaid
sequenceDiagram
    actor User
    participant Dashboard
    participant Platform as "Platform Services"
    participant Audit as "GEO Audit Crawler"
    participant AICoord as "AI Coordination Engine\nideabosque/ai_coordination_engine"
    participant AgentCore as "AI Agent Core Engine\nideabosque/ai_agent_core_engine"
    participant ActionCenter as "Action Center"
    participant Jira

    Note over User,Jira: PHASE 1: Audit Execution
    User->>Dashboard: Start GEO audit<br/>(domain, sitemap URL)
    Dashboard->>Platform: Create audit + start run
    Platform->>Audit: Crawl site + collect issues
    Audit-->>Platform: Issues + evidence ready

    Note over User,Jira: PHASE 2: AI Agent Orchestration (Multi-Agent Workflow)
    Platform->>AICoord: Run multi-agent analysis
    AICoord->>AgentCore: Execute reasoning + tools
    AgentCore-->>AICoord: Findings + ranked impact
    AICoord-->>ActionCenter: Recommendations + fix guidance

    Note over User,Jira: PHASE 3: User Review & Action
    User->>Dashboard: View Action Center
    ActionCenter-->>Dashboard: Prioritized actions + evidence
    Dashboard-->>User: Review recommendations
    User->>Dashboard: Create Jira ticket
    Dashboard->>Jira: Create issue with fix guidance
    Jira-->>Dashboard: Issue created (PROJ-123)
    Dashboard-->>User: Task created
```

**Description:**

A product-level flow from audit to action:

- Crawl and capture issues
- Use multi-agent reasoning to diagnose and prioritize
- Deliver recommendations to the Action Center
- Create execution tasks (e.g., Jira)

---

### 2.3 LLM Emulator Testing Flow

Pre-publish content testing workflow.

```mermaid
sequenceDiagram
    actor ContentEditor
    participant Dashboard
    participant Emulator as "LLM Emulator + Judge"
    participant Results as "Results + Recommendation"
    participant AICoord as "AI Coordination Engine\nideabosque/ai_coordination_engine"
    participant AgentCore as "AI Agent Core Engine\nideabosque/ai_agent_core_engine"
    participant ActionCenter

    ContentEditor->>Dashboard: Start pre-publish test
    Dashboard->>Emulator: Submit variants + prompts
    Emulator->>AICoord: Orchestrate evaluation
    AICoord->>AgentCore: Execute scoring + judgment
    AgentCore-->>Emulator: Scores + reasoning
    Emulator-->>Results: Scores + recommendation
    Results-->>Dashboard: Summary report
    Dashboard-->>ContentEditor: Review decision

    alt Recommended to publish
        ContentEditor->>ActionCenter: Approve change
    else Needs revision
        ContentEditor->>Dashboard: Revise content
    end
```

**Description:**

A pre-publish validation loop:

- Submit variants and prompts
- Emulate outcomes and judge quality
- Recommend publish or revise

---

### 2.4 Alert and Notification Flow

Real-time alerting when metrics change significantly.

```mermaid
sequenceDiagram
    participant Scorer
    participant MetricStream
    participant AlertService
    participant RuleEngine
    participant NotificationRouter
    participant Slack
    participant Email
    participant Webhook
    participant Dashboard

    Scorer->>MetricStream: Publish daily scores
    MetricStream->>AlertService: Stream metric events

    AlertService->>RuleEngine: Evaluate rules

    alt Answer Share dropped >10%
        RuleEngine->>RuleEngine: Rule matched: DROP_THRESHOLD
        RuleEngine->>NotificationRouter: Trigger alert

        NotificationRouter->>NotificationRouter: Determine channels:<br/>- Severity: HIGH<br/>- Team: Marketing

        par Send to multiple channels
            NotificationRouter->>Slack: POST webhook
            Slack-->>NotificationRouter: Delivered
        and
            NotificationRouter->>Email: Send via SES
            Email-->>NotificationRouter: Sent
        and
            NotificationRouter->>Webhook: POST to customer endpoint
            Webhook-->>NotificationRouter: Acknowledged
        end

        NotificationRouter->>Dashboard: Push real-time update
        Dashboard->>Dashboard: Show notification badge
    end

    alt Competitor gained >15%
        RuleEngine->>RuleEngine: Rule matched: COMPETITOR_SURGE
        RuleEngine->>NotificationRouter: Trigger alert

        NotificationRouter->>Slack: Send detailed report
        Note over Slack: Include:<br/>- Competitor name<br/>- Gained prompts<br/>- Evidence links
    end

    alt Weekly digest schedule
        RuleEngine->>RuleEngine: Time-based rule: WEEKLY_DIGEST
        RuleEngine->>NotificationRouter: Generate digest

        NotificationRouter->>Scorer: Query weekly summary
        Scorer-->>NotificationRouter: Aggregate data

        NotificationRouter->>NotificationRouter: Format digest:<br/>- Top wins<br/>- Top losses<br/>- Action items<br/>- Trends

        NotificationRouter->>Email: Send digest
        Email-->>NotificationRouter: Sent to team
    end
```

**Description:**

A simple alert lifecycle:

- Ingest metric changes
- Evaluate rules and route notifications
- Track acknowledgement and resolution

---

## 3. Activity Diagrams

### 3.1 User Journey: From Insight to Action

End-to-end user workflow from discovering an issue to implementing a fix.

```mermaid
flowchart TD
    Start([User Logs In]) --> ViewDashboard[View Dashboard]
    ViewDashboard --> CheckMetrics{Check Metrics}

    CheckMetrics -->|All Good| SetupMonitoring[Set Up New Monitoring]
    CheckMetrics -->|Issue Detected| InvestigateIssue[Investigate Issue]

    InvestigateIssue --> ViewEvidence[View Evidence:<br/>- Screenshots<br/>- Competitor Comparison<br/>- Lost Prompts]

    ViewEvidence --> RunAudit{Run GEO Audit?}

    RunAudit -->|Yes| ExecuteAudit[Execute Site Audit]
    RunAudit -->|No| CheckActionCenter[Check Action Center]

    ExecuteAudit --> ReviewIssues[Review Detected Issues]
    ReviewIssues --> CheckActionCenter

    CheckActionCenter --> PrioritizeActions[Review Prioritized Actions]
    PrioritizeActions --> SelectAction{Select Action}

    SelectAction -->|Content Fix| GenerateOutline[Generate Content Outline]
    SelectAction -->|Technical Fix| ViewFixRecipe[View Fix Recipe]
    SelectAction -->|Need More Data| RequestAnalysis[Request Deeper Analysis]

    GenerateOutline --> TestContent{Test Before Publishing?}
    ViewFixRecipe --> TestContent

    TestContent -->|Yes| RunEmulator[Run LLM Emulator]
    TestContent -->|No| CreateTask[Create Implementation Task]

    RunEmulator --> ReviewResults{Emulator Results}
    ReviewResults -->|Positive| CreateTask
    ReviewResults -->|Negative| RefineContent[Refine Content]
    RefineContent --> RunEmulator

    CreateTask --> IntegrateJira[Create Jira Ticket / Notion Task]
    IntegrateJira --> AssignTeam[Assign to Content/Dev Team]

    AssignTeam --> ImplementFix[Team Implements Fix]
    ImplementFix --> PublishChanges[Publish Changes]

    PublishChanges --> SetupExperiment{Setup Experiment?}
    SetupExperiment -->|Yes| ConfigureExperiment[Configure A/B or Pre/Post Test]
    SetupExperiment -->|No| MonitorImpact[Monitor Impact]

    ConfigureExperiment --> WaitForData[Wait for Data Collection]
    WaitForData --> AnalyzeResults[Analyze Experiment Results]

    AnalyzeResults --> MeasureImpact{Significant Lift?}
    MeasureImpact -->|Yes| DocumentSuccess[Document Success]
    MeasureImpact -->|No| RefineApproach[Refine Approach]

    DocumentSuccess --> ShareLearning[Share Learning with Team]
    MonitorImpact --> ShareLearning

    RefineApproach --> PrioritizeActions

    ShareLearning --> SetupMonitoring
    RequestAnalysis --> ViewDashboard

    SetupMonitoring --> End([Continue Monitoring])

    style Start fill:#e1f5ff
    style InvestigateIssue fill:#fff4e1
    style RunEmulator fill:#f3e5f5
    style MeasureImpact fill:#e8f5e9
    style DocumentSuccess fill:#c8e6c9
    style End fill:#e1f5ff
```

**Description:**

End-to-end user journey from signal to impact:

- Detect visibility changes
- Diagnose root cause
- Prioritize actions
- Implement and validate outcomes

---

### 3.2 Automated Collection Workflow

Detailed flow of the automated data collection process.

```mermaid
flowchart TD
    ScheduleTrigger([Coordination Scheduler Scheduled Event]) --> LoadPlans[Load Active Plans]

    LoadPlans --> CheckBudget{Within Budget?}
    CheckBudget -->|No| SendBudgetAlert[Send Budget Alert]
    CheckBudget -->|Yes| GenerateTasks[Generate Collection Tasks]

    SendBudgetAlert --> PausePlan[Pause Plan]
    PausePlan --> End1([End Run])

    GenerateTasks --> EnqueueTasks[Enqueue to Task Dispatch]
    EnqueueTasks --> DequeueTask[Dequeue Task]

    DequeueTask --> ValidateTask{Task Valid?}
    ValidateTask -->|No| SendToDLQ[Send to DLQ]
    ValidateTask -->|Yes| InitCollector[Initialize Collector]

    SendToDLQ --> AlertOps[Alert Operations Team]
    AlertOps --> End2([Manual Investigation])

    InitCollector --> SelectEngine{Select Engine}

    SelectEngine -->|ChatGPT| ConfigChatGPT[Configure ChatGPT Collector]
    SelectEngine -->|Perplexity| ConfigPerplexity[Configure Perplexity Collector]
    SelectEngine -->|Gemini| ConfigGemini[Configure Gemini Collector]

    ConfigChatGPT --> LaunchBrowser[Launch Headless Browser]
    ConfigPerplexity --> LaunchBrowser
    ConfigGemini --> LaunchBrowser

    LaunchBrowser --> NavigateToEngine[Navigate to Engine URL]
    NavigateToEngine --> WaitForLoad[Wait for Page Load]

    WaitForLoad --> InjectPrompt[Inject Prompt Text]
    InjectPrompt --> SubmitQuery[Submit Query]

    SubmitQuery --> WaitForResponse[Wait for AI Response]
    WaitForResponse --> CheckTimeout{Timeout?}

    CheckTimeout -->|Yes| RetryLogic{Retry Count < Max?}
    CheckTimeout -->|No| CaptureResponse[Capture Response]

    RetryLogic -->|Yes| IncrementRetry[Increment Retry Counter]
    RetryLogic -->|No| MarkFailed[Mark Task Failed]

    IncrementRetry --> SubmitQuery
    MarkFailed --> LogFailure[Log Failure Details]
    LogFailure --> SendToDLQ

    CaptureResponse --> ExtractAnswer[Extract Answer Text]
    ExtractAnswer --> ExtractCitations[Extract Citations]
    ExtractCitations --> TakeScreenshot[Take Screenshot]

    TakeScreenshot --> ValidateData{Data Complete?}
    ValidateData -->|No| MarkPartial[Mark Partial Success]
    ValidateData -->|Yes| PrepareArtifacts[Prepare Artifacts]

    MarkPartial --> PrepareArtifacts

    PrepareArtifacts --> UploadToS3[Upload to S3]
    UploadToS3 --> CheckUpload{Upload Success?}

    CheckUpload -->|No| RetryUpload{Retry Upload?}
    CheckUpload -->|Yes| UpdateJobStatus[Update Job Status]

    RetryUpload -->|Yes| UploadToS3
    RetryUpload -->|No| MarkFailed

    UpdateJobStatus --> PublishEvent[Publish S3 Event]
    PublishEvent --> AckMessage[Acknowledge Task Dispatch Message]

    AckMessage --> CloseBrowser[Close Browser]
    CloseBrowser --> CheckMoreTasks{More Tasks in Queue?}

    CheckMoreTasks -->|Yes| DequeueTask
    CheckMoreTasks -->|No| UpdateMetrics[Update Collection Metrics]

    UpdateMetrics --> End3([Collection Complete])

    style ScheduleTrigger fill:#e1f5ff
    style CheckBudget fill:#fff9c4
    style ValidateTask fill:#fff9c4
    style CheckTimeout fill:#ffccbc
    style CheckUpload fill:#fff9c4
    style End3 fill:#c8e6c9
```

**Description:**

Automated collection pipeline at a glance:

- Schedule runs
- Collect AI answers
- Store evidence and compute scores
- Surface insights to users

---

### 3.3 Experiment Workflow

Complete workflow for running and analyzing experiments.

```mermaid
flowchart TD
    Start([User Initiates Experiment]) --> DefineGoal[Define Experiment Goal]

    DefineGoal --> SelectType{Select Experiment Type}

    SelectType -->|A/B Test| SetupAB[Setup A/B Test]
    SelectType -->|Pre/Post| SetupPrePost[Setup Pre/Post Test]

    SetupAB --> DefineVariants[Define Variant Groups:<br/>Control A and Treatment B]
    SetupPrePost --> DefineBaseline[Define Baseline Period]

    DefineVariants --> SelectPages[Select Test Pages]
    DefineBaseline --> SelectPages

    SelectPages --> SelectPrompts[Select Target Prompts]
    SelectPrompts --> SetDuration[Set Test Duration]

    SetDuration --> SetMetrics[Define Success Metrics:<br/>- Primary: Answer Share<br/>- Secondary: Citation Share, Prominence]

    SetMetrics --> CalculateSampleSize[Calculate Required Sample Size]
    CalculateSampleSize --> ReviewPlan[Review Experiment Plan]

    ReviewPlan --> ApproveTest{Approve?}
    ApproveTest -->|No| ModifyPlan[Modify Parameters]
    ApproveTest -->|Yes| StartBaseline[Start Baseline Collection]

    ModifyPlan --> ReviewPlan

    StartBaseline --> CollectBaselineData[Collect Baseline Data]
    CollectBaselineData --> CheckBaseline{Baseline Complete?}

    CheckBaseline -->|No| CollectBaselineData
    CheckBaseline -->|Yes| ImplementChanges[Implement Changes]

    ImplementChanges --> DeployVariant{Deploy Variant}

    DeployVariant -->|A/B| RandomizeTraffic[Randomize Traffic:<br/>50% A, 50% B]
    DeployVariant -->|Pre/Post| DeployToAll[Deploy to All Pages]

    RandomizeTraffic --> StartTreatment[Start Treatment Collection]
    DeployToAll --> StartTreatment

    StartTreatment --> MonitorProgress[Monitor Daily Progress]
    MonitorProgress --> CheckSampleSize{Sufficient Sample?}

    CheckSampleSize -->|No| ContinueCollection[Continue Data Collection]
    CheckSampleSize -->|Yes| CheckDuration{Min Duration Met?}

    ContinueCollection --> MonitorProgress

    CheckDuration -->|No| ContinueCollection
    CheckDuration -->|Yes| FinalizeData[Finalize Data Collection]

    FinalizeData --> CleanData[Clean & Validate Data]
    CleanData --> RunAnalysis[Run Statistical Analysis]

    RunAnalysis --> CalculateMetrics[Calculate:<br/>- Mean Difference<br/>- Confidence Interval<br/>- P-value]

    CalculateMetrics --> BayesianAnalysis[Bayesian Uplift Modeling]
    BayesianAnalysis --> CheckSignificance{Statistically Significant?}

    CheckSignificance -->|No| AnalyzeVariance[Analyze Variance:<br/>- Insufficient data<br/>- No real effect<br/>- High variance]
    CheckSignificance -->|Yes| CalculateUplift[Calculate Uplift %]

    AnalyzeVariance --> InterpretResults[Interpret Results]
    CalculateUplift --> InterpretResults

    InterpretResults --> GenerateReport[Generate Experiment Report]

    GenerateReport --> IncludeEvidence[Include Evidence:<br/>- Screenshots<br/>- Answer Comparisons<br/>- Citation Changes]

    IncludeEvidence --> PublishResults[Publish Results to Dashboard]
    PublishResults --> NotifyStakeholders[Notify Stakeholders]

    NotifyStakeholders --> ReviewByTeam{Team Review}

    ReviewByTeam -->|Success| RolloutWinner{Rollout Winner?}
    ReviewByTeam -->|Failure| DocumentLearning[Document Learning]
    ReviewByTeam -->|Inconclusive| ExtendTest[Extend Test Duration]

    ExtendTest --> MonitorProgress

    RolloutWinner -->|Yes| FullDeployment[Deploy to All Pages]
    RolloutWinner -->|No| RevertChanges[Revert Changes]

    FullDeployment --> UpdateBestPractices[Update Best Practices]
    DocumentLearning --> UpdateBestPractices
    RevertChanges --> DocumentLearning

    UpdateBestPractices --> ArchiveExperiment[Archive Experiment]
    ArchiveExperiment --> End([Experiment Complete])

    style Start fill:#e1f5ff
    style ApproveTest fill:#fff9c4
    style CheckSignificance fill:#fff9c4
    style ReviewByTeam fill:#fff9c4
    style End fill:#c8e6c9
```

**Description:**

Scientific validation flow:

- Define hypothesis and setup
- Run baseline and treatment
- Analyze results and decide rollout

---

## 4. State Diagrams

### 4.1 Collection Job State Machine

State transitions for individual collection jobs.

```mermaid
stateDiagram-v2
    [*] --> Pending: Job Created

    Pending --> Scheduled: Added to Queue
    Scheduled --> InProgress: Collector Acquired

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

    InProgress --> Paused: Manual Pause
    Scheduled --> Paused: Budget Exceeded
    Paused --> Scheduled: Resume Requested

    Failed --> Archived: After 30 Days
    Completed --> Archived: After Retention Period

    Completed --> [*]
    Failed --> [*]
    Archived --> [*]

    note right of Pending
        Initial state after
        job creation
    end note

    note right of InProgress
        Active processing
        with heartbeat
    end note

    note right of Retrying
        Exponential backoff
        retry strategy
    end note

    note right of Failed
        Manual investigation
        or DLQ processing
    end note
```

**Description:**

Job lifecycle for monitoring tasks:

- Pending -> Running -> Completed
- Retry and pause paths for failures or budget limits
- Archived when retention ends

---

### 4.2 Audit Issue Lifecycle

State machine for GEO audit issues.

```mermaid
stateDiagram-v2
    [*] --> Detected: Audit Identifies Issue

    Detected --> Triaged: Severity Assigned
    Triaged --> Open: Added to Action Center

    Open --> InProgress: Task Created
    Open --> Dismissed: Marked Not Applicable

    InProgress --> UnderReview: Fix Implemented
    UnderReview --> Verified: Improvement Confirmed
    UnderReview --> Rejected: No Improvement

    Rejected --> Open: Reassess Approach

    Verified --> Resolved: Metrics Improved

    Dismissed --> Closed: Documented
    Resolved --> Closed: Documented

    Closed --> Reopened: Issue Reoccurs
    Reopened --> Triaged

    Closed --> [*]

    note right of Detected
        Initial detection
        from crawler
    end note

    note right of Triaged
        Priority calculated:
        impact  severity
    end note

    note right of Verified
        Data-driven
        verification
    end note
```

**Description:**

Issue lifecycle from detection to closure:

- Detect -> Triage -> Work -> Verify -> Close
- Reopen if issues recur

---

### 4.3 Experiment State Machine

State transitions for experiment lifecycle.

```mermaid
stateDiagram-v2
    [*] --> Draft: User Creates Experiment

    Draft --> Configured: Parameters Set
    Configured --> Review: Ready for Approval

    Review --> Draft: Requires Changes
    Review --> Approved: Stakeholder Approval

    Approved --> RunningBaseline: Baseline Started
    RunningBaseline --> BaselineComplete: Baseline Data Sufficient

    BaselineComplete --> Deploying: Deploy Variant
    Deploying --> RunningTreatment: Variant Live

    RunningTreatment --> Paused: Manual Pause
    Paused --> RunningTreatment: Resume

    RunningTreatment --> CollectionComplete: Sample Size Met
    CollectionComplete --> Analyzing: Statistical Analysis

    Analyzing --> ResultsReady: Analysis Complete
    ResultsReady --> UnderReview: Team Review

    UnderReview --> Concluded: Decision Made

    Concluded --> Successful: Positive Lift
    Concluded --> Failed: No Improvement
    Concluded --> Inconclusive: Insufficient Evidence

    Inconclusive --> Extended: Extend Duration
    Extended --> RunningTreatment

    Successful --> Published: Rollout Winner
    Failed --> Published: Document Learning

    Published --> Archived: After 90 Days
    Archived --> [*]

    note right of Draft
        Initial planning
        and setup
    end note

    note right of RunningBaseline
        1-2 weeks of
        baseline data
    end note

    note right of Analyzing
        Bayesian analysis
        + significance tests
    end note
```

**Description:**

Experiment lifecycle from draft to archive:

- Draft -> Review -> Run -> Analyze -> Publish
- Archive for long-term learning

---

### 4.4 Alert State Machine

Lifecycle of alert notifications.

```mermaid
stateDiagram-v2
    [*] --> Monitoring: Rule Active

    Monitoring --> Triggered: Threshold Crossed
    Triggered --> Pending: Alert Created

    Pending --> Sent: Notification Dispatched
    Sent --> Delivered: Confirmed Delivery
    Sent --> Failed: Delivery Failed

    Failed --> Retrying: Retry Scheduled
    Retrying --> Sent: Reattempt
    Retrying --> Abandoned: Max Retries

    Delivered --> Acknowledged: User Viewed
    Delivered --> AutoResolved: Metric Recovered

    Acknowledged --> InProgress: User Investigating
    InProgress --> Resolved: Issue Fixed
    InProgress --> Escalated: Needs Escalation

    Escalated --> InProgress: Escalation Handled

    Resolved --> Closed: Verified
    AutoResolved --> Closed: No Action Needed
    Abandoned --> Closed: Manual Follow-up

    Closed --> [*]

    note right of Triggered
        Rule condition
        evaluated true
    end note

    note right of Delivered
        Confirmation from
        delivery channel
    end note

    note right of AutoResolved
        Metric returned to
        normal range
    end note
```

**Description:**

Alert lifecycle with recovery paths:

- Monitor -> Trigger -> Notify
- Acknowledge -> Resolve or Auto-resolve
- Retry and escalation when delivery fails

---

## Summary

These diagrams provide comprehensive visual documentation of the GEO/AI Search Monitoring Platform:

**Architecture Diagrams** show the structural organization of components, data flow patterns, and technology stack choices.

**Sequence Diagrams** illustrate detailed interactions between components over time, showing how specific workflows are executed.

**Activity Diagrams** map user journeys and business processes, showing decision points and parallel activities.

**State Diagrams** define lifecycle management for key entities, ensuring consistent state transitions and proper error handling.

Together, these diagrams serve as:
- **Communication tools** for stakeholders
- **Implementation guides** for developers
- **Training materials** for new team members
- **Documentation** for system maintenance
- **Planning artifacts** for future enhancements

---

**Last Updated:** December 26, 2025
**Maintained By:** Platform Architecture Team
