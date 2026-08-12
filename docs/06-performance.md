[&larr; Back to the case study](../README.md)

# Performance and Economics

*Measured latency across the request path, and what it costs to run the whole thing for under $45 a month.*

---

## Performance

### A note on provenance

Numbers in engineering write-ups are usually presented without saying where they came from. That makes them useless. So, explicitly:

**Benchmark figures below were measured on a development machine with GPU disabled**, forcing the same CPU-only code path production executes. They are single-component measurements in isolation: directionally honest about *relative* cost, and not production-equivalent. Resist the temptation to bridge them to production with an assumed hardware factor — I tried that, and it was actively misleading. The two sets answer different questions and neither converts into the other.

**The load test predates the multimodal work.** It was run against a lighter stack, before the speech models were added to each worker.

**Memory figures transfer between machines. Latency figures do not.** RSS is architecture-independent; wall-clock is not. Everything below is labelled accordingly.

Production end-to-end times remain **higher** than the bench figures, for reasons the sections after this one work through in detail — that gap is the point of separating them, not an inconsistency to explain away.

### Measured on production (7-day rolling averages)

| Path | Observed |
|---|---|
| End-to-end turn | **P50 9.3s** · P90 20.7s · P95 29.7s · max 42.5s |
| Voice round-trip | **P50 7.7s** · max 28.9s |
| Image turn | P50 10.2s · max 15.6s |
| Conversation depth | avg 10.7 messages/thread · P50 8 · P90 30 |

Where the time actually goes, per component:

| Component | Average |
|---|---|
| LLM inference | **4.51s** |
| All tool execution in a turn, combined | 0.50s |
| Agent framework routing | 0.01s |

Three readings drive current work:

**The model dominates and the framework is free.** Ten milliseconds of graph routing across an entire turn means orchestration overhead isn't worth optimising, and half a second of combined tool execution isn't the problem either. Latency here *is* LLM latency. That's worth knowing before anyone spends a week tuning an agent framework.

**Output tokens dominate more than input tokens.** An inventory call returning twenty items adds several thousand input tokens, but the expensive part is that the model then *generates* a formatted summary of all of it — and generation is what costs wall-clock. So the leverage is in trimming what tools return before the model sees it, not in shrinking the prompt. This is the highest-value optimisation still outstanding and it is not done.

**Deep sessions were the entire tail.** The history window was cut specifically to stop long debugging sessions dragging enormous payloads into every subsequent turn, which is what the P90 was made of. The fix was a config change found by reading traces, not an architectural one.

For contrast, the same paths before the voice optimisation round ran at 24–28s end to end with transcription alone costing 11–14s. The [round-two section](02-platform.md#round-two-what-production-revealed-that-the-benchmark-couldnt) covers what changed.

### Measured on bench (CPU-only, GPU disabled)

| Metric | Value |
|---|---|
| Tool execution accuracy | >99% |
| Autonomous resolution | 95%+ |
| Webhook ingest | median 5ms · p95 9ms · p99 14ms |
| Injection classification | p50 24ms · p95 32ms |
| Concurrent users (multi-worker) | 125 – 250 |
| Hardware | CPU-only, 8 cores |

**Stage-level costs** — bench, indicative only:

| Stage | Typical |
|---|---|
| Cache hit | 2–5ms |
| Relational query | 20–50ms |
| Vector search | 50–100ms |
| Sparse retrieval | 30–80ms |
| RRF fusion | 10–20ms |
| Injection classification | ~24ms |
| Cross-encoder rerank (CPU) | 150–350ms |
| LLM inference (small payload) | 250–800ms |
| External API | 100–500ms |

### Adversarial load test (bench, pre-multimodal)

Sixty seconds of mixed traffic against the webhook endpoint: **30 concurrent attackers** sending payloads designed to trigger the CPU-bound injection classifier, alongside legitimate full-pipeline conversations running LLM inference and retrieval.

| Traffic | Success | RPS | Avg | p95 |
|---|---|---|---|---|
| Legitimate (full pipeline) | **100%** | 0.23 | 95ms | 391ms |
| Malicious (guard-blocked) | **100%** (19,164) | **319.40** | 84ms | 123ms |

**The finding that mattered:** the quantized injection classifier sustained **over 319 malicious evaluations per second at p95 under 125ms** on eight CPU cores — while the legitimate message queue held **100% success throughout the attack**. The guard layer absorbs sustained attack volume without measurably degrading real users, which is the point of putting it inline rather than out of band.

Memory held around 3 GB during the sustained attack.

The same test validated thread-contention tuning: worker count at half the core count leaves headroom for background inference threads rather than having event loops and ONNX threads fight for the same cores.

**Caveat, stated plainly:** this ran on the bench host, and before the speech models were resident in every worker. The guard layer's *relative* resilience is the durable finding — that it absorbs sustained attack volume without degrading real users. The absolute RPS figure belongs to that host and that stack, and shouldn't be quoted as a production capacity number. It needs re-running against the current stack.

The reranker and the model dominate. Everything else was optimised until it stopped mattering.

---

## Economics

The whole system runs for less than a streaming subscription.

| Component | Monthly |
|---|---|
| VPS hosting | ~$6–15 |
| LLM API | ~$5–20 |
| WhatsApp messaging | ~$0–10 |
| Telegram · TLS · CI/CD | $0 |
| Domain (amortised) | ~$1–2 |
| **Total** | **~$11–45** |

For comparison, at equivalent capability:

| Approach | Monthly |
|---|---|
| **This system** | **$25–40** |
| Managed cloud equivalent (compute, managed DB, managed cache, managed vector) | $300–500+ |
| Frontier-model API + hosted vector DB | $200–400 |
| Human support team it replaces | $3,000–5,000 |

That last row is the one that matters commercially — roughly a **99% cost reduction** against the staffing it displaces, with immediate payback. The self-hosted speech stack matters here too: per-minute STT and TTS billing would have been the single fastest-growing line item as voice adoption increased, and it's $0.

The engineering point underneath the economics: none of this required exotic optimisation. It required refusing to reach for a managed service every time something got slightly hard, and being willing to quantize, cache, and measure instead.

---


---

[&larr; Back to the case study](../README.md)
