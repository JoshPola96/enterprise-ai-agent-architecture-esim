## 💎 A Note to the Community

When the initial architecture for this project was drafted in late 2025, deploying a fully autonomous, transaction-capable agent into a production environment was an outlier. The industry was largely focused on stateless chatbots and basic RAG. During that early phase, we built in public and shared our infrastructure methodologies freely to help advance the emerging agentic ecosystem. We're genuinely proud to have contributed to that momentum when it mattered.

Today, autonomous agents are the industry standard. EsimTime is now a maturing, revenue-generating platform operating at scale. Consequently, our engineering strategy has shifted from public ecosystem contribution to protecting our competitive advantage.

This document serves as a capability overview and architectural footprint for technical diligence, investors, and high-signal engineering reviews. The proprietary source code, internal algorithms, and configuration topologies remain strictly private.

---

<p align="center">
# 🌍 EsimTime — Production AI Agent Platform
</p>

<p align="center">
  <strong>Autonomous Multi-Channel eSIM Commerce via Conversational AI</strong><br>
  <em>Full-stack implementation: AI · Commerce · Infrastructure · Security · DevOps</em>
</p>

---

**TL;DR:** Designed, built, and deployed a production AI agent that handles the full eSIM customer lifecycle — discovery, payment, activation, and support — autonomously, across WhatsApp and Telegram, with sub-5 second response times and 95%+ zero-human-intervention rate. Running live in production since mid-2025.

---

## 📋 Table of Contents

