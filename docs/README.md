# GEO/AI Search Monitoring Platform - Documentation Index

**Last Updated:** December 26, 2025

---

## Overview

This directory contains the core documentation for the GEO/AI Search Monitoring & Optimization Platform. The platform is framed around three business pillars:

1. **Pillar 1: Planner + Collectors (Observe)**
2. **Pillar 2: GEO Audit Crawler (Diagnose)**
3. **Pillar 3: LLM Emulator + Experiment Lab (Predict & Prove)**

---

## Start Here

### 1. [HIGH_LEVEL_DEVELOPMENT_PLAN.md](HIGH_LEVEL_DEVELOPMENT_PLAN.md)
**What:** Business plan and development roadmap
**For:** Product managers, executives, stakeholders
**Contents:**
- Vision and goals
- Three-pillar structure
- Phases, timelines, success metrics
- Updated tech stack alignment

---

## Core Technical Docs

### 2. [PLATFORM_ON_AGENT_FRAMEWORK.md](PLATFORM_ON_AGENT_FRAMEWORK.md) ? Primary Agent Docs
**What:** AI Agent Framework guide
**For:** Engineers building the platform
**Contents:**
- Foundation modules (ai_agent_core_engine + ai_coordination_engine)
- Three-pillar integration
- Configuration-driven approach
- Platform integration patterns

### 3. [ARCHITECTURE_DIAGRAMS.md](ARCHITECTURE_DIAGRAMS.md)
**What:** Visual architecture diagrams
**For:** Engineers, architects
**Contents:**
- High-level business architecture
- Three-pillar system view
- Data architecture and flow (GraphRAG-first)
- Deployment topology

### 4. [ER_DIAGRAMS.md](ER_DIAGRAMS.md)
**What:** Data models and schema references
**For:** Database engineers, backend developers
**Contents:**
- Core entity relationships
- Monitoring, audit, and experiment entities
- GraphRAG-aligned data relationships

---

## How to Use This Documentation

**New to the project?**
1. Read [HIGH_LEVEL_DEVELOPMENT_PLAN.md](HIGH_LEVEL_DEVELOPMENT_PLAN.md)
2. Read [PLATFORM_ON_AGENT_FRAMEWORK.md](PLATFORM_ON_AGENT_FRAMEWORK.md)
3. Review [ARCHITECTURE_DIAGRAMS.md](ARCHITECTURE_DIAGRAMS.md)

**Designing data models?**
- Use [ER_DIAGRAMS.md](ER_DIAGRAMS.md)

**Working on agent logic?**
- Use [PLATFORM_ON_AGENT_FRAMEWORK.md](PLATFORM_ON_AGENT_FRAMEWORK.md)

---

## Key Architectural Principles

1. **Foundation modules are frozen**
   - AI Agent Core Engine and AI Coordination Engine are reusable and never modified
2. **Configuration over code**
   - Agents and workflows are YAML configs, not hardcoded logic
3. **Thin application layer**
   - Services delegate intelligence to the foundation modules
4. **GraphRAG-first retrieval**
   - Neo4j GraphRAG is the primary retrieval layer for evidence and relationships

---

## File Organization

```
docs/
  README.md                           # This file
  HIGH_LEVEL_DEVELOPMENT_PLAN.md      # Business plan & roadmap
  PLATFORM_ON_AGENT_FRAMEWORK.md      # Agent framework guide
  ARCHITECTURE_DIAGRAMS.md            # System architecture diagrams
  ER_DIAGRAMS.md                      # Data models and schema
```

---

## Quick Links

| I want to... | Go to... |
|--------------|----------|
| Understand the business vision | [HIGH_LEVEL_DEVELOPMENT_PLAN.md](HIGH_LEVEL_DEVELOPMENT_PLAN.md) |
| Learn about AI agents | [PLATFORM_ON_AGENT_FRAMEWORK.md](PLATFORM_ON_AGENT_FRAMEWORK.md) |
| See architecture diagrams | [ARCHITECTURE_DIAGRAMS.md](ARCHITECTURE_DIAGRAMS.md) |
| Design database schema | [ER_DIAGRAMS.md](ER_DIAGRAMS.md) |

---

**Questions?** Start with the roadmap and follow the links. Keep docs consistent with the three-pillar framing and foundation modules.
