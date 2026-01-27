# 🌐 Enterprise AI Agent Platform: Production Architecture

<p align="center">
  <strong>Multi-Channel Autonomous Agent for Global eSIM Commerce</strong><br>
  <em>Full-stack implementation: AI • DevOps • Infrastructure • Security</em>
</p>

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.119.0-009688.svg)](https://fastapi.tiangolo.com)
[![LangGraph](https://img.shields.io/badge/LangGraph-0.6.10-blueviolet.svg)](https://langchain-ai.github.io/langgraph/)
[![Qdrant](https://img.shields.io/badge/Qdrant-1.12.2-orange.svg)](https://qdrant.tech)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED.svg)](https://docs.docker.com/compose/)

---

## 📋 Repository Overview

**What This Is**: A comprehensive technical portfolio piece documenting a production-grade conversational AI platform I architected, implemented, and deployed for the global eSIM industry.

**Project Scope** (July - December 2024):
- ✅ **AI Architecture**: Agentic workflows, hybrid RAG, multi-channel authentication
- ✅ **Full-Stack Development**: FastAPI backend, PostgreSQL, Redis, Qdrant vector DB
- ✅ **DevOps & Infrastructure**: Docker orchestration, Nginx reverse proxy, SSL/TLS
- ✅ **Security Hardening**: Firewall rules, fail2ban, rate limiting, input validation
- ✅ **CI/CD Pipeline**: GitHub Actions with dev→main→prod workflow, automated testing
- ✅ **Database Engineering**: Schema design, Alembic migrations, connection pooling
- ✅ **Production Operations**: Server provisioning, DNS configuration, monitoring, backups

**What's Included Here**:
- ✅ System architecture and design decisions
- ✅ Technical implementation patterns (publicly available tools)
- ✅ DevOps workflows and infrastructure setup
- ✅ Performance optimization strategies
- ✅ Production deployment procedures

**What's Excluded**:
- ❌ Source code (proprietary to client)
- ❌ Business-confidential metrics or strategies
- ❌ API keys, credentials, or secrets

**Legal Status**: Shared with explicit client permission for portfolio and educational purposes.

---

## 🎯 Project Genesis & Partnership

**Business Vision**: Herman Güler (CEO, [EsimTime](https://esimtime.com)) identified an opportunity to revolutionize eSIM customer service using autonomous AI agents—eliminating 24/7 human support costs while improving response quality and speed.

**Technical Execution**: I was engaged as AI Engineer & DevOps Architect to transform this vision into a production-ready system capable of handling the complete customer lifecycle across multiple messaging platforms.

**Collaboration Model**:
- **Herman's Domain**: Business requirements, product vision, domain expertise, market strategy
- **My Domain**: Technology stack selection, system architecture, implementation, deployment, operations
- **Shared Success**: A system that autonomously handles thousands of customer interactions with sub-5s response times

**Result**: A production platform that replaced the need for human support agents while maintaining high customer satisfaction—demonstrating the commercial viability of agentic AI in customer service.

---

## 🎯 Project Context & Collaboration

### The Vision (Herman Güler, EsimTime CEO)

**Herman Güler**, founder and CEO of **[EsimTime](https://esimtime.com)**, envisioned an autonomous AI agent that could handle the complete customer journey - from discovering travel data plans to purchase to troubleshooting - across multiple messaging platforms, 24/7, without human support staff.

This was **Herman's business innovation** - recognizing that AI could transform customer service in the eSIM industry while dramatically reducing operational costs.

### My Role (AI Engineer & Technical Architect)

I was brought on to transform Herman's vision into technical reality. My responsibilities included:

- **Technology Research**: Evaluating LLM providers, vector databases, and agent frameworks
- **Architecture Design**: Designing the multi-phase system evolution from research to production
- **Implementation**: Building the conversational agent, memory systems, and integrations
- **Production Hardening**: Implementing authentication, error handling, and deployment automation
- **Testing & Validation**: Creating comprehensive test suites and evaluation frameworks

### A True Partnership

This project exemplifies successful collaboration:
- **Herman's Contribution**: Business vision, product requirements, domain expertise, and entrepreneurial leadership
- **My Contribution**: Technical architecture, implementation skills, technology selection, and engineering execution
- **EsimTime Team**: Backend API support and integration assistance

**Credit**: The **idea** and **business vision** belong to Herman Güler and EsimTime. The **technical architecture** and **implementation** represent my engineering work. This case study showcases the **learnings and patterns** I developed during this collaboration.

---

## 🚀 Technical Highlights & Innovations

### System Capabilities

**24/7 Autonomous Operations**
- Handles complete customer journey: discovery → purchase → troubleshooting
- Multi-channel support: Telegram, WhatsApp, web-ready architecture
- Zero human intervention required for 95%+ of interactions
- Intelligent escalation for edge cases

**Performance**
- Sub-5 second response times for complex multi-tool queries
- 1.5-3 second response for simple lookups
- >99% tool execution accuracy
- Handles 125-250 concurrent users (5-worker deployment)

**Reliability**
- Circuit breakers on all external dependencies
- Graceful degradation (continues with cached data on failures)
- Multi-layer error recovery and retry logic
- Health monitoring with degraded status detection

### AI Architecture Innovations

#### 1. **Hybrid Memory System** (Inspired by MemoAI Research)

Implemented multi-tier memory architecture balancing speed, persistence, and semantic understanding:

**Short-Term Context** (In-memory cache)
- Recent conversation turns for fast access
- Automatic expiration policies
- Sub-5ms retrieval latency

**Long-Term Episodic Memory** (Vector storage)
- User-specific interaction history
- Purchase patterns and preferences
- Troubleshooting context from past sessions
- Semantic retrieval for contextual recall

**Knowledge Base** (Hybrid search)
- Installation guides for 50+ device types
- Troubleshooting workflows and diagnostics
- Policy documents and coverage information
- Operator details per country

**Retrieval Strategy**:
- Combines keyword-based search with semantic vector embeddings
- Rank fusion algorithms to merge multiple result sets
- Neural reranking for precision optimization
- Dynamic result set sizing based on query type

**Impact**: 15-25% improvement in retrieval relevance compared to single-method approaches

#### 2. **Architectural Evolution: Research to Production**

| Phase | Timeline | Key Characteristics |
| :--- | :--- | :--- |
| **Phase 1: Multi-Agent Exploration** | Jul-Sep 2024 | • Experimented with specialized agent coordination<br>• Validated agentic workflows for complex conversations<br>• **Bottleneck:** High latency in agent handoffs<br>• **Challenge:** Context window management |
| **Phase 2: Simplified Production** | Oct-Dec 2024 | • Consolidated to **Single-Agent** with specialized capabilities<br>• Migrated to **Gemini Flash 2.0** (1M tokens)<br>• Implemented persistent **Qdrant** storage<br>• **Result:** Achieved 60% latency reduction |

**Lesson**: Architectural simplicity often outperforms complexity in production environments

#### 3. **Cross-Platform Identity & Authentication**

**Unified Identity Resolution**:
- Links user accounts across messaging platforms
- Passwordless authentication flows
- Secure token management with revocation capabilities

**Seamless Session Management**:
- Detects authentication state changes automatically
- Preserves user intent across login flows
- Recovers gracefully from session expiry
- Provides context-aware re-authentication prompts

**User Experience**:
- No awkward "What would you like to do next?" after login
- Automatic execution of pending requests post-authentication
- Continuity across platform switches (Telegram ↔ WhatsApp)

#### 4. **Accuracy Assurance Mechanisms**

**Multi-Layer Validation**:
- Request validation before execution
- Type checking and schema enforcement
- Budget limits to prevent runaway loops
- Duplicate request detection
- Injection attack prevention

**Priority Execution Ordering**:
- Authentication operations execute before data access
- Token injection for dependent operations
- Single-turn completion for related tasks
- Optimizes for user experience over strict sequencing

**Result**: >99% execution accuracy in production testing

#### 5. **Production Reliability Engineering**

**Circuit Breaker Implementation**:
- Monitors health of all external dependencies
- Automatic failover to cached/degraded modes
- Periodic recovery testing
- Prevents cascade failures

**Graceful Degradation Philosophy**:
- Distinguishes critical vs. nice-to-have features
- Continues operation with reduced capabilities
- User-friendly error communication (never technical jargon)
- Automatic escalation when appropriate

**Multi-Worker Coordination**:
- Distributed locking for shared operations
- Leader election for singleton tasks
- State synchronization across workers
- Horizontal scaling readiness

---

## 🤖 Agent Capabilities & Persona

### Conversational Concierge

The agent operates as an **expert travel connectivity consultant**—combining product knowledge, technical troubleshooting expertise, and customer service excellence into a single autonomous interface.

**Core Roles**:

**1. Product Consultant**
- Understands travel patterns and connectivity needs through natural conversation
- Recommends optimal plans based on destination, duration, and usage patterns
- Compares options across countries and regions to find best value
- Explains technical specifications (coverage, speeds, compatibility) in accessible language

**2. Technical Troubleshooter**
- Guides users through device-specific installation processes
- Diagnoses connectivity issues with contextual questioning
- Provides step-by-step remediation tailored to device type and OS version
- Knows when to escalate vs. when to continue guided support

**3. Account Manager**
- Handles authentication seamlessly across messaging platforms
- Provides account information and usage summaries
- Manages promotional offers and discount validation
- Creates support tickets with proper context preservation

### Intelligent Behaviors

**Multilingual Intelligence**
- Auto-detects user language from first message
- Maintains language consistency across conversation turns
- Persists language preference across sessions and platforms
- Handles code-switching naturally (e.g., English/Spanish mix)

**Cross-Platform Optimized**
- Formats responses appropriately for Telegram vs. WhatsApp
- Uses platform-appropriate emoji and markdown syntax
- Adapts message length to platform constraints
- Maintains conversation continuity across platform switches

**Context-Aware Personalization**
- Remembers user preferences and past interactions
- Adapts tone based on conversation stage (exploration vs. troubleshooting)
- Recognizes returning users and adjusts onboarding
- Balances friendliness with efficiency based on user urgency

**Proactive Guidance**
- Asks clarifying questions before making recommendations
- Suggests complementary products or better-value alternatives
- Anticipates common follow-up questions
- Provides unsolicited but relevant information (e.g., "This plan includes 50 countries")

### Conversational Excellence

**Natural Dialogue Patterns**:
- Warm, friendly tone (like chatting with a knowledgeable friend)
- Liberal emoji use for visual engagement and clarity
- Proper markdown formatting (bold for emphasis, lists for comparisons)
- Varied sentence structure (avoids robotic repetition)

**Error Recovery**:
- Gracefully handles ambiguous requests ("Did you mean X or Y?")
- Admits uncertainty when information is incomplete
- Never fabricates prices, specifications, or availability
- Falls back to human escalation when confidence is low

**Efficiency Optimization**:
- Single-turn resolutions when possible (doesn't ask unnecessary questions)
- Multi-tool coordination (fetches profile + usage in one response)
- Smart caching (doesn't re-fetch recently accessed data)
- Progressive disclosure (detailed specs only when requested)

---

## 🏗️ System Architecture

### High-Level Component Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                      USER INTERFACES                        │
│  Telegram Bot API  │  WhatsApp (Twilio)  │  Future: Web     │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│              FASTAPI APPLICATION (Port 8000)                │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  Security Middleware                                  │  │
│  │  • Rate Limiting (SlowAPI): 30 req/min per IP         │  │
│  │  • CORS Validation: Whitelist origins only            │  │
│  │  • Trusted Host Middleware: Prevent host poisoning    │  │
│  │  • HSTS Headers: Force HTTPS in production            │  │
│  └───────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  Session & Conversation Management                    │  │
│  │  • SessionManager: Identity linking, OTP flow         │  │
│  │  • ConversationManager: Working + episodic memory     │  │
│  │  • Intent Preservation: 401 recovery, pending tasks   │  │
│  └───────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────┐  │
│  │      AGENTIC ORCHESTRATION (LangGraph FSM)            │  │
│  │                                                       │  │
│  │  [Agent Node] → [Router] → [Tool Executor]            │  │
│  │       ↓            ↓             ↓                    │  │
│  │  [Phantom Login Handler] → [Synthesizer]              │  │
│  │                                                       │  │
│  │  • LLM: Gemini Flash 2.0 (1M token context)           │  │
│  │  • Specialized capabilities via tool system           │  │
│  │  • Budget limits prevent runaway operations           │  │
│  │  • Multi-layer validation for accuracy                │  │
│  └───────────────────────────────────────────────────────┘  │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│           PERSISTENCE & EXTERNAL SERVICES                   │
│                                                             │
│  PostgreSQL 16        Redis 7           Qdrant 1.12         │
│  • Users & Sessions   • Cache (2-5ms)   • KB Vectors        │
│  • Conversation Logs  • Distributed     • Conversation      │
│  • Audit Trail        •   Locks         •   Memories        │
│  • LangGraph States   • Rate Limits     • Hybrid Search     │
│  • Alembic Migrations • BM25 State      •   (BM25+Vector)   │
│                                                             │
│  External APIs                                              │
│  • EsimTime REST API (Products, Orders, Auth, Support)      │
│  • Twilio WhatsApp Business API                             │
│  • Telegram Bot API (Webhook mode)                          │
└─────────────────────────────────────────────────────────────┘
```

### Agent State Machine (LangGraph FSM)

```
USER MESSAGE
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│                      [AGENT NODE]                           │
│                                                             │
│  1. Fetch LIVE session data (auth_status from Redis)        │
│  2. Load conversation context:                              │
│     • Working memory (recent 10-50 turns)                   │
│     • Episodic memory (relevant past interactions)          │
│     • Pending intents (blocked requests awaiting auth)      │
│  3. Inject system hints:                                    │
│     • Auth state changes (login detection)                  │
│     • Timeout risk warnings (if response time high)         │
│  4. LLM inference (Gemini Flash 2.0):                       │
│     • 1M token context window                               │
│     • Comprehensive system instructions                     │
│     • Tool calling with structured outputs                  │
│  5. Generate: Tool calls OR final response wrapper          │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│                 [ROUTER: Should Continue?]                  │
│                                                             │
│  Decision Logic:                                            │
│  ├─ Has actionable tool calls? ────────────> [TOOL NODE]    │
│  ├─ Only final response wrapper? ──────────> [END]          │
│  └─ Empty output? ─────────────────────────> [AGENT NODE]   │
│      (Retry with system hint to force response)             │
└─────────────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│              [TOOL NODE - Auth-Aware Wrapper]               │
│                                                             │
│  PHASE 1: Anti-Hallucination Filter                         │
│  ├─ Whitelist validation (reject non-existent tools)        │
│  ├─ Schema validation (type-check arguments)                │
│  ├─ Filter "final_response" (not executable)                │
│  └─ Malformed call detection                                │
│                                                             │
│  PHASE 2: Priority Authentication                           │
│  ├─ Detect: complete_login or logout in tool calls          │
│  ├─ If login + multiple tools:                              │
│  │   ├─ Execute login FIRST                                 │
│  │   ├─ Check if successful (fetch fresh session)           │
│  │   ├─ Inject NEW access_token into remaining tools        │
│  │   └─ Execute other tools in SAME turn                    │
│  └─ On logout: Execute immediately, block other tools       │
│                                                             │
│  PHASE 3: Standard Tool Execution                           │
│  ├─ Fetch LIVE session (ignore cache)                       │
│  ├─ For each tool:                                          │
│  │   ├─ Requires auth + NOT logged in?                      │
│  │   │   ├─ Block execution                                 │
│  │   │   ├─ Store as "pending intent"                       │
│  │   │   └─ Return 401-style error                          │
│  │   └─ Requires auth + IS logged in?                       │
│  │       ├─ Inject access_token                             │
│  │       └─ Execute tool                                    │
│  ├─ Execute valid tools (async, bounded concurrency)        │
│  └─ Collect ToolMessage outputs                             │
│                                                             │
│  PHASE 4: Post-Execution State Management                   │
│  ├─ Detect 401 errors:                                      │
│  │   ├─ Extract original tool call                          │
│  │   ├─ Store as pending intent                             │
│  │   └─ Update error message with context                   │
│  ├─ Detect successful login (NOT auth → IS auth):           │
│  │   ├─ Fetch pending intents                               │
│  │   ├─ Generate tool calls for each intent                 │
│  │   ├─ Inject NEW token                                    │
│  │   ├─ Execute intents (same turn)                         │
│  │   └─ Clear pending intents                               │
│  └─ Detect UUID merge (login with new user_id):             │
│      ├─ Update state with new user_id                       │
│      └─ Fetch session with correct ID                       │
│                                                             │
│  Return: Updated messages + session_data + context          │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
       [Back to AGENT NODE]
       (Loop until END condition)
```

---

## 💻 Technology Stack

### Application Layer
| Component | Technology | Purpose |
|---|---|---|
| **Web Framework** | FastAPI 0.119.0 | Async API with automatic OpenAPI docs |
| **ASGI Server** | Uvicorn + Gunicorn | Production multi-worker server (5 workers default) |
| **Agent Orchestration** | LangGraph 0.6.10 | Finite-state machine workflows |
| **Primary LLM** | Gemini Flash 2.0 | 1M token context, native tool calling |
| **Embeddings** | Google Generative AI | 768-dim vectors for semantic search |

### Data Layer
| Component | Technology | Purpose |
|---|---|---|
| **Relational DB** | PostgreSQL 16 | Users, sessions, conversation history, audit logs |
| **Vector DB** | Qdrant 1.12.2 | Semantic search (KB + conversation memories) |
| **Cache/Coordination** | Redis 7 | Distributed locks, rate limiting, hot data (2-5ms) |
| **ORM** | SQLAlchemy 2.0.44 | Async database operations with type safety |
| **Migrations** | Alembic 1.17.0 | Versioned schema evolution |

### AI/ML Stack
| Component | Technology | Purpose |
|---|---|---|
| **LangChain Core** | 0.3.79 | Document loaders, text splitters, RAG framework |
| **LangChain Community** | 0.3.31 | PDF/DOCX loaders, BM25 retriever |
| **Sentence Transformers** | 5.1.1 | Cross-encoder reranking (ms-marco-MiniLM) |
| **FastEmbed** | 0.7.3 | Fast CPU embeddings (fallback option) |

### Infrastructure & DevOps
| Component | Technology | Purpose |
|---|---|---|
| **Containerization** | Docker + Compose | Multi-service orchestration (6 containers) |
| **Reverse Proxy** | Nginx | SSL termination, rate limiting, load balancing |
| **SSL/TLS** | Let's Encrypt | Automated certificate management |
| **Firewall** | UFW + fail2ban | Port filtering, DDoS protection, IP blocking |
| **CI/CD** | GitHub Actions | Automated testing + deployment pipeline |
| **Observability** | LangSmith + JSON logs | LLM tracing, structured logging with rotation |
| **Dev Tunneling** | Ngrok | Local development webhook testing |

---

## 🚀 Infrastructure & DevOps

### Server Architecture

**Production Environment** (VPS-based deployment):
- **Platform**: Linux Ubuntu 24.04 LTS
- **Docker Compose Stack**: 6 containerized services
- **Network**: Bridge networking with internal DNS
- **Persistence**: Named volumes for data durability
- **Resource Management**: Memory limits per container (PostgreSQL: 2GB, Qdrant: 6GB, App: 12GB)

### Docker Compose Services

1. **PostgreSQL** (`postgres:16-alpine`)
   - User/session data, conversation history, audit logs
   - Health check: `pg_isready` every 10s
   - Persistent volume: `postgres_data`
   - Exposed internally only (security)

2. **Redis** (`redis:7-alpine`)
   - Distributed locking, rate limit counters, session cache
   - Health check: `redis-cli ping`
   - Memory limit: 512MB
   - TTL-based expiration for hot data

3. **Qdrant** (custom Dockerfile with optimizations)
   - Vector storage for KB (50k+ documents) + conversations
   - Health check: `/readyz` endpoint
   - Persistent volume: `qdrant_data`
   - Memory limit: 6GB (handles large collections)

4. **Migrations** (Alembic auto-run)
   - Runs once on stack startup
   - Applies pending database schema changes
   - Depends on PostgreSQL health

5. **App** (Gunicorn + Uvicorn workers)
   - 5 Gunicorn workers (configurable via `GUNICORN_WORKERS` env)
   - Each worker runs async Uvicorn server
   - Binds to `127.0.0.1:8000` (Nginx proxies external traffic)
   - Health endpoint: `/health` with dependency checks
   - Automatic graceful shutdown on SIGTERM

6. **Ngrok** (development only - `docker-compose.override.yml`)
   - Exposes local port 8000 to public internet
   - Auto-configures Telegram webhook URL
   - Disabled in production

### Automated Deployment System

**`manage.sh` - One-Command Operations**

Core capabilities:
- `./manage.sh build [service]`: Build Docker images with layer caching
- `./manage.sh start`: Full stack startup with health checks
- `./manage.sh down`: Graceful shutdown (preserves data)
- `./manage.sh down-all`: Complete teardown (deletes volumes)
- `./manage.sh logs [service]`: Tail logs with color output
- `./manage.sh test-setup`: Start test dependencies
- `./manage.sh test-run pytest`: Execute integration tests

Intelligent features:
- Auto-detects environment (`.env.local` vs `.env.production`)
- GPU support: Adds `docker-compose.gpu.yml` if NVIDIA GPU present
- Distributed locking: Only first worker sets Telegram webhook (prevents race)
- Ngrok integration (dev mode):
  - Starts tunnel
  - Waits for public URL (15 retries, 2s interval)
  - Updates `.env.local` dynamically
  - Configures webhook automatically
- Pre-flight validation: Checks Docker, Docker Compose, `jq` availability

### Security Hardening

**Network Security**:
- **Firewall (UFW)**: Default deny inbound, allow 22 (SSH), 80 (HTTP), 443 (HTTPS)
- **fail2ban**: 3 failed login attempts → 10-minute IP ban
- **Nginx Security Headers**:
  - `Strict-Transport-Security`: Force HTTPS
  - `X-Frame-Options`: Prevent clickjacking
  - `X-Content-Type-Options`: Prevent MIME sniffing
  - `X-XSS-Protection`: Enable browser XSS filtering

**Application Security**:
- **Rate Limiting** (SlowAPI):
  - General endpoints: 30 requests/minute per IP
  - Auth endpoints: 5 requests/minute per IP (stricter)
  - Health check: 10 requests/minute (monitoring-friendly)
- **Input Sanitization**:
  - SQL injection protection: Parameterized queries via SQLAlchemy
  - XSS prevention: HTML entity encoding on user inputs
  - Path traversal: Whitelist-based file access
- **CORS Policy**: Whitelist-only origins (no `*` wildcard in production)
- **Trusted Host Middleware**: Prevents DNS rebinding attacks

**Authentication Security**:
- **JWT Token Management**:
  - Access tokens: 15-minute expiry
  - Refresh tokens: 7-day expiry
  - JTI blacklist: Immediate revocation on logout
  - Rotation on refresh (old token blacklisted)
- **Session Encryption**: Fernet symmetric encryption for PII
- **OTP Flow**: Email/SMS one-time codes (no password storage)

**Data Security**:
- **Encryption at Rest**: PostgreSQL/Qdrant data encrypted via LUKS (filesystem-level)
- **Encryption in Transit**: TLS 1.3 for all external communication
- **Secrets Management**: Environment variables (never committed to Git)
- **Backup Encryption**: AES-256 encrypted backups with secure key storage

### CI/CD Pipeline (GitHub Actions)

**Three-Branch Strategy**:
1. **`dev`**: Development branch (active development)
2. **`main`**: Staging branch (integration testing)
3. **`prod`**: Production branch (deployment trigger)

**Workflow 1: Continuous Integration** (`.github/workflows/ci.yml`)

Triggers: Push/PR to `main`

Steps:
1. **Lint & Type Check**: `ruff` (linting) + `mypy` (static types)
2. **Build Image**: Docker image build with layer caching
3. **Save Artifact**: Upload image as GitHub artifact (avoids rebuild)
4. **Integration Tests**:
   - Download image artifact
   - Start full stack (`docker-compose up -d`)
   - Run 10+ journey tests (`pytest tests/integration/`)
   - Aggressive disk cleanup (prevents runner storage exhaustion)

**Workflow 2: Continuous Deployment** (`.github/workflows/cd.yml`)

Triggers: Merge to `prod`

Steps:
1. **Build & Push**:
   - Build production-optimized image
   - Tag with `latest`, `prod-<sha>`, timestamp
   - Push to GitHub Container Registry (GHCR)
2. **Remote Deployment**:
   - SCP `deploy.sh` script to production server
   - SSH execute deployment:
     - Pull new image from GHCR
     - Safety retag as `esimtime-ai-assistant-app:latest`
     - Restart stack: `./manage.sh down && ./manage.sh start`
     - Health verification (60s loop, check container status)
     - Hash verification (compare running container vs. pulled image)

**Workflow 3: Automated Maintenance** (`.github/workflows/cleanup.yml`)

Triggers: Weekly (Sunday midnight)

Steps:
- Delete all but most recent workflow artifact
- Purge untagged Docker images from GHCR
- Keep only last 10 production-tagged images
- Delete workflow logs older than 3 days

**Required Secrets** (GitHub Repository Settings):
- `CONTABO_HOST`: Production server IP
- `CONTABO_USER`: SSH username
- `CONTABO_SSH_KEY`: Private SSH key for passwordless access
- `GEMINI_API_KEY`: For integration tests
- `POSTGRES_PASSWORD`: Test database credentials

### Database Management

**Schema Evolution** (Alembic):
- Versioned migrations in `alembic/versions/`
- Auto-generated via `alembic revision --autogenerate`
- Applied automatically on container startup
- Rollback support: `alembic downgrade -1`

**Connection Pooling**:
- SQLAlchemy pool size: 20 connections
- Max overflow: 10 (30 total concurrent)
- Pool recycle: 3600s (prevents stale connections)
- Connection timeout: 30s

**Backup Strategy** (production):
- Daily PostgreSQL dumps: `pg_dump` → compressed `.sql.gz`
- Weekly Qdrant snapshots: Vector collection exports
- Retention: 7 daily, 4 weekly, 3 monthly
- Offsite backup: Encrypted upload to cloud storage

### Monitoring & Observability

**Structured Logging**:
- Format: JSON (easily parsable)
- Fields: timestamp, level, logger, message, user_id, chat_id, duration_ms, etc.
- Rotation: Max 20MB per file, keep 5 files
- Centralized: Container logs accessible via `docker logs`

**LangSmith Integration**:
- Traces every LLM inference call
- Captures: prompts, completions, token usage, latency
- Tool execution tracking: args, outputs, duration
- Agent decision tracking: state transitions, routing logic
- Debugging: Full conversation replay capability

**Health Monitoring**:
- Endpoint: `/health` (GET request)
- Checks: PostgreSQL, Redis, Qdrant, external API connectivity
- Returns: `{"status": "healthy|degraded|unhealthy", "checks": {...}}`
- Status codes: 200 (healthy), 503 (degraded), 500 (critical)
- Used by: Docker health checks, uptime monitors, load balancers

**Performance Metrics** (logged per request):
- Total response time
- Database query time
- Vector search time
- LLM inference time
- Tool execution time
- Cache hit/miss rates

---

## 🧠 Conversational Intelligence Design

### Natural Language Understanding

The agent employs sophisticated prompt engineering to achieve human-like conversation quality while maintaining accuracy and reliability.

**Key Capabilities**:

**Multilingual Fluency**
- Automatically detects and responds in user's preferred language
- Supports English, Spanish, French, German, Portuguese, and more
- Maintains language consistency across authentication flows and errors
- Handles mixed-language queries gracefully

**Platform-Optimized Formatting**
- Telegram: Full markdown support, inline buttons, rich media
- WhatsApp: Emoji-first communication, business-friendly formatting
- Adapts message length and structure to platform constraints
- Uses visual hierarchy (emojis, bold, lists) for scannability

**Persona & Tone**
- Expert travel connectivity consultant (knowledgeable but approachable)
- Warm, enthusiastic communication style
- Liberal emoji use for engagement (✈️ travel, 🌍 coverage, 💳 payment)
- Balances friendliness with efficiency based on context

**Contextual Awareness**
- Distinguishes between product exploration, troubleshooting, and account management
- Adapts verbosity: brief for simple queries, detailed for complex issues
- Remembers conversation context across multiple turns
- Recognizes topic changes and adjusts accordingly

### Accuracy & Reliability Mechanisms

**Zero-Hallucination Policy**
- Never fabricates prices, product IDs, specifications, or availability
- Explicitly states uncertainty when information is incomplete
- Distinguishes between data-backed facts vs. general guidance
- Falls back to human support when confidence is low

**Authentication State Management**
- Seamlessly prompts for login when accessing private data
- Preserves user intent across authentication flows
- Detects successful logins without explicit user confirmation
- Recovers gracefully from session expiry

**Error Recovery Strategies**
- Asks clarifying questions for ambiguous requests
- Provides multiple interpretations when intent is unclear
- Gracefully handles malformed inputs or unexpected responses
- Never shows technical errors to users (user-friendly messages only)

**Intelligent Defaults**
- Assumes most common use cases when details are missing
- Asks focused questions rather than requesting all information upfront
- Progressive disclosure: reveals details as conversation evolves
- Balances thoroughness with user patience

### Conversational Patterns

**Consultant Mode** (Product Discovery)
- Asks about travel destination, duration, data needs
- Suggests plans with clear value propositions
- Compares options side-by-side when multiple fit
- Proactively mentions important details (coverage, speeds, restrictions)

**Troubleshooter Mode** (Technical Support)
- Gathers device type, OS version, and symptom description
- Follows systematic diagnostic flowchart
- Provides step-by-step instructions with visual confirmation
- Tracks progress and adapts based on user responses

**Account Manager Mode** (User Profile)
- Handles authentication with minimal friction
- Surfaces relevant account information efficiently
- Anticipates common follow-up queries (balance → recent purchases)
- Maintains security while being user-friendly

### Response Quality Assurance

**Structured Outputs**
- All responses include confidence scoring (internal tracking)
- Reasoning transparency (for debugging, not shown to users)
- Mandatory response wrapper ensures consistency
- Prevents accidental raw LLM outputs

**Content Safety**
- Validates all responses before delivery
- Checks for markdown syntax errors
- Ensures platform compatibility (Telegram vs. WhatsApp)
- Filters inappropriate content or language

**Performance Optimization**
- Timeout risk detection (simplifies response when latency is high)
- Caches frequently accessed data
- Batches multiple data requests when possible
- Prioritizes speed for simple queries, depth for complex ones

---

## 📊 Performance Metrics & Benchmarks

### Response Time Breakdown (Production)

| Component | Latency | Notes |
| :--- | :--- | :--- |
| **Redis Cache Hit** | 2-5ms | In-memory → Redis lookup |
| **PostgreSQL Query** | 20-50ms | Session/conversation fetch |
| **Qdrant Vector Search** | 50-100ms | Semantic search (k=10) |
| **BM25 Keyword Search** | 30-80ms | In-memory sparse index |
| **RRF Fusion** | 10-20ms | Rank aggregation algorithm |
| **Cross-Encoder Rerank** | 150-350ms | CPU-based scoring (ms-marco) |
| **Gemini Inference** | 250-800ms | Dependent on prompt complexity |
| **External API Call** | 100-500ms | Network latency to third-parties |
| **TOTAL (Simple)** | **1.5 - 3s** | "What's my balance?" |
| **TOTAL (Complex)** | **3 - 5s** | Multi-tool synthesis |

### Throughput & Scalability

**Single Worker**:
- Concurrent users: ~25-50
- Requests/minute: ~100-200

**5 Workers (Production Default)**:
- Concurrent users: ~125-250
- Requests/minute: ~500-1000

**Bottleneck Analysis**:
- PostgreSQL: 100 connection limit (configurable)
- Qdrant: 6GB RAM handles ~2M vectors
- Redis: Single-threaded but sub-5ms latency
- Gemini API: Rate limits vary by tier (TPM, RPM)

### Memory & Resource Usage

| Resource | Usage | Purpose |
| :--- | :--- | :--- |
| **PostgreSQL** | 500MB - 2GB | Sessions, conversations, checkpoints |
| **Redis** | 100MB - 300MB | Cache, locks, rate limits |
| **Qdrant** | 2GB - 4GB | KB vectors (~50k docs) + conversation vectors |
| **App (per worker)** | 800MB - 1.5GB | Python runtime, models, cache |
| **Total RAM** | **8GB - 12GB** | 5-Worker Production Footprint |
| **Disk (Persistent)** | 5GB - 10GB | Databases + Qdrant storage |

### Reliability Metrics

**Circuit Breaker Thresholds**:
- Failure count: 3 consecutive failures open circuit
- Recovery timeout: 15s (Redis), 30s (RAG)
- Half-open state: 1 successful request closes circuit

**Uptime Target**:
- SLO: 99.9% uptime (43.2 minutes downtime/month)
- Graceful degradation: Continues with cached data on partial failures
- Background health checks: Every 30 seconds

---

## 🧪 Quality Assurance & Validation

### Real-World Testing Scenarios

The system was validated against comprehensive real-world usage patterns representing the full spectrum of customer interactions.

**Scenario: First-Time Traveler**
- User new to eSIMs, traveling to Europe for 2 weeks
- Needs basic education on what eSIMs are and how they work
- Wants recommendation without overwhelming technical details
- Requires device compatibility confirmation
- Expected outcome: Confident purchase decision with clear next steps

**Scenario: Returning Customer**
- User has purchased before, knows the product
- Quickly checking account balance and active plans
- May want to purchase additional data or new destination
- Expects streamlined experience without re-explaining basics
- Expected outcome: Fast, efficient transaction

**Scenario: Bargain Hunter**
- User actively looking for best deals and promotions
- Compares multiple countries/regions for value
- Asks about coupon codes and discounts
- Expected outcome: Finds optimal plan with maximum savings

**Scenario: Technical Troubleshooting**
- User successfully purchased but can't get eSIM working
- Unclear what "activation" vs "installation" means
- Frustrated, needs patient step-by-step guidance
- Device/OS-specific configuration required
- Expected outcome: Working connection, calm user

**Scenario: Multi-Destination Trip**
- Complex travel itinerary (e.g., USA → Europe → Asia)
- Needs to understand coverage across regions
- Budget-conscious, wants single plan if possible
- May need multiple plans comparison
- Expected outcome: Clear recommendation with cost breakdown

**Scenario: Account Management**
- User needs to update password, check invoice history
- May have forgotten account details
- Requires secure authentication without friction
- Expected outcome: Account tasks completed smoothly

**Scenario: Cross-Platform Continuity**
- User starts conversation on Telegram, switches to WhatsApp
- Expects agent to recognize them without re-introduction
- Ongoing conversation should continue seamlessly
- Expected outcome: Unified experience across platforms

**Scenario: Language Switching**
- User starts in English, later messages in Spanish
- Agent should detect and adapt language preference
- Maintain context despite language change
- Expected outcome: Fluent conversation in user's preferred language

**Scenario: Ambiguous Requests**
- User says "I need internet in Europe" (vague)
- Agent needs to ask clarifying questions without annoying user
- Progressive information gathering
- Expected outcome: Efficient discovery through smart questions

**Scenario: Edge Case Recovery**
- User provides contradictory information
- Session expires mid-conversation
- Unexpected input formats or typos
- Agent maintains composure and guides user
- Expected outcome: Graceful error recovery

### Validation Methodology

**Automated Testing Approach**:
- End-to-end conversation flows simulating real user behavior
- Intelligent evaluation (not rigid string matching)
- Context-aware pass/fail criteria
- Validates both correctness AND user experience quality

**Success Criteria**:
- **Accuracy**: Responses factually correct, never hallucinated
- **Completeness**: User queries fully addressed
- **Efficiency**: Minimal back-and-forth for simple requests
- **Tone**: Appropriate warmth and professionalism
- **Formatting**: Platform-compatible markdown and emojis

**Evaluation Dimensions**:
- Does the response move the conversation forward?
- Is it free of critical flaws (hallucinations, contradictions)?
- Is formatting appropriate for the messaging platform?
- Would a real user find this helpful?

**Continuous Improvement**:
- Failed scenarios trigger prompt refinement
- Edge cases added to regression test suite
- Performance metrics tracked over time
- User feedback incorporated into validation criteria

---

## 📈 Project Evolution Timeline

### July 2024: Inception & Research
- Project officially commenced (contract signed 07/30/2024)
- Exploration of multi-agent architectures
- Experimentation with locally-hosted LLMs (Ollama + LLaMA 3.1)
- Initial RAG prototyping with ChromaDB

### August-September 2024: Phase 1 (Multi-Agent Research)
- Implemented 3-agent system (Orchestrator, API Agent, RAG Agent)
- Validated agentic workflows for complex conversations
- Identified latency bottlenecks (8-12s response times)
- Discovered context window limitations (128k insufficient)
- **Milestone**: Successfully proved concept, planned production pivot

### October 2024: Phase 2 Transition (Production Re-Architecture)
- Migration to Gemini Flash 2.0 (1M token context)
- Shift to single-agent + specialized tools pattern
- Qdrant integration for persistent vector storage
- Design of identity-aware authentication system
- **Milestone**: Achieved 60% latency reduction (3-5s)

### November 2024: Production Hardening
- Docker containerization and multi-service orchestration
- Multi-worker coordination with distributed locking
- Circuit breakers and comprehensive error handling
- Security hardening (firewall, fail2ban, rate limiting, input validation)
- **Milestone**: Production-grade reliability achieved

### December 2024: Full Automation & Deployment
- Complete CI/CD pipeline implementation (GitHub Actions)
- Automated deployment scripts (`manage.sh`)
- Implementation of MemoAI-inspired hybrid memory system
- Comprehensive integration test suites (10+ journeys)
- Production deployment and monitoring setup
- **Milestone**: System live, handling real customer interactions

---

## 🎓 Key Learnings & Insights

### 1. Research vs. Production Architectures
**Lesson**: Multi-agent systems are intellectually elegant but operationally complex.

**Decision**: Single-agent with specialized tools proved superior for production:
- 60% faster response times
- Simpler debugging (single execution path)
- Easier to extend (add new tools vs. coordinating agents)
- Lower maintenance burden

### 2. Context Windows Transform Architecture
**Lesson**: 128k tokens insufficient for full conversation history + KB context + system prompt.

**Decision**: Gemini Flash 2.0's 1M tokens eliminated need for complex memory management:
- No context pruning logic required
- Can include full conversation history
- Reduces "context amnesia" issues
- Simplifies prompt engineering

### 3. Live State > Conversation History
**Lesson**: LLM conversation history can be stale—always check live system state.

**Decision**: Implemented "auth_status supremacy" pattern:
- Fetch fresh session data before every agent decision
- Inject state changes as system messages
- Phantom login detection prevents UX confusion
- Priority: Live state > Tool outputs > History

### 4. LLM-Based Testing > Hardcoded Assertions
**Lesson**: Hardcoded assertions fail for generative, non-deterministic outputs.

**Decision**: LLM-as-evaluator with liberal pass thresholds:
- 10x faster test development
- Catches semantic errors traditional assertions miss
- Context-aware evaluation
- Easier to maintain (no brittle string matching)

### 5. Graceful Degradation is Essential
**Lesson**: External dependencies WILL fail—plan for it.

**Decision**: Multi-layer fallbacks with cached data:
- Circuit breakers prevent cascade failures
- Continues with reduced functionality
- User-friendly error messages (never show stack traces)
- Background health checks for recovery detection

### 6. DevOps is Part of the Product
**Lesson**: Beautiful AI is worthless if it can't be deployed reliably.

**Decision**: Invested heavily in infrastructure automation:
- One-command deployment (`./manage.sh start`)
- Automated migrations, health checks, backups
- CI/CD pipeline catches issues before production
- Monitoring and observability built-in from day one

---

## 🚀 Future Enhancement Opportunities

### Phase 3: Complete eSIM Lifecycle
- **Payment Integration**: Stripe/PayPal in-chat checkout
- **Order Management**: Track order status, delivery notifications
- **eSIM Auto-Provisioning**: Activate plans immediately after payment
- **Post-Purchase Support**: Automated activation assistance

### Phase 4: Advanced Analytics
- **Conversation Analytics**: Intent classification, drop-off analysis
- **Performance Dashboards**: Grafana visualization of metrics
- **A/B Testing Framework**: Test different prompts, flows, tools
- **User Feedback Loop**: Thumbs up/down with comment collection

### Phase 5: Voice Integration
- **Multi-Language Voice**: Real-time transcription + synthesis
- **Phone Call Handling**: IVR integration for voice queries
- **Accent Adaptation**: Support for non-native speakers

### Phase 6: Multi-Tenant Expansion
- **White-Label Platform**: Rebrand for other eSIM providers
- **API Marketplace**: Expose agent capabilities as API
- **Self-Service Onboarding**: Automated tenant provisioning

---


1. **Remember context** across multiple conversations (not just within a single chat)
2. **Access accurate information** from knowledge bases and live APIs
3. **Handle authentication** seamlessly without breaking conversation flow
4. **Never hallucinate** prices, product IDs, or specifications
5. **Respond quickly** (under 5 seconds) even with complex multi-step requests
6. **Scale reliably** to handle multiple concurrent users

### My Approach: A Two-Phase Evolution

I structured the project in two distinct phases, learning from each:

#### **Phase 1: Research & Validation** (July - September 2024)

**Goal**: Validate that autonomous agentic workflows could handle the business requirements

**Approach Taken**:
- Explored multi-agent architectures with specialized roles
- Used locally-hosted open-source LLMs for cost control during R&D
- Implemented in-memory vector storage for rapid prototyping
- Built proof-of-concept with basic session management

**Key Learnings**:
- ✅ Validated that agent workflows can handle complex multi-turn conversations
- ✅ Confirmed RAG (Retrieval-Augmented Generation) reduces hallucinations
- ⚠️ Discovered latency issues with multiple agent coordination
- ⚠️ Found context window limitations with smaller models
- ⚠️ Learned that local model performance varies significantly

**Outcome**: Successfully proved the concept, but identified architectural improvements needed for production.

#### **Phase 2: Production Engineering** (October - December 2024)

**Goal**: Build a production-ready system that's fast, reliable, and maintainable

**Approach Taken**:
- Migrated to a single-agent architecture with specialized tool calling
- Adopted cloud-based LLM with massive context window (1M tokens)
- Implemented persistent vector storage with proper indexing
- Built comprehensive authentication and session management
- Created automated deployment and testing pipelines

**Key Learnings**:
- ✅ Single-agent with tools is simpler and faster than multi-agent coordination
- ✅ Large context windows eliminate complex memory management
- ✅ Proper state management is critical for authentication flows
- ✅ LLM-based testing is more effective than hardcoded assertions for generative outputs
- ✅ Circuit breakers and graceful degradation are essential for production

**Outcome**: Achieved 60% faster response times, >99% tool accuracy, and production-grade reliability.

---

## 🧠 Key Technical Patterns Explored

### 1. Hybrid Memory Architecture (Inspired by MemoAI Research)

**Pattern**: Implement multiple memory systems for different purposes

**What I Built**:
- **Working Memory**: Recent conversation turns (fast access, temporary)
- **Episodic Memory**: User-specific long-term interactions and preferences
- **Semantic Memory**: Domain knowledge (guides, policies, troubleshooting)

**Why This Matters**: Different types of information require different retrieval strategies. Recent context needs speed; long-term patterns need semantic search; factual knowledge needs precision.

**Technical Approach**:
- Combined keyword-based search (BM25) with semantic vector search
- Used rank fusion algorithms to merge results from multiple sources
- Applied reranking to improve precision of top results
- Implemented caching layers (in-memory → Redis → database)

**Learning**: Hybrid retrieval consistently outperforms single-method approaches, especially when dealing with both structured data (tables) and unstructured text (guides).

### 2. Architecture Evolution: Multi-Agent to Single-Agent

**Initial Hypothesis**: Specialized agents would improve performance through division of labor

**What I Tried (Phase 1)**:
- **Orchestrator Agent**: Routing and conversation management
- **API Agent**: External system interactions
- **RAG Agent**: Knowledge base retrieval

**What I Discovered**:
- Agent coordination adds significant latency (each handoff = API call)
- Context passing between agents risks information loss
- Debugging multi-agent flows is extremely complex
- Benefits didn't outweigh coordination overhead

**Pivot Decision**: Migrate to single-agent with specialized tool calling

**What Changed (Phase 2)**:
- One agent with access to 15+ specialized tools
- Agent decides which tools to call and when
- Tools handle specific tasks (auth, API calls, KB lookup, etc.)
- Simpler architecture, easier debugging, faster execution

**Results**: 
- Response time decreased from 8-12s to 3-5s
- Tool execution accuracy improved to >99%
- System became easier to maintain and extend

**Learning**: For production systems, simplicity and performance often trump architectural elegance. The single-agent pattern proved superior for this use case.

### 3. Identity-Aware Authentication Across Channels

**Challenge**: Users might interact via Telegram today, WhatsApp tomorrow, and web next week - how do we recognize them as the same person without forcing re-login?

**Pattern Explored**: Cross-platform identity resolution

**Conceptual Approach**:
- Map platform-specific identifiers (Telegram ID, WhatsApp number, web session) to a unified user identity
- Implement passwordless authentication (OTP via email/SMS - no password storage vulnerabilities)
- Preserve conversation context across authentication state changes
- Handle session expiry gracefully without losing user intent

**Key Innovation - "Phantom Login" Detection**:

**Problem**: User requests private data → System asks for login → User provides credentials in separate channel → User returns and says "okay" or "ready"

**Question**: Did they log in, or are they just acknowledging the instruction?

**Solution**: Check authentication state BEFORE interpreting the message
- If auth state changed from "not authenticated" to "authenticated" between turns
- AND user has pending intents (the original request they made)
- THEN automatically execute the pending intent
- User experiences seamless flow: "show my data" → [logs in] → "okay" → [sees their data]

**Learning**: In conversational AI, **current state is truth**. Don't rely solely on conversation history - always check live system state before taking action.

### 4. Preventing AI Hallucination in Tool Usage

**Challenge**: LLMs can generate plausible-looking but non-existent tool calls, leading to errors and user confusion

**Pattern**: Multi-layer validation pipeline

**Validation Layers Implemented**:

1. **Tool Name Whitelist**: Reject calls to tools that don't exist in the system
2. **Schema Validation**: Verify arguments match expected types and formats before execution
3. **Loop Budget Limits**: Prevent infinite retry cycles (e.g., max 3 knowledge base queries per turn)
4. **Duplicate Detection**: Never execute the exact same tool call twice in one turn
5. **Graceful Failure**: Return user-friendly error messages, not stack traces

**Additional Safety Mechanisms**:
- Argument sanitization (prevent injection attacks)
- Rate limiting on expensive operations
- Timeout protection (abandon stuck operations)
- Circuit breakers (stop calling failing external services)

**Results**: Achieved >99% tool execution accuracy in production testing

**Learning**: Don't trust LLM outputs blindly. Validate everything before execution, and always have fallback behaviors for edge cases.

### 5. Production-Grade Reliability Patterns

**Challenge**: External dependencies fail. Databases go down. Networks timeout. Production systems must handle failures gracefully.

**Patterns Implemented**:

**Circuit Breaker Pattern**:
- Monitor failure rates for each external dependency
- After N consecutive failures, "open the circuit" (stop calling the failing service)
- Use cached/fallback data instead
- Periodically test if service has recovered
- "Close the circuit" when service is healthy again

**Graceful Degradation**:
- Identify which features are critical vs. nice-to-have
- When non-critical services fail, continue with reduced functionality
- Example: If knowledge base is down, use conversation history and apologize for limited context
- Never show raw error messages to users

**Health Monitoring**:
- Regular health checks for all dependencies (database, cache, vector store, external APIs)
- Report overall system status as: healthy | degraded | unhealthy
---

## 📚 Technical References & Inspirations

### Research Papers & Publications

**MemoAI: Episodic Memory for LLMs**
- Paper: [MemoAI (2023)](https://arxiv.org/abs/2310.10809)
- Inspiration for hybrid memory architecture (episodic + semantic)
- Applied: Dual-memory system with Qdrant vector store

**Reciprocal Rank Fusion (RRF)**
- Paper: [Cormack et al., SIGIR 2009](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf)
- Combines multiple ranked lists for better retrieval
- Applied: Fusion of BM25 + vector search results

**Cross-Encoder Reranking**
- Model: [sentence-transformers/ms-marco-MiniLM-L-6-v2](https://huggingface.co/cross-encoder/ms-marco-MiniLM-L-6-v2)
- Improves precision of top-k retrieval results
- Applied: Final reranking stage after RRF fusion

### Open-Source Tools & Frameworks

**LangChain Ecosystem**
- [LangChain Core](https://python.langchain.com/docs/get_started/introduction): RAG framework, document loaders
- [LangGraph](https://langchain-ai.github.io/langgraph/): Agentic workflows with finite-state machines
- [LangSmith](https://smith.langchain.com/): LLM tracing and observability

**Vector Databases**
- [Qdrant](https://qdrant.tech/documentation/): High-performance vector search
- Chosen for: Hybrid search support, persistent storage, Docker-friendly

**LLM Providers**
- [Google Gemini](https://ai.google.dev/): Gemini Flash 2.0 (1M token context)
- Chosen for: Context window, speed, native tool calling, cost-effectiveness

**Infrastructure & DevOps**
- [FastAPI](https://fastapi.tiangolo.com/): Modern async Python web framework
- [Docker](https://docs.docker.com/): Containerization and orchestration
- [GitHub Actions](https://docs.github.com/en/actions): CI/CD automation
- [PostgreSQL](https://www.postgresql.org/): Production-grade relational database

### Learning Resources

**Agent Architectures**
- AutoGPT, BabyAGI: Explored for agentic patterns
- ReAct (Reasoning + Acting): Informed tool calling design
- Multi-agent coordination: Validated then abandoned for production

**RAG Best Practices**
- Pinecone, Weaviate documentation: Vector DB comparisons
- LlamaIndex guides: Chunking strategies, retrieval optimization
- Sentence Transformers: Embedding and reranking models

**Production ML Systems**
- Chip Huyen's "Designing Machine Learning Systems"
- Google's "Rules of Machine Learning"
- Netflix, Uber engineering blogs: Production patterns

---

## 👨‍💻 About the Developer

**Joshua Peter Polaprayil**  
AI Engineer | LLM Systems Architect

I specialize in production-grade conversational AI systems, with expertise in:
- Agentic workflows (LangGraph, AutoGPT)
- Hybrid RAG systems (BM25 + vector + reranking)
- Multi-channel bot development (Telegram, WhatsApp, web)
- Identity-aware authentication systems
- Production deployment (Docker, CI/CD, observability)

**Connect:**
- LinkedIn: [linkedin.com/in/joshua-peter-polaprayil](https://linkedin.com/in/joshua-peter-polaprayil)
- Email: josh19peter96@gmail.com
- GitHub: [github.com/josh19peter96](https://github.com/josh19peter96)

---

## 🙏 Acknowledgments & Project Ownership

### Business Ownership & Vision

This project was **conceived and commissioned by Herman Güler**, CEO of **[EsimTime](https://esimtime.com)**.

**Herman's contribution was fundamental:**
- Original business concept: Using AI to automate eSIM customer service
- Product vision and requirements definition
- Entrepreneurial leadership and project direction
- Trust and opportunity to build this system

**The idea belongs to Herman and EsimTime.** This case study documents my technical implementation of that vision.

### Technical Implementation

As the AI Engineer on this project, my contribution was the technical architecture and implementation:
- Technology research and selection
- System design and architecture decisions
- AI agent implementation and RAG systems
- Authentication and production hardening
- Testing frameworks and deployment automation

### Collaborative Partnership

This project demonstrates what's possible when business vision meets technical execution:
- **Herman provided**: The problem to solve, business requirements, and market insights
- **I contributed**: The technical solution, architecture, and engineering implementation
- **EsimTime Team**: Backend API support and integration assistance

### Deep Gratitude

**Special Thanks:**
- **Herman Güler** (EsimTime CEO) - For the opportunity, trust, and collaborative partnership throughout this journey
- **EsimTime Engineering Team** - Backend API integration and technical support
- **Google AI** - Gemini Flash 2.0 API access
- **LangChain & Qdrant Teams** - Excellent open-source tools and documentation
- **The AI Research Community** - For publishing papers and patterns that informed this work

### What This Repository Represents

This is a **portfolio and learning showcase** documenting:
- ✅ My engineering skills and problem-solving approach
- ✅ Technical patterns I explored and learnings I gained
- ✅ Publicly available architectural concepts (nothing proprietary)

This is **NOT**:
- ❌ Source code (remains proprietary to EsimTime)
- ❌ Business-confidential information
- ❌ A competing product or service

**Built With:**
- ❤️ Passion for solving real-world problems with AI
- 🧠 6 months of learning, research, and iteration
- 🚀 Commitment to production-grade engineering practices
- 🤝 Respect for collaborative partnerships

---

## 📄 License

**MIT License** - See [LICENSE](LICENSE) file for details.

This repository contains **architectural documentation and case study materials only**.  
The actual codebase remains proprietary to EsimTime Inc.

**Usage**: This documentation may be freely referenced for educational purposes and portfolio showcasing.

---

## 💬 Questions?

Feel free to reach out if you have questions about the architecture, technical decisions, or want to discuss similar projects!

**Email**: josh19peter96@gmail.com  
**LinkedIn**: [Joshua Peter Polaprayil](https://linkedin.com/in/joshua-peter-polaprayil)

---

**Last Updated**: December 27, 2024  
**Status**: ✅ Production-Ready Architecture (Deployed July-December 2024)

---

<p align="center">
  <em>Built with precision. Deployed with confidence. Documented with care.</em>
</p>
