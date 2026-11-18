# Building a Transaction-Capable AI Agent Before There Was a Playbook

### Engineering field notes from thirteen months building production agentic AI — CPU-only, about $17 a month plus a few cents per conversation

<p align="center">
  <strong>Joshua Peter Polaprayil</strong> · Full-Stack AI Engineer<br/>
  <em>AI · Commerce · Retrieval · Security · Infrastructure</em>
</p>

<p align="center">

[![Python 3.12](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-async-009688.svg)](https://fastapi.tiangolo.com)
[![LangGraph](https://img.shields.io/badge/LangGraph-agent_loop-blueviolet.svg)](https://langchain-ai.github.io/langgraph/)
[![Gemini](https://img.shields.io/badge/Gemini-Flash-4285F4.svg)](https://ai.google.dev)
[![Qdrant](https://img.shields.io/badge/Qdrant-native_hybrid-orange.svg)](https://qdrant.tech)
[![ONNX](https://img.shields.io/badge/ONNX-INT8_CPU-005CED.svg)](https://onnxruntime.ai)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED.svg)](https://docs.docker.com/compose/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-WAF%20%2B%20Turnstile-F48120.svg)](https://cloudflare.com)

</p>

---

> **Scope** · A production case study of a system that served real customers. Architecture, trade-offs, measurements and failures are documented in full. Source code is not included.

> [!IMPORTANT]
> **Status note.** I designed, built and operated this platform from July 2025 until my handover in August 2026. This document describes the system as it stood at that point — a snapshot, not a live record. I no longer maintain the platform or hold access to it, and nothing here should be read as describing its current state.
>
> The handover included full documentation, a directory of every open item, and an explicit list of what was shipped versus what was scoped but not started. The items that were still open are described in [Known Limitations](docs/07-reflections.md#known-limitations), as they stood at handover.

---

## A Note Before You Read

When I started this work in July 2025, transaction-capable autonomous agents — agents that hold state, manage identity, and complete a purchase end to end — were not yet a commercial category.

The public timeline bears that out. OpenAI's in-chat checkout, Instant Checkout, launched on 29 September 2025. Google unveiled its Universal Commerce Protocol for agentic shopping on 11 January 2026. Through the first quarter of 2026, this agent went live provisioning eSIMs and taking payments from real customers inside WhatsApp and Telegram.

The more interesting fact isn't "early". OpenAI wound Instant Checkout down in March 2026, around five months after launch: only about a dozen Shopify merchants had gone live, and Walmart reported that purchases completed inside ChatGPT converted at roughly a third of the rate of click-through. This system is a different scale and a different vertical — a focused eSIM concierge, not a general marketplace — so the comparison isn't "this beat that". It's narrower and more useful. A purchase journey that starts, confirms and completes inside the conversation — wallet payments settling in the thread, card payments through a hosted checkout link, the activation code delivered back into the chat — was still running here at handover in August 2026, re-integrated through a steady stream of backend changes along the way, while a far larger implementation of a similar pattern had already been walked back.

That gap is why this document exists. The core problems here — exactly-once processing of proactive events, cross-platform identity resolution, intent preservation across authentication boundaries, prompt-injection defence inside the request path — had to be solved from first principles, because there was no reference implementation to borrow from. Some of it would be built differently today with tooling that now exists. That's the point. These are field notes from before the playbook.

**What this is:** an architectural and engineering case study of a production system I designed, built and operated — written as a technical narrative. What I tried, what broke, what I measured, what I chose instead.

**What this is not:** an implementation guide. No source code, business logic, schemas or credentials appear here. Security thresholds are given where they're instructive, because the defensive design is meant to be reusable.

---

## TL;DR

A production AI agent handling the complete eSIM customer lifecycle — discovery, authentication, payment, provisioning, activation, troubleshooting — autonomously, inside WhatsApp and Telegram, in text, voice and images.

| | |
|---|---|
| **Response time** | **P50 9.3s** · P90 20.7s over a 7-day production window — dominated by LLM output tokens, see [Performance](docs/06-performance.md) |
| **Voice round-trip** | **P50 7.7s** across 8 production traces, down from 24–28s after a measurement-driven optimisation round |
| **Tool accuracy** | **>99%** across the integration suite, 26 schema-validated tools |
| **Under adversarial load** | **19,164** malicious payloads absorbed at **319 RPS** while legitimate traffic held **100% success** (bench, pre-multimodal) |
| **Injection classification** | p50 **23.8ms** · p95 32.2ms — INT8 on CPU, inline in the request path |
| **Webhook ingest** | median 5ms · p95 9ms · p99 14ms · 100% delivery (bench) |
| **System prompt** | **82%** token compression with no measured behaviour loss; ~15k-token static prefix served from an explicit cache |
| **Testing** | 159 unit and contract tests · 49 LLM-judged journey scenarios · a live end-to-end driver with nothing mocked |
| **Observability** | 51 application metrics · ~120-panel operator dashboard · 8 alert rules as code |
| **Languages** | Agent mirrors the user's language · system text in 10 locales incl. RTL · speech voice map covers 32 languages |
| **Hardware** | One 8 vCPU CPU-only VPS — no GPU, anywhere |
| **Running cost** | **About $17 a month**, development included, at handover list prices: a €14 server and a $10-a-year domain, with the dev tunnel, CI/CD, secret scanning, tracing and the edge on free plans. Per use: $0.005 a WhatsApp message, in or out, and about 2.3¢ of LLM a conversation at the paid rate before caching, though at its traffic production ran almost entirely on Gemini's free tier. Speech, guards, reranking and data stores are self-hosted ([breakdown](docs/06-performance.md#economics)) |
| **Recognition** | Selected **top 5 of 39** at an investor pitch · **#5** in F6S's AI company ranking for Sheridan, Wyoming, where the company is registered ([May 2026](#recognition)) |
| **On the record** | **2,385** test messages exported from Telegram and WhatsApp, platform-timestamped, the wallet balance agreeing to the cent across both · native screenshots and original voice notes, hashed · [the exhibits](#from-the-test-logs) |

**The model set, all on CPU:** three INT8 ONNX models in the request path — Meta's Llama Prompt Guard 2 for injection classification, a multilingual NER model for PII detection, and a cross-encoder reranker — plus self-hosted speech (faster-whisper at int8 and Piper TTS) and an offline-trained XGBoost risk classifier exported to ONNX.

<p align="center">
  <img src="docs/images/grafana_ss_1.jpeg" alt="Live Grafana operator dashboard showing conversations, answer success rate, typical reply time, and host and container capacity gauges" width="760"/>
</p>
<p align="center">
  <img src="docs/images/latest_login_flow_ss1.jpeg" alt="WhatsApp screenshot of the login-relay authentication flow, including a fresh link issued on request and the welcome offer after login" width="430"/>
  &nbsp;&nbsp;
  <img src="docs/images/purchase_flow_explanation_ss.jpeg" alt="The agent explaining its own purchase-flow logic in plain language, matching the documented consultant behaviour" width="250"/>
</p>
<p align="center"><em>Not staged: the live operator dashboard; the login relay on the production domain, on a second test account (link tokens, code and eSIM identifiers boxed); and the agent describing its own design when asked, on the production number on 13 March 2026 (WhatsApp lines 690–701). Dashboard panels reflect their own time windows; the measured 7-day figures are in <a href="docs/06-performance.md">Performance</a>.</em></p>

---

## Architecture at a Glance

```mermaid
flowchart LR
    subgraph IN["Channels"]
        TG["Telegram"]
        WA["WhatsApp"]
        EV["Payment and<br/>backend events"]
    end

    subgraph SHIELD["Defence before inference"]
        EDGE["Edge<br/>WAF · firewall"]
        GATES["Gates<br/>human · behavioural<br/>semantic · honeypot"]
    end

    subgraph CORE["Agent core"]
        STREAMS["Redis Streams<br/>exactly-once · DLQ"]
        AGENT["LangGraph agent<br/>Gemini Flash · 26 tools"]
        MEM["Memory<br/>working · episodic"]
        KB["Hybrid retrieval<br/>Qdrant · RRF · reranker"]
        SPEECH["Speech and vision<br/>Whisper · Piper · Gemini"]
    end

    subgraph STORE["State"]
        PG[("PostgreSQL")]
        RD[("Redis")]
        QD[("Qdrant")]
    end

    TG --> EDGE
    WA --> EDGE
    EV --> EDGE
    EDGE --> GATES --> STREAMS --> AGENT
    SPEECH --> AGENT
    AGENT --> MEM
    AGENT --> KB
    MEM --> PG
    MEM --> QD
    KB --> QD
    STREAMS --> RD
    AGENT -- "chunked, localised replies" --> TG
    AGENT -- "chunked, localised replies" --> WA
```

The full component diagram is in [System Architecture](docs/05-architecture.md#system-architecture); every defence layer is mapped in the [Security Deep Dive](docs/09-security-architecture.md#3-the-layer-map).

---

## Evolution at a Glance

```mermaid
gantt
    title July 2025 to August 2026
    dateFormat YYYY-MM-DD
    axisFormat %b %y
    section Research
    Multi-agent research                  :2025-07-01, 2025-09-30
    section Production
    Single-agent pivot                    :2025-10-01, 2025-10-31
    Identity memory hardening             :2025-11-01, 2025-12-31
    section Commerce
    In-chat commerce and proactive events :2026-01-01, 2026-03-31
    Investor pitch top 5 of 39            :milestone, 2026-02-16, 0d
    section Hardening
    Security under live traffic           :2026-04-01, 2026-06-30
    Observability                         :2026-04-01, 2026-07-31
    F6S rank 5 AI in Sheridan WY          :milestone, 2026-05-15, 0d
    section Multimodal
    Voice and image                       :2026-05-01, 2026-08-10
    Measurement round                     :2026-07-15, 2026-08-10
```

| | Research (Phase 1) | Production pivot (Phases 2–3) | At handover (Phases 4–7) |
|---|---|---|---|
| **Architecture** | Three coordinating agents | Single agent, six-node graph, two LLM calls per turn | Single agent, two-node loop, one LLM call per step · commerce · proactive events |
| **LLM** | LLaMA 3.1 8B, local | Gemini Flash | Gemini 2.5 Flash — successor evaluated, not yet decided |
| **Context** | 128k | 1M | 1M |
| **Vector store** | In-memory | Qdrant, persistent | Qdrant, native server-side hybrid |
| **Auth** | Basic sessions | OTP + cross-platform identity | Web-link login relay · billing identity |
| **Memory** | Conversation only | Episodic + semantic | Episodic + semantic, PII-scrubbed |
| **Commerce** | — | — | Checkout, wallet balance, free trials |
| **Notifications** | — | — | Exactly-once, dead-letter queue, pending store |
| **Modality** | Text | Text | Text, voice, image |
| **Security** | — | Rate limits, encryption | Ten defence layers, ML-classified, load-tested |
| **Tools** | ~5 | 15+ | 26 |
| **Response time** | 8–12s | 3–5s (text-only stack) | P50 9.3s (multimodal stack, production) |

---

## The Case Study

An engineering narrative, split by theme. Start anywhere — each part stands on its own.

| | Part | What's in it |
|---|---|---|
| **01** | [**Timeline — Seven Phases**](docs/01-timeline.md) | The brief, the constraint that shaped every decision, and seven phases from multi-agent research prototype to transaction-capable production agent. |
| **02** | [**Platform Constraints**](docs/02-platform.md) | Message delivery and localisation, the platform limits you can't engineer around, the boot loop, and a measured memory model for running speech and classifiers under a fixed RAM ceiling. |
| **03** | [**Models, Temperature & Attention**](docs/03-models.md) | A 24-run model evaluation, why the decision stayed open, and the wrong-product-ID failure that no prompt or temperature setting could fix. |
| **04** | [**Evaluation, Testing & Delivery**](docs/04-engineering.md) | Offline MLOps, the test layers, contract tests for silent drift, the incident that redefined "tested", and the delivery pipeline. |
| **05** | [**System Architecture**](docs/05-architecture.md) | The agent loop, the sixteen-section system prompt, hybrid retrieval, the data layer, and the full technology stack. |
| **06** | [**Performance & Economics**](docs/06-performance.md) | Measured latency across the whole request path, with provenance, and what running all of it costs, line by line, at handover and now, against cloud, frontier-model and human alternatives. |
| **07** | [**Limitations, Next Steps & Lessons**](docs/07-reflections.md) | What was still open at handover, the prioritised work that came next, how the system is governed, and what I'd tell someone starting this today. |

**Deep dives, outside the seven-part narrative:**

| | Deep dive | What's in it |
|---|---|---|
| **08** | [**Research Notes**](docs/08-research-notes.md) | Two experimental prototypes of published agentic techniques — a hierarchical plan–execute–reflect agent and an LLM-synthesised memory closer to Mem0 — presented as concepts, with their structural costs. |
| **09** | [**Security Architecture**](docs/09-security-architecture.md) | Ten defence layers in full: the human gate, behavioural and semantic gates, honeypot, agent-initiated flagging, leakage-safe ML risk scoring, PII and encryption design, and the adversarial load test. |

---

## Ideas Worth Taking

The patterns from this project I'd reuse anywhere, each documented in depth.

| Idea | In one line | Where |
|---|---|---|
| **Phantom login** | Queue the request that needed auth, detect the login, execute it — the user asks once | [01](docs/01-timeline.md#phase-3--identity-memory-hardening-novdec-2025) |
| **Resolve the fact before the model describes it** | Mint the login link *before* generating the message that promises one | [01](docs/01-timeline.md#phase-3--identity-memory-hardening-novdec-2025) |
| **Validate selections against what was shown** | A purchase is refused unless the product ID and price match what this user was just shown | [03](docs/03-models.md#temperature-attention-and-the-wrong-product-id) |
| **Temperature fixes fabrication, not selection** | Two identical-looking failures, two completely different fixes | [03](docs/03-models.md#temperature-attention-and-the-wrong-product-id) |
| **Contract tests for silent drift** | Prompt vs toolset, training vs inference, score vs threshold | [04](docs/04-engineering.md#contract-tests-for-the-drift-nothing-else-catches) |
| **Tested is not verified** | A path is verified only when run against the real system *and* the resulting state is re-read | [04](docs/04-engineering.md#the-incident-that-redefined-tested) |
| **Retrieval on demand, not on every turn** | Don't pay reranking latency on greetings and balance checks | [05](docs/05-architecture.md#retrieval-architecture) |
| **Size to the recycle peak** | Measure shared vs per-worker memory; plan for N+1 workers during restarts | [02](docs/02-platform.md#the-measured-memory-model) |
| **Crawlers consume single-use links** | Serve link-preview bots a decoy before they spend the user's verification | [09](docs/09-security-architecture.md#5-the-human-gate) |
| **Lift a block everywhere it was set** | An unblock that didn't reach every store the block lived in kept it answering every message | [09](docs/09-security-architecture.md#on-the-record-the-agent-flagged-me) |
| **Silence for adversaries, clarity for customers** | One notice a day when suspended; nothing at all when banned | [09](docs/09-security-architecture.md#6-the-behavioural-gate--userguard) |
| **Deny the model the label's inputs** | A risk classifier trained on the columns its labels come from learns nothing — and scores brilliantly | [09](docs/09-security-architecture.md#11-ml-risk-scoring) |
| **Ask your dashboards a question** | Executing every panel's query found 48 of 61 targets silently empty | [01](docs/01-timeline.md#the-dashboards-were-lying-and-i-only-found-out-by-asking-them) |
| **A confirmation is a fact, not a sentence** | The agent confirmed a second purchase the wallet couldn't pay for; the ledger was right and the words were wrong | [01](docs/01-timeline.md#from-the-test-log-the-money-and-the-words) |
| **What the model can see, it can say** | Deliver secrets beside the agent, never through it — it quoted a verification code back three days later | [↓](#case-study-the-verification-code-the-agent-repeated) |

---

## From the Test Logs

The rest of this document is my account. This section is the record underneath it: my own test conversations with the agent, exported from the platforms themselves — **1,777 messages** with the Telegram development bot (May–August 2026) and **608** with the production WhatsApp number (November 2025 – September 2026). Every message carries the platform's own timestamp. The wallet balance agrees to the cent across the two channels: **$35.22** on the development bot on 18 July is what the production number speaks on 29 July, and **$33.72** after a purchase on the development bot on 2 August is the balance the production number reports on 30 August. Several of the fixes described in the case study appear in the logs as dated before-and-after pairs.

Screenshots are taken in the apps themselves, with solid boxes over codes, contact details and eSIM identifiers, and text quoted from them is verbatim. References point into the exports: `#4808` is a Telegram message id, and "WhatsApp line 1777" is a line of the exported chat. Voice notes are the original files, byte for byte, in [`docs/test-log/audio`](docs/test-log/audio), each named for its message: `tg-4810` is Telegram #4810, `wa-1808` WhatsApp line 1808. [`SHA256SUMS`](docs/test-log/SHA256SUMS) binds all of it to what I hold: the full exports, every audio file, and each screenshot's **unredacted original**, so the boxes can be checked against the original later without revealing what is under them. In `docs/test-log`, `sha256sum -c --ignore-missing SHA256SUMS` checks whatever is present. The exports: Telegram `ec95789070d4000a3b60db6e3519d0fadea685ef4280d5347c8bf7505ebc7227`, WhatsApp `46b45232dcd9e11d8ce2713e91c0e651278f1ba4e1e6d05db0023006c56ac9ce`.

| Exhibit | Recorded | What it shows | Evidence | Where |
|---|---|---|---|---|
| **Two confirmations, one debit** | 24 Jul 2026 | A $1.63 plan "bought" twice from a $1.95 balance, the agent's own voice note admitting it, and the clean purchase nine days later | screenshots · voice | [01](docs/01-timeline.md#from-the-test-log-the-money-and-the-words) |
| **The split payment that half-ran** | 17 Jul 2026 | Wallet leg taken, card leg failed, nothing rolled back — fixed the same afternoon | screenshot | [01](docs/01-timeline.md#from-the-test-log-the-money-and-the-words) |
| **The human gate** | 26 May → 3 Aug 2026 | The decoy preview card, the verification link and the held message answered, on the production number and on Telegram with its contact share, and the verification page itself | screenshots | [09](docs/09-security-architecture.md#5-the-human-gate) |
| **Flagged by its own agent** | 24–25 Jul 2026 | My photo of the operator dashboard flagged within a minute, and a block that outlived its unblock | screenshot | [09](docs/09-security-architecture.md#on-the-record-the-agent-flagged-me) |
| **"I wasn't able to transcribe it"** | 20 Jul → 2 Aug 2026 | A clear six-second question refused as unintelligible, then the same kind of question answered | voice | [01](docs/01-timeline.md#phase-7--multimodal-q2q3-2026) |
| **"$35.22 Sparkles"** | 26 Jul → 3 Aug 2026 | The ✨ in a reply read aloud by its name, on both channels, then gone | voice | [01](docs/01-timeline.md#phase-7--multimodal-q2q3-2026) |
| **Hindi in an English voice** | 3 Aug 2026 | The speech bug in Known Limitations, as first heard | voice | [07](docs/07-reflections.md#known-limitations) |
| **"Letter 1E37"** | 30 Aug 2026 | The same bug on the production number, with accented letters spoken as Unicode code points | voice | [07](docs/07-reflections.md#known-limitations) |
| **The code the agent repeated** | 23 → 26 May 2026 | A verification code quoted back three days after delivery | screenshot · text | [↓](#case-study-the-verification-code-the-agent-repeated) |

<p align="center">
  <img src="docs/test-log/phantom_purchase_chat.png" alt="Telegram, 24 July 2026: two 'Funds locked in' confirmations for the same $1.63 plan from a $1.95 balance, my question how, and the agent's 24-second voice reply" width="300"/>
</p>

https://github.com/user-attachments/assets/8c395637-858e-4850-a942-791f55397244

<p align="center"><em>24 July 2026, 00:44–00:49, in the Telegram app and as audio: the agent's 24-second voice note, which admits "You only had enough in your balance for one plan, not two", and the original, <a href="https://cdn.jsdelivr.net/gh/JoshPola96/enterprise-ai-agent-architecture-esim@40124bf1de749be8224c53c3eef61322bdc9fd42/docs/test-log/audio/tg-4810.ogg">tg-4810.ogg</a>. The full exchange, and why the ledger was right while the words were wrong, is in <a href="docs/01-timeline.md#from-the-test-log-the-money-and-the-words">Phase 4</a>. Sound on: the player starts muted.</em></p>

### Case study: the verification code the agent repeated

On 23 May 2026 the production agent's welcome message carried a one-time password in its text. Three days later I said hello, and the agent answered:

> **Esimtime Agent** · WhatsApp, 26 May 2026, 17:38 — Hello again! 👋 Were you able to verify your account using the OTP I provided earlier (`[code removed]`) and the link in your email? Let me know if you need any help with that, or if you're ready to explore some plans!

<p align="center">
  <img src="docs/test-log/otp_repeated_chat.jpg" alt="WhatsApp, production number: the 23 May welcome message carrying a one-time password, boxed; then on 26 May two replies that quote the same code back, boxed" width="360"/>
  <br/>
  <em>The production number in the WhatsApp app: the code delivered in the welcome text on 23 May at 00:22 (line 1025), then quoted back on 26 May at 17:38 and 18:26 (lines 1039 and 1041). The code is boxed in all three places; the hash of the unredacted original is in <code>SHA256SUMS</code>.</em>
</p>

It was the same code, character for character, and the agent quoted it again at 18:26. Nothing had malfunctioned in the usual sense: the model was being helpful with what was in its context, and the code was in its context because the message that delivered it had gone through the conversation. **What the model can see, it can say** — in a reply, a trace or a memory summary. Asking it not to is a prompt rule, and a prompt rule is not a control.

The fix moved the code out of the model's mouth. From 29 May each code arrives as a message of its own, from the same number but not from the model:

> **Esimtime Agent** · WhatsApp, 29 May 2026, 20:48 — *`[code removed]`* is your verification code. For your security, do not share this code.

That is WhatsApp's authentication template: fixed wording that Meta approves and the model never composes, sent through the [notification path](docs/01-timeline.md#phase-4--commerce--proactive-engagement-q1-2026) rather than the agent loop. The agent only asks the user to send the code back, and from then on the production log shows it asking for codes, never quoting them.

The stricter version was never built. In the final login relay the user still pastes an emailed code back into the chat ([Phase 3](docs/01-timeline.md#phase-3--identity-memory-hardening-novdec-2025)); it is scrubbed before logs and long-term memory ([§12](docs/09-security-architecture.md#12-data-protection)), but it sits in the context of the turn that verifies it. The next step is to take the code on the relay's own web form, out of band, so that it never reaches the model. MCP's URL-mode elicitation ([SEP-1036](https://modelcontextprotocol.io/community/seps/1036-url-mode-elicitation-for-secure-out-of-band-intera)) standardises exactly this for agent tooling: credentials go to a URL, never through the client.

---

## Recognition

**F6S ranking — May 2026.** Ranked **#5** in F6S's monthly list of AI companies in Sheridan, Wyoming, where the company is registered. The [list](https://www.f6s.com/companies/artificial-intelligence/united-states/wyoming/sheridan/co) re-ranks every month, so here is [a snapshot from the time](https://www.instagram.com/p/DZIYxqtErg8/). F6S reshared the announcement to its own audience.

**Investor pitch — February 2026.** The company pitched to investors alongside 38 other startups and was selected **top 5 of 39** for the final round, with the autonomous agent highlighted as the key product differentiator. The final round was a demo day for investors in Istanbul on 6 April 2026.

<p align="center">
  <img src="docs/images/demo_day_2026-04-06.jpg" alt="The founder on stage beside slide 16 of the company's deck, titled Ekibimiz, Turkish for Our Team: Joshua P. Polaprayil as AI/ML Engineer, a full-stack engineer and a frontend developer; a Turkish flag to the left of the screen, and the audience in the foreground" width="640"/>
  <br/>
  <em>The demo day, 6 April 2026: the founder presenting the team slide, with me as AI/ML Engineer. The banners at the edges are KOSGEB's and BTM TEKMER's, the Istanbul Chamber of Commerce's startup centre.</em>
</p>

**The company's own team slide.** From the company's presentation deck, shown uncropped as I shared it in my [post of 7 April 2026](https://www.linkedin.com/posts/josh33-peter10_ai-startups-engineering-ugcPost-7447292696014016513-Jj4F/): the founder, me as AI/ML, and two engineers on backend and frontend — the split described in [My Role](#my-role).

<p align="center">
  <img src="docs/images/team_slide_2026-04.jpg" alt="Slide 13 of the company's presentation deck, titled Our Team: the founder, Joshua P. Polaprayil as AI/ML, a backend engineer and a frontend engineer" width="640"/>
</p>

**Founder's public acknowledgement — June 2026.** On my [F6S announcement](https://www.linkedin.com/posts/josh33-peter10_esimtime-aiagents-esim-share-7467970557624360960-LKuY/), the company's founder [commented](https://www.linkedin.com/feed/update/urn:li:activity:7467970560543797249/?dashCommentUrn=urn%3Ali%3Afsd_comment%3A%287468040954906673153%2Curn%3Ali%3Aactivity%3A7467970560543797249%29):

> At this stage, I feel it is my duty to thank my teammates Joshua Peter Polaprayil, Opeoluwa Adeyeri, and Zain Ali for their successful work, professional solutions, and especially for their efforts to keep our system secure.

The comment goes on, with characteristic humour, to thank "our dear hackers :)" for "a free penetration testing experience" that made the system's security stronger. The live-attack hardening in the [security deep dive](docs/09-security-architecture.md) is otherwise my account; this is the founder's, in public, and it is what I'd point a sceptical reader to. Beyond security, the company exercised the whole system — the agent, its flows, the retrieval layer and the shipped features — and its feedback gave me a fast loop for finding and fixing problems.

**Production track record.** In production on two messaging channels from late 2025, with in-chat commerce live from Q1 2026 through to handover. My own test log with the production WhatsApp number starts on 16 November 2025 ([From the Test Logs](#from-the-test-logs)).

---

## My Role

I owned the full technical surface of the AI platform — as sole AI engineer and architect, alongside a separate in-house team responsible for the company's core backend and frontend. There is no layer of the agent platform I didn't design, build, break, and repair.

**AI & Machine Learning** — LangGraph agent architecture and state design; hybrid retrieval including fusion strategy, reranking, chunking and knowledge-base engineering; system prompt architecture and measured compression across iterative passes; explicit prompt caching; ONNX inference pipelines for reranking, injection classification and NER; an offline risk classifier with leakage-safe features and promotion gates; a self-hosted speech pipeline with benchmark-driven model selection; an LLM-as-judge harness for testing, model selection and production quality review.

**Full-Stack Engineering** — FastAPI async backend and API design; PostgreSQL schema design with time-ordered keys, migrations and pooling; Redis distributed locking, stream consumer groups, sliding-window rate limiting and Bloom filters; Qdrant collection management, hybrid search and multi-worker indexing coordination; Stripe integration across the full lifecycle; dual-channel Telegram and WhatsApp delivery with ten-locale internationalisation.

**Infrastructure & DevOps** — VPS provisioning and OS hardening from bare metal; Docker Compose orchestration and build-time model provisioning; resource modelling from direct measurement; Nginx reverse proxy, TLS, rate limiting and security headers; Cloudflare WAF, Turnstile, DNS and origin security; GitHub Actions CI/CD with artifact-consistent testing, registry publishing and hash-verified deployment; backup and restore tooling; load and adversarial testing; Prometheus, Grafana, Loki and LangSmith observability, with LangSmith tracing in place from day one.

**Security Engineering** — layered defence from edge to request path; behavioural abuse detection with cross-platform ban propagation; semantic injection defence validated under adversarial load; honeypots and timing analysis; validation as an abuse signal; dual-mode encryption with searchable lookup hashing; PII redaction that preserves product signal; incident analysis and iterative hardening; administrative tooling for non-technical operators.

---

## Contact

**Joshua Peter Polaprayil**\
Full-Stack AI Engineer · MSc Big Data Analytics & AI (ATU, Ireland)

- **LinkedIn** — [linkedin.com/in/josh33-peter10](https://www.linkedin.com/in/josh33-peter10/)
- **GitHub** — [github.com/JoshPola96](https://github.com/JoshPola96)
- **Email** — [josh19peter96@gmail.com](mailto:josh19peter96@gmail.com)

Open to AI/ML and full-stack AI engineering roles. If you're building production agentic systems and want someone who can own them from architecture through deployment, I'd like to hear from you.

---

## Repository Note

This repository contains **architectural documentation and engineering narrative only**. It includes no source code, credentials, customer data or confidential business data. The test-log exhibits are my own conversations with the agent, with codes, contact details and eSIM identifiers removed. Company and product names appear solely as factual work history. The security design is documented in depth deliberately, as a contribution to how conversational agents can be defended.

**MIT License** — see [LICENSE](LICENSE).

---

**Last updated:** November 2026\
**Status:** Handed over August 2026 — documented as of that date

<p align="center">
  <em>Built under constraint. Hardened under fire. Measured, not assumed.</em>
</p>