- [About EsimTime](#-about-esimtime)
- [Repository Overview](#-repository-overview)
- [Platform Capabilities](#-platform-capabilities)
- [Technology Stack](#-technology-stack)
- [Infrastructure & DevOps](#-infrastructure--devops)
- [Security Architecture](#-security-architecture)
- [Performance & Scale](#-performance--scale)
- [Recognition & Validation](#-recognition--validation)
- [My Role & Contribution](#-my-role--contribution)
- [Project Evolution](#-project-evolution)
- [Let's Connect](#-lets-connect)

---

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-async-009688.svg)](https://fastapi.tiangolo.com)
[![LangGraph](https://img.shields.io/badge/LangGraph-agentic_workflows-blueviolet.svg)](https://langchain-ai.github.io/langgraph/)
[![Qdrant](https://img.shields.io/badge/Qdrant-vector_search-orange.svg)](https://qdrant.tech)
[![Docker](https://img.shields.io/badge/Docker-orchestrated-2496ED.svg)](https://docs.docker.com/compose/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-WAF%20%2B%20DDoS-F48120.svg)](https://cloudflare.com)

---

## 🏢 About EsimTime

[EsimTime](https://esimtime.com) is rethinking how travelers connect globally. Instead of downloading another app, struggling with QR codes, or waiting on hold — users open WhatsApp or Telegram, send a message, and an AI agent handles everything.

- **No app download required**
- **No signup friction — no password, no form**
- **Plan discovery, payment, and eSIM activation in a single conversation thread**
- **24/7 autonomous support in any language**

The platform is live, serving real customers across Telegram and WhatsApp, with full Stripe payment integration and autonomous eSIM provisioning.

> *Ranked **#5 AI company globally** on F6S (May 2026) — out of 2 million+ startups.*

---

## 📖 Repository Overview

**What This Is**: A technical portfolio and capabilities document for a production-grade conversational AI platform I architected, implemented, and operate as Full-Stack AI Engineer at EsimTime.

**What's Documented Here**:
- ✅ Platform capabilities and agent behaviors
- ✅ Technology stack and infrastructure overview
- ✅ Security posture and DevOps architecture
- ✅ Performance characteristics and production metrics
- ✅ Project scope and evolution

**What's Not Here**:
- ❌ Source code (proprietary to EsimTime)
- ❌ Implementation details, internal algorithms, or configuration specifics
- ❌ Business-confidential data

**Legal Status**: Shared with company authorization for portfolio and professional visibility purposes.

---

## ⚙️ Platform Capabilities

### What the Agent Does

The agent operates as a fully autonomous eSIM concierge — no human in the loop for 95%+ of interactions.

**Commerce Lifecycle**
- Understands travel needs through natural, unstructured conversation
- Recommends and compares eSIM plans across 100+ countries and regions
- Processes secure in-chat payment via Stripe (card payments and wallet balance)
- Provisions eSIMs immediately post-payment
- Delivers activation instructions and LPA codes in the same response turn — no second step required
- Free trial provisioning with anti-abuse controls and async activation handling

**Proactive Engagement**
- Initiates conversations without user prompting (payment confirmations, usage alerts, plan expiry warnings)
- Holds undeliverable notifications for new users and delivers them the moment they first message the bot
- Backend-initiated events flow through a dedicated async pipeline with guaranteed delivery semantics

**Support & Account Management**
- Guides users through device-specific eSIM installation and troubleshooting
- Manages user accounts across both messaging platforms with a unified identity layer
- Maintains full conversation context across sessions and platform switches
- Creates and tracks support escalations with complete context preservation

**Intelligence Layer**
- Auto-detects and preserves user language from the first message, across all future turns and sessions
- Adapts response format, length, and rendering to each platform's constraints
- Context-aware personalization based on conversation history and user profile
- Handles ambiguous, incomplete, and multi-intent messages gracefully

**Authentication**
- OTP-based identity verification, native to the messaging platform — no external redirect
- Smart session continuity: frequent users stay authenticated; infrequent users are prompted only when needed
- Intent preservation: original user request is queued and auto-executed after re-authentication
- Unified cross-platform identity — the same user account works across Telegram and WhatsApp

### Conversational Excellence

The agent is designed for natural, zero-friction interaction:

- Warm, human tone — like texting a knowledgeable friend, not filling a form
- Multilingual from day one — auto-detects and responds in the user's language
- Proactive, not reactive — surfaces relevant information before it's asked for
- Honest and accurate — never fabricates prices, plan specs, or availability
- Single-turn resolution wherever possible — doesn't ask unnecessary clarifying questions

---

## 🛠️ Technology Stack

### Application Layer

| Component | Technology |
|---|---|
| Web Framework | FastAPI (fully async) |
| ASGI Server | Gunicorn + Uvicorn (multi-worker) |
| Agent Orchestration | LangGraph |
| Primary LLM | Gemini Flash 2.5 |
| Embeddings | Google Generative AI |

### Data Layer

| Component | Technology |
|---|---|
| Relational Database | PostgreSQL |
| Vector Database | Qdrant |
| Cache, Coordination & Streams | Redis |
| ORM | SQLAlchemy (async) |
| Schema Migrations | Alembic |

### AI / ML Stack

| Component | Technology |
|---|---|
| Agent Framework | LangChain Core + Community + LangGraph |
| Retrieval | Hybrid RAG (multi-signal retrieval + neural reranking) |
| Neural Reranking | Cross-encoder ONNX inference |
| Security Classification | ONNX-based ML models (multi-class) |
| NER / PII Detection | ONNX-based named entity recognition |

### Infrastructure & Operations

| Component | Technology |
|---|---|
| Containerization | Docker + Compose |
| Reverse Proxy | Nginx |
| CDN / WAF | Cloudflare |
| SSL / TLS | Let's Encrypt + Cloudflare Origin CA |
| Host Firewall | UFW + nftables + fail2ban |
| CI / CD | GitHub Actions |
| Metrics & Dashboards | Prometheus + Grafana |
| Log Aggregation | Loki + Promtail |
| LLM Tracing | LangSmith |
| Messaging Channels | Telegram Bot API + Twilio (WhatsApp) |
| Payment Processing | Stripe |

---

## 🏗️ Infrastructure & DevOps

### Production Environment

The platform runs on a dedicated Linux VPS (Ubuntu 24.04 LTS):
- 8 vCPU cores, 23 GB RAM, 200 GB SSD
- Full infrastructure ownership and control over every component and layer
- All services containerized in a Docker Compose stack

### Network Topology

Traffic flows through a hardened, multi-layer stack before reaching the application:

```
Internet
  ↓
Cloudflare  (WAF · DDoS mitigation · Bot protection · CDN)
  ↓
VPS  (UFW/nftables firewall — minimal port exposure)
  ↓
Nginx  (TLS termination · rate limiting · path routing · real-IP restoration)
  ↓
Docker Bridge Network  (internal only — containers not directly reachable externally)
  ↓
Application  (FastAPI + LangGraph agent)
```

### Containerized Services

The platform runs as a fully orchestrated Docker stack:

- **App** — FastAPI application server (Gunicorn + Uvicorn, multi-worker)
- **PostgreSQL** — User accounts, sessions, conversation state, audit logs
- **Redis** — Distributed coordination, stream workers, session cache, hot data
- **Qdrant** — Vector embeddings for knowledge base and conversation memory

All containers communicate over an internal Docker bridge network. No container ports are exposed to the public internet — all external traffic enters exclusively through Nginx.

### Observability Stack

Full observability deployed in production:

- **Prometheus** — Metrics collection across app, containers, and host
- **Grafana** — Operational dashboards (agent performance, infrastructure health, business metrics)
- **Loki + Promtail** — Centralized log aggregation and querying
- **LangSmith** — LLM call tracing, token usage, agent decision replay
- **Structured JSON logging** — All application logs in parseable JSON format with rotation

### Deployment Automation

A custom management script (`manage.sh`) provides one-command operations across the full deployment lifecycle: stack startup with automated database migration, container rebuilds, backup management (PostgreSQL + Qdrant), log access, and test environment provisioning. The startup sequence includes pre-flight validation, environment auto-detection, and distributed lock management to ensure correct multi-worker initialization ordering.

**CI/CD Pipeline (GitHub Actions):**
- Automated test execution on pull requests
- Dev → Main → Production promotion workflow
- Docker layer caching for fast image rebuilds
- Zero-downtime deployment for application updates

### Backup & Recovery

- Daily automated backups (PostgreSQL + Qdrant vector data)
- Backup lifecycle managed by the deployment script (auto-installs/removes cron on stack start/stop)
- Restore tooling tested and documented

---

## 🛡️ Security Architecture

Security is implemented as a **10-layer defense-in-depth stack** — from the network edge to individual application requests. Each layer assumes the one above it can be bypassed.

**Perimeter Defense:**
- Cloudflare WAF with custom rule sets (DDoS mitigation, bot filtering, per-channel webhook IP verification)
- VPS firewall with strict default-deny policy and Docker network isolation
- Nginx per-route rate limiting with intelligent source-aware policies
- Automated IP banning (SSH + web) with Cloudflare edge propagation for web threats

**Application Defense:**
- Per-channel cryptographic webhook authenticity verification
- JWT-based session authentication on all protected endpoints
- Semantic analysis of all incoming messages before agent processing
- ML-based abuse and spam detection with cross-platform ban propagation
- PII detection and handling
- AppArmor container confinement

The security architecture has been stress-tested against real attack traffic since launch — including credential stuffing, automated scanner probes, and coordinated abuse attempts. It is continuously refined based on live production log analysis.

---

## 📊 Performance & Scale

| Metric | Value |
|---|---|
| Simple query response time | 1.5 – 3 seconds |
| Complex multi-tool response time | 3 – 5 seconds |
| Autonomous resolution rate | 95%+ (zero human intervention) |
| Supported messaging channels | Telegram + WhatsApp |
| Language support | Multilingual (auto-detected) |
| Availability | 24/7 production operations |

All figures are measured on real production traffic — not synthetic benchmarks.

---

## 🏆 Recognition & Validation

**F6S Global Ranking — May 2026**
> EsimTime ranked **#5 AI company globally** on F6S — out of 2 million+ startups worldwide. Achieved without paid promotion or advertising, driven purely by platform activity and product metrics.

**Investor Demo Day — Early 2026**
> Presented at a competitive startup showcase (FestUp). The autonomous AI platform was the core product differentiator — EsimTime finished **Top 5 out of 39 competing startups**. Architecture decisions validated from both technical and commercial standpoints by the investor panel.

**Y Combinator — 2026**
> EsimTime is actively applying to Y Combinator. The autonomous agent platform is the centerpiece of the application.

**Investment Interest**
> EsimTime received an acquisition-conditional investment offer in 2026. The offer was declined by the CEO in order to preserve the founding team and its equity position — a reflection of long-term confidence in what has been built.

**Production Track Record**
> Live and serving real paying customers across Telegram and WhatsApp since mid-2025. Growth driven through direct partnerships, event activations, and word of mouth — no paid acquisition.

---

## 👤 My Role & Contribution

**Joshua Peter Polaprayil** | Full-Stack AI Engineer

I operate across the full technical surface of the platform. There is no part of the stack I haven't designed, built, broken, and fixed.

**AI & Machine Learning**
- LangGraph agent architecture and multi-phase state machine design
- Hybrid RAG pipeline: retrieval strategy, reranking, knowledge base engineering
- Prompt engineering and behavioral calibration across hundreds of production iterations
- ONNX-based ML inference pipeline (security classification, NER, semantic analysis)
- LLM evaluation, observability, and continuous behavioral improvement

**Full-Stack Development**
- FastAPI async backend architecture and API design
- PostgreSQL schema design, Alembic migrations, connection pool management
- Redis integration: distributed locking, stream workers, session caching
- Qdrant vector database: collection management, hybrid search, data lifecycle
- Stripe payment integration (full lifecycle: initiation, confirmation, failure handling)
- Telegram Bot API + Twilio (WhatsApp Business) dual-channel integration

**DevOps & Infrastructure**
- VPS provisioning and OS hardening from scratch (Ubuntu 24.04)
- Full Docker Compose orchestration and image optimization
- Nginx configuration: reverse proxy, TLS, rate limiting, real-IP handling
- Cloudflare configuration: WAF custom rules, bot management, DNS, origin security
- CI/CD pipeline (GitHub Actions): test automation, image builds, deployment
- Full observability stack: Prometheus, Grafana, Loki, Promtail
- Automated backup systems and recovery tooling

**Security Engineering**
- 10-layer defense architecture: design, implementation, and ongoing tuning
- fail2ban configuration with Cloudflare edge-level ban propagation
- JWT authentication system design
- Webhook signature verification for both messaging platforms
- Production security incident analysis and iterative hardening
- ML-based threat classification pipeline

---

## 📅 Project Evolution

**Phase 1 — Foundation (Mid-2025)**
Core agent architecture, FastAPI backend, Telegram integration, initial RAG pipeline, identity and session management, first production deployment.

**Phase 2 — Intelligence & Hardening (Late 2025)**
WhatsApp (Twilio) integration, advanced security hardening across all layers, enhanced conversational capabilities, long-term memory systems, CI/CD automation, comprehensive integration test suites.

**Phase 3 — Commerce & Proactive Engagement (Q1 2026)**
Full in-chat Stripe checkout (card + wallet balance), free trial provisioning, automated eSIM provisioning, proactive notification pipeline with guaranteed delivery, automated backup lifecycle, investor demo day (Top 5 finish).

**Phase 4 — Scale & Observability (Q2 2026)**
Full production observability stack deployment, deep security audits and live incident response, performance optimization across the full stack, cross-platform ban propagation hardening.

### 🚀 Future Roadmap
**Phase 5 — Multimodal Integration (Target: Q3 2026)**
Expanding the agent's sensory capabilities beyond text to drastically reduce user friction. This includes native audio processing to allow users to interact via voice notes, and image parsing for automated visual troubleshooting via user-uploaded screenshots.

**Status: 🟢 Active — Live in production. Ongoing development.**

---

## 🔗 Let's Connect

**Joshua Peter Polaprayil**
Full-Stack AI Engineer · EsimTime · MSc Big Data & AI (ATU Ireland)

- **LinkedIn:** [linkedin.com/in/josh33-peter10](https://www.linkedin.com/in/josh33-peter10/)
- **Email:** [josh19peter96@gmail.com](mailto:josh19peter96@gmail.com)
- **GitHub:** [github.com/JoshPola96](https://github.com/JoshPola96)
- **EsimTime:** [esimtime.com](https://esimtime.com)
- **Try the agent on WhatsApp:** [wa.me/13029833081](https://wa.me/13029833081?text=Hi)
- **Try the agent on Telegram:** [t.me/esimtimeaibot](https://telegram.me/esimtimeaibot)

---

## 🙏 Acknowledgments

**Herman Polat** (CEO, EsimTime) — for the original vision, entrepreneurial conviction, and the trust to build something genuinely new in a space that hadn't proven itself yet.

**Opeoluwa Adeyeri** — backend engineering excellence, steady through every refactor.

**Zain Ali** — frontend experience that makes the platform accessible and credible.

**The Open-Source Community** — LangChain, Qdrant, FastAPI, Redis, Docker, and many others. The ecosystem made this possible; we're glad to have pushed it in a new direction.

---

## 📄 License & Repository Note

**MIT License** — see [LICENSE](LICENSE) for details.

This repository contains **architectural documentation and capability descriptions only**. The production codebase, business logic, configuration, and proprietary implementation details are confidential to EsimTime and are not included here.

This documentation is shared for portfolio visibility and professional credibility purposes, with company authorization.

---

**Last Updated:** June 2026
**Project Status:** 🟢 Active — Production (Mid-2025 – Present)

---

<p align="center">
  <em>Built with precision. Deployed with confidence. Growing in production.</em>
</p>
