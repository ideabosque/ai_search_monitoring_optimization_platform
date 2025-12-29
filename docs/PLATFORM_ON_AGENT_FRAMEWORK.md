# Building the GEO/AI Platform on Agent Foundation Modules

**Created:** December 26, 2025  
**Purpose:** Guide for how the platform uses the AI Agent Framework, organized by the three business pillars.

> **Note:** This is the **primary documentation** for the AI Agent Framework. It aligns with:
> - [ARCHITECTURE_DIAGRAMS.md](ARCHITECTURE_DIAGRAMS.md) sections 1 and 2
> - [ER_DIAGRAMS.md](ER_DIAGRAMS.md) section 6

---

## Three Pillars (Platform Framing)

```
P1[Pillar 1: Planner + Collectors
Observe]
P2[Pillar 2: GEO Audit Crawler
Diagnose]
P3[Pillar 3: LLM Emulator + Experiment Lab
Predict & Prove]
```

The platform is organized around these three pillars. Each pillar is implemented as a **thin application layer** on top of the two foundation modules:
- `https://github.com/ideabosque/ai_agent_core_engine`
- `https://github.com/ideabosque/ai_coordination_engine`

---

## Shared Foundation Modules (Used by All Pillars)

### AI Agent Core Engine (Execution)
- Executes agent configs (YAML)
- Hosts tool registry and memory management
- Integrates LLM providers
- Never contains business logic

### AI Coordination Engine (Orchestration)
- Defines workflows (DAGs)
- Dispatches tasks to agents
- Manages workflow state and retries
- Synthesizes results

**Key principle:** Foundation modules are **frozen** and reusable. All business logic lives in configs, tools, and thin services.

---

## Pillar 1: Planner + Collectors (Observe)

**Business goal:** Measure how AI engines describe and cite the brand.

**Uses:**
- Agent Core for analysis tasks (e.g., scoring, evidence synthesis)
- Coordination Engine for collection workflows

**Typical workflow:**
1. Plan created (topics, prompts, cadence)
2. Collectors gather AI answers + evidence
3. Parser normalizes responses
4. Brand resolution and scoring
5. Insights published to dashboard

**Agent-driven outputs:**
- Answer share and citation share summaries
- Evidence packages for each prompt

---

## Pillar 2: GEO Audit Crawler (Diagnose)

**Business goal:** Explain why visibility is low and what to fix.

**Uses:**
- Coordination Engine to orchestrate multi-agent audit workflows
- Agent Core to run diagnostic agents and generate fix guidance

**Typical workflow:**
1. Crawl site and collect issues
2. Agents cluster issues and assess impact
3. Competitive analysis is generated
4. Recommendations and fix recipes are produced
5. Action Center surfaces priorities

**Agent-driven outputs:**
- Root cause analysis
- Ranked recommendations
- Fix guidance and templates

---

## Pillar 3: LLM Emulator + Experiment Lab (Predict & Prove)

**Business goal:** Predict outcomes before publishing and validate impact after changes.

**Uses:**
- Agent Core for evaluation and judgment
- Coordination Engine to run comparison workflows

**Typical workflow:**
1. Submit content variants and prompts
2. Emulator simulates outcomes
3. Judge model compares quality
4. Recommendation is produced
5. Experiment results validate changes

**Agent-driven outputs:**
- Publish vs. revise recommendation
- Experiment verdicts and uplift summaries

---

## Configuration-Driven Intelligence

All platform intelligence is expressed in configs, not hardcoded logic:

- **Agent configs**: `config/agents/*.yaml`
- **Workflow configs**: `config/workflows/*.yaml`
- **Tools**: `tools/*.py` (extension only)

This allows fast iteration without redeploying core services.

---

## Integration with Data and Evidence

- **Neo4j GraphRAG** is the primary retrieval layer for graph + vector queries.
- **DynamoDB** stores operational metadata and transaction records.
- **S3** stores raw artifacts and evidence snapshots.
- **AI Coordination Engine** manages the processing pipeline.

---

## Operating Principles

1. **Thin services**: Services delegate reasoning to agents
2. **Evidence-first**: Every recommendation is traceable
3. **Workflow clarity**: Orchestrate, don?t hardcode
4. **Reusable foundation**: Core engines never modified

---

## References

- AI Agent Core Engine: https://github.com/ideabosque/ai_agent_core_engine
- AI Coordination Engine: https://github.com/ideabosque/ai_coordination_engine
- Architecture diagrams: [ARCHITECTURE_DIAGRAMS.md](ARCHITECTURE_DIAGRAMS.md)
- ER diagrams: [ER_DIAGRAMS.md](ER_DIAGRAMS.md)
