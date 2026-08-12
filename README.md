# Building a Transaction-Capable AI Agent Before There Was a Playbook

### Engineering field notes from twelve months of production agentic AI — CPU-only, under $45/month

<p align="center">
  <strong>Joshua Peter Polaprayil</strong> · Full-Stack AI Engineer<br>
  <em>AI · Commerce · Retrieval · Security · Infrastructure</em>
</p>

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-async-009688.svg)](https://fastapi.tiangolo.com)
[![LangGraph](https://img.shields.io/badge/LangGraph-agentic_FSM-blueviolet.svg)](https://langchain-ai.github.io/langgraph/)
[![Qdrant](https://img.shields.io/badge/Qdrant-native_hybrid-orange.svg)](https://qdrant.tech)
[![ONNX](https://img.shields.io/badge/ONNX-INT8_CPU-005CED.svg)](https://onnxruntime.ai)
[![Docker](https://img.shields.io/badge/Docker-orchestrated-2496ED.svg)](https://docs.docker.com/compose/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-WAF%20%2B%20Turnstile-F48120.svg)](https://cloudflare.com)

---

## A Note Before You Read

When I started this work in July 2025, transaction-capable autonomous agents — agents that hold state, manage identity, and complete a purchase end to end — were not a commercial category. They were whitepapers and roadmap slides from the largest labs in the world.

The timeline bears that out. OpenAI's in-chat checkout went live in September 2025; by then this agent was already provisioning eSIMs and taking payments from real customers. Google's agentic commerce protocol wasn't published until January 2026.

That gap is why this document exists. The core problems here — exactly-once proactive delivery, cross-platform identity resolution, intent preservation across authentication boundaries, prompt-injection defence inside the request path — had to be solved from first principles, because there was no reference implementation to borrow from. Some of it would be built differently today with tooling that now exists. That's the point. These are field notes from before the playbook.

**What this is:** an architectural and engineering case study of a production system I designed, built, and operate — written as a technical narrative. What I tried, what broke, what I measured, what I chose instead.

**What this is not:** an implementation guide. No source code, business logic, schemas, credentials, or configuration topologies appear here. Thresholds and tuning constants are described by intent rather than value. The production codebase is proprietary.

---

## TL;DR

A production AI agent handling the complete eSIM customer lifecycle — discovery, authentication, payment, provisioning, activation, troubleshooting — autonomously, inside WhatsApp and Telegram. No app, no forms, no passwords.

| | |
|---|---|
| **Response time** | **P50 9.3s** · P90 20.7s in production, dominated by LLM output tokens — see [Performance](docs/06-performance.md) |
| **Voice round-trip** | **P50 7.7s**, down from 24–28s after a measurement-driven optimisation round |
| **Tool execution accuracy** | >99% across 26 tools |
| **Autonomous resolution** | 95%+, zero human intervention |
| **Under adversarial load test** | 19,164 malicious payloads absorbed at 319 RPS while legitimate traffic held **100% success** (bench, pre-multimodal) |
| **Webhook ingest** | median 5ms · p95 9ms · p99 14ms · 100% delivery (bench) |
| **Localisation** | 10 languages for system messaging, incl. RTL |
| **Hardware** | CPU-only commodity VPS — no GPU, anywhere |
| **Total running cost** | Under **$45/month** — versus $300–500 managed-cloud equivalent |
| **Recognition** | **Top-5 AI company on [F6S](https://www.f6s.com/companies/artificial-intelligence/united-states/wyoming/sheridan/co)** (peaked #5, May 2026) · **Top 5 of 39** at investor showcase |

Five ML models — reranking, prompt-injection classification, PII detection, risk scoring, speech — all quantized INT8, all on CPU, several inside the request path.

---

## Evolution at a Glance

| | Phase 1 (research) | Phase 2 (production) | Phase 3+ (current) |
|---|---|---|---|
| **Architecture** | Three coordinating agents | Single agent + tools | Single agent + commerce + proactive events |
| **LLM** | LLaMA 3.1 8B, local | Gemini Flash | Gemini Flash — successor under evaluation |
| **Context** | 128k | 1M | 1M |
| **Vector store** | In-memory | Qdrant, persistent | Qdrant, native hybrid |
| **Auth** | Basic sessions | OTP + identity linking | OTP + billing identity |
| **Memory** | Conversation only | Episodic + semantic | Episodic + semantic |
| **Commerce** | — | — | Checkout, balance, trials |
| **Notifications** | — | — | Exactly-once, DLQ, pending store |
| **Modality** | Text | Text | Text, voice, image |
| **Security** | — | Rate limits, encryption | Ten-layer, ML-classified, load-tested |
| **Tools** | ~5 | 15+ | 26 |
| **Response time** | 8–12s | 3–5s | P50 9.3s (measured on prod, multimodal stack) |

---

## The Case Study

Roughly 1,300 lines of engineering narrative, split by theme. Start anywhere — each part stands on its own.

| | Part | What's in it |
|---|---|---|
| **01** | [**Timeline — Seven Phases**](docs/01-timeline.md) | The origin and brief, the constraint that shaped every later decision, and the seven phases from multi-agent research prototype to transaction-capable production agent. |
| **02** | [**Platform Constraints**](docs/02-platform.md) | Exactly-once message delivery, ten-language localisation, the constraints you cannot engineer around, and what it takes to run five ML models under a fixed RAM ceiling. |
| **03** | [**Models, Temperature & Attention**](docs/03-models.md) | How the LLM was chosen and re-chosen, how far model selection actually got, and the wrong-product-ID failure that traced back to sampling temperature. |
| **04** | [**Evaluation, Testing & Delivery**](docs/04-engineering.md) | MLOps tooling, the testing strategy, the incident that redefined what "tested" meant, and the deployment pipeline. |
| **05** | [**System Architecture**](docs/05-architecture.md) | The agent loop itself, the system prompt and its measured compression, hybrid retrieval, the data layer, and the full technology stack. |
| **06** | [**Performance & Economics**](docs/06-performance.md) | Measured latency across the whole request path, and the cost breakdown behind running all of it for under $45 a month. |
| **07** | [**Limitations, Governance & Lessons**](docs/07-reflections.md) | What the system still cannot do, how it's governed, references and influences, and what I'd tell someone starting this today. |

---

## Recognition

**F6S Ranking — May 2026**
Peaked at **#5 AI company** in its F6S category, currently holding **top 6** — [live listing](https://www.f6s.com/companies/artificial-intelligence/united-states/wyoming/sheridan/co). Achieved with no paid promotion, driven by platform activity, product metrics, and public technical visibility. F6S reshared the announcement to their own audience.

**Investor Showcase — Early 2026**
Presented at a competitive startup demo day; the autonomous agent was the core product differentiator and the company placed **Top 5 of 39**. Architecture decisions were examined and validated by the investor panel on both technical and commercial grounds.

**Production Track Record**
Live and serving real paying customers across two messaging channels since mid-2025, with growth from partnerships, event activation, and word of mouth rather than paid acquisition.

---

## My Role

I own the full technical surface of this platform. There is no layer of it I haven't designed, built, broken, and repaired.

**AI & Machine Learning** — LangGraph agent architecture and state machine design; hybrid retrieval pipeline including fusion strategy, reranking, chunking, and knowledge base engineering; system prompt architecture and measured compression across hundreds of production iterations; ONNX inference pipelines for reranking, injection classification, NER, and risk scoring; self-hosted speech pipeline with benchmark-driven model selection; LLM-as-judge evaluation harness for both testing and production quality review.

**Full-Stack Engineering** — FastAPI async backend and API design; PostgreSQL schema design with time-ordered keys, migrations, connection pooling; Redis distributed locking, stream consumer groups, sliding-window rate limiting, Bloom filters; Qdrant collection management, hybrid search, multi-worker indexing coordination; Stripe integration across the full lifecycle including failure handling; dual-channel Telegram and WhatsApp integration with platform-aware delivery and ten-locale internationalisation.

**Infrastructure & DevOps** — VPS provisioning and OS hardening from bare metal; Docker Compose orchestration, image optimisation, build-time model provisioning; resource modelling from direct measurement; Nginx reverse proxy, TLS, rate limiting, security headers; Cloudflare WAF, Turnstile, DNS, origin security; GitHub Actions CI/CD with artifact-consistent testing, registry publishing, and hash-verified deployment; automated backup and restore tooling; load and adversarial testing.

**Security Engineering** — layered defence from edge to request path; behavioural abuse detection with cross-platform ban propagation; semantic injection defence validated under adversarial load; adversarial instrumentation including honeypots and timing analysis; validation-as-abuse-signal design; dual-mode encryption with searchable lookup hashing; PII redaction pipeline; production incident analysis and iterative hardening; administrative tooling for non-technical operators.

---

## Acknowledgments

**Herman Polat** (CEO, EsimTime) — for the original vision, entrepreneurial conviction, and the trust to build something genuinely new in a category that hadn't proven itself yet.

**Opeoluwa Adeyeri** — backend engineering, steady through every refactor.

**Zain Ali** — frontend experience that makes the platform accessible and credible.

**The open-source ecosystem** — for the tools listed throughout, and many more besides.

---

## Contact

**Joshua Peter Polaprayil**
Full-Stack AI Engineer · MSc Big Data Analytics & AI (ATU, Ireland)

- **LinkedIn** — [linkedin.com/in/josh33-peter10](https://www.linkedin.com/in/josh33-peter10/)
- **GitHub** — [github.com/JoshPola96](https://github.com/JoshPola96)
- **Email** — [josh19peter96@gmail.com](mailto:josh19peter96@gmail.com)

Open to AI/ML and full-stack AI engineering roles. If you're building production agentic systems and want someone who can own them from architecture through deployment, I'd like to hear from you.

---

## Repository Note

This repository contains **architectural documentation and engineering narrative only**. Production source code, business logic, internal algorithms, configuration topologies, and tuning constants are confidential and not included. Thresholds and parameters are described by intent rather than value throughout.

Shared for portfolio and professional visibility purposes, with authorization.

**MIT License** — see [LICENSE](LICENSE).

---

**Last updated:** August 2026
**Status:** 🟢 Live in production

<p align="center">
  <em>Built under constraint. Hardened under fire. Measured, not assumed.</em>
</p>
