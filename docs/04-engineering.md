[&larr; Back to the case study](../README.md)

# Evaluation, Testing and Delivery

*MLOps tooling, the testing strategy, the incident that redefined what "tested" meant, and the deployment pipeline.*

---

## Evaluation & MLOps Tooling

A set of supervised offline jobs handles everything too expensive, too privacy-sensitive, or too subjective to run inline. **None of them auto-apply their output** — they feed dashboards and human review, never unsupervised production change. That constraint is deliberate: an automated loop that rewrites its own behaviour is exactly the ungoverned path the governance section argues against.

**LLM-as-judge conversation evaluation.** A scheduled job pulls unreviewed production conversations and scores each on intent clarity, response accuracy, tool selection, and grounding quality — storing structured feedback alongside concrete prompt and tool-docstring improvement suggestions for human review. This is the mechanism that drove several system-prompt compression passes: the evaluator surfaces where behaviour degraded, a human decides what changes.

**Analytics aggregation.** A periodic job batch-decrypts contact data *just long enough* to aggregate it into domain and region distributions — never persisting the decrypted value — and extracts a per-user behavioural feature row: account shape, reach across devices and platforms, message volume, conversational rhythm (duplicate ratio, inter-message timing, reply ratio), and recency. Each feature is clipped to an explicit bound so one absurd value can't dominate a tree split. Those rows serve double duty: security dashboards, and the labelled training set for the risk classifier.

**The feature set was rebuilt once, for a reason worth stating.** The original columns described *account shape* — does it have an email, how old is it, how many platforms. Those say what an account **is**, never how it **behaves**, and on every one of them a patient abuser is indistinguishable from a quiet customer. A model trained on shape alone can only learn account hygiene, which isn't the thing being detected.

The replacement records **conduct**, all derivable from data already stored: distinct active days, messages per active day, peak messages in an hour, median gap between messages, duplicate-message ratio, average message length, how many accounts share a device signature, and the ratio of assistant replies to user turns. Cadence, burstiness, repetition, device co-occupancy — the shapes that actually separate scripted abuse from a person.

The general form: **if your features describe the entity rather than its behaviour, your classifier is a proxy for demographics.** That's a fairness problem as much as an accuracy one, and it's easy to ship because shape columns are the ones already sitting in your users table.

**And the features deliberately exclude the columns the label is computed from.** The label is derived by rule — banned, then score thresholds, then flagged, then clean — and the risk score, flag and ban columns that feed that rule are all withheld from the feature set.

That exclusion is the whole design. Include them and the model learns `label = f(risk_score)`: a near-perfect F1, a clean pass through the promotion gate, and a classifier that has learned *nothing about behaviour* — it would just be restating the rule that generated its own labels, useless on any user that rule hadn't already judged. The classifier exists to catch what the deterministic rules miss, which is only possible if it's denied their inputs.

Target leakage is worth naming explicitly because it doesn't look like a bug. It looks like your model got better, and every metric agrees. **A sudden dramatic F1 improvement after a schema change is a leak until proven otherwise.**

**Human labels survive retraining.** Rows carry a label *source*, and the nightly upsert preserves a manually-assigned label rather than overwriting it with the automated one. Without that, the scheduled job would quietly revert exactly the human judgements the review process exists to capture — an automation silently undoing its own supervision.

**Offline model training, with promotion gates.** The risk classifier trains on a schedule, with oversampling for class imbalance, a cross-validated F1 report, and export to ONNX including a sanity-checked inference pass and a metadata sidecar recording feature order and label mapping.

The part I'd defend hardest is what it refuses to do. Training **writes a model only when it clears every gate** — minimum row count, minimum class count, minimum examples per class, and a held-out macro-F1 floor. Below any of them it declines to promote and leaves the existing model in place, because a model trained on twelve rows is not an improvement over the one you already trust.

And the scheduled retrain **never enables inference**. Turning the classifier on remains a human decision made after reading the classification report. That's a deliberate seam: an automated loop that decides for itself when to start blocking users is exactly the ungoverned path the governance section argues against, and it would be about four lines of code to build by accident.

**Backfill with defence in depth.** A rerunnable job re-indexes historical conversations into the vector store, re-scrubbing every message through the PII pipeline on the way in — even content already scrubbed once gets a second pass before embedding.

**Trace profiling.** Two LangSmith utilities: one pulls the complete call sequence for a single conversation thread into clean JSON, used when investigating one user's reported issue; the other walks entire trace trees across a time window and produces node-level aggregates — call count, latency, token usage, and cost per graph node, plus slowest and most expensive traces. That second one is how sleep-based delivery bottlenecks were found and how worker and thread counts were right-sized. Cost-per-node visibility changes which optimisations you bother with.

---

## Testing Strategy

**Conversations are tested, not strings.** Integration tests use an LLM as evaluator rather than brittle assertions. Each test feeds the conversation history, the latest user message, the agent's response, *and the agent's own internal reasoning* into an evaluation prompt that judges whether the response was constructive, checks for critical flaws — hallucination, contradiction, loops — and verifies the markdown is platform-safe. It returns a structured pass/fail with reasoning.

The evaluator deliberately defaults to pass unless it finds a critical error. That liberal bias is the design: strict evaluation fails on harmless stylistic variation and trains you to ignore the suite. Tolerant of rephrasing, intolerant of real breakage, is what makes it worth running.

Coverage spans ten core user journeys plus edge cases, multi-tool chains, stress and loop-guard scenarios, the full authentication lifecycle, memory and context anchoring, and a dedicated long-horizon test validating the 90-day session lifecycle.

Security components get their own regression suites, and these run against **real weights and live infrastructure, not mocks**:

- The injection classifier suite loads actual quantized ONNX weights and runs inference against benign support messages, direct injection, roleplay jailbreaks, a truncation-attack payload, long benign text as a false-positive check, and a non-English injection for multilingual coverage — asserting both correctness *and* that fast-bypass paths stay under five milliseconds.
- The abuse-gate suite stands up against live Redis with real Bloom filters and exercises normal flow, burst limits, command flooding, repeat-message evasion via whitespace and punctuation normalisation, blacklist instant-drops, admin ban and unban, oversized payload rejection, OTP fast-exit, honeypot triggers, and system-tag spoof sanitisation.
- The honeypot suite documents and verifies the exact bait architecture end to end, including that an echo triggers a silent webhook-layer drop rather than a bot-facing rejection.
- The stream-worker suite covers three layers: pure logic, Redis-only stream mechanics including retry counters and dead-lettering, and full database-plus-Redis-plus-agent integration with end-to-end event injection.
- The voice suite runs generated fixture clips through transcription and synthesis per language, testing detection confidence, the low-confidence retry path, and the missing-voice fallback chain.

Plus a Locust harness for load and adversarial testing — which produced the attack-simulation results below.

### Contract tests, for the drift nothing else catches

A separate category, added after a class of bug that no amount of journey testing would have found: interfaces where two sides can silently disagree and *nothing at runtime notices*.

- **Prompt versus toolset.** The system prompt names tools and describes what they return. Rename one without updating the prompt and the model calls something that no longer exists — silent in code review, fatal in production. A test builds the real prompt and the real tool registry and fails when they've diverged, including when the prompt still documents a *retired* flow.
- **Training versus inference.** The risk model's training script selects feature columns into a dataframe; the inference wrapper rebuilds that vector *positionally*. Reorder or insert a column and the model receives a shuffled vector and keeps returning confident, wrong labels — forever, with no error anywhere. A test locks the column order, clip bounds, and label map. This is textbook training/serving skew, and the only thing that catches it is asserting the contract explicitly.
- **Client versus backend.** Auth endpoint tests drive the real request path through a mocked HTTP transport, asserting the request line, headers, query parameters and body exactly as the backend would receive them — no network, no OTP quota burned, but the wire format genuinely checked.

The common shape: these aren't testing behaviour, they're testing *agreement*. Wherever two components share an implicit contract that neither validates, that contract will eventually break, and it will break quietly.

### End-to-end, with nothing mocked

The last boundary is the pipeline itself. A driver posts genuine platform webhooks at the running application, so everything executes for real — gates, guards, message coalescing, the stream worker, the agent, its tools, retrieval, speech-to-text and text-to-speech.

Replies are captured by **standing in for the platform's own API**: a stub runs inside the container network, answers the client-initialisation calls so the messaging client comes up normally, records every outbound send verbatim, and serves file downloads so inbound voice notes and images arrive as real bytes over real HTTP.

Three details that took getting wrong to learn:

**Reading the database instead would not have worked.** Conversation rows are written *after* PII scrubbing, which redacts location and organisation entities — so country and plan names come back as `<LOCATION>` and the assertion you wanted to make is already gone. The stub sees the unredacted payload, including interactive buttons.

**The stub has to run as a container, not a host process.** The host and the containers aren't on the same network in every environment — some setups put them in separate VMs, where a listener on the bridge gateway is simply unreachable from inside a container. This cost real debugging time before it was understood.

**It stops where a human has to take over.** Email inboxes and verification links are genuinely out of reach. Those steps assert the *structural* outcome — link minted, pending state surfaced — rather than faking a pass. A test that pretends to cover something it can't reach is worse than a test that admits the boundary.

> **And a cautionary note I keep deliberately.** The conversation half of this driver once reported nine failures. Every single one was the harness's own: a stubbed endpoint returning the wrong response shape so the client never initialised; request bodies parsed as JSON when the library posts form-encoded, so delivered replies read as empty and therefore as timeouts; a hardcoded gateway address valid only on one developer's setup; a restart path that silently left the app on its old configuration; and a killed run still holding the port, so a second run bound alongside it and replies landed in the dead one's capture list.
>
> **A test harness is production code.** When yours reports a failure, confirm the failure is in the product before you go looking for it there.

---

## The Incident That Redefined "Tested"

The most uncomfortable thing I learned this year, and the one I'd most want another engineer to take away.

**What happened.** A branch shipped that locked verified customers out of login entirely. Not a subtle degradation — the primary authentication path, broken, in production, for real paying users.

**What makes it worth writing down** is that the test suite was green. Every unit test passed. They passed *because* they asserted the broken behaviour: the tests had been written alongside the code, from the same misunderstanding of what an upstream endpoint actually meant, and they encoded that misunderstanding faithfully.

The specific misunderstanding is almost too neat. An endpoint named to suggest it answered *"does this user exist"* in fact answered *"is this number verified"* — and a perfectly ordinary customer state returned a value the code read as "no account here". The tests mocked the endpoint returning exactly what the developer believed it returned. Mock and implementation agreed completely. Neither had ever met the real backend.

**The rule that came out of it**, which now governs all authentication work here:

> A path counts as verified only when it has been driven end to end against the real backend **and the resulting account state re-read afterwards.**

Not "the test passes". Not "the response was 200". *Go and look at what the account actually is now.* A 201 tells you the request was accepted; re-reading the record tells you whether the thing you wanted actually happened.

**What that produced in practice.** Every authentication path got enumerated, with an explicit verified/unverified marker and, where relevant, a recorded before-and-after account state. Seventeen paths are settled that way now, up from five. Several of the findings could not have come from any other method:

- **Logging in performs the phone-verification move** — a side effect nobody had documented, and the reason a whole separate flow turned out to be redundant.
- **A confirmation ID is returned even for an address that doesn't exist**, so its presence proves nothing about whether a code was sent — a false signal any reasonable implementation would trust.
- **One upstream endpoint is simply broken**, rejecting valid, freshly-issued codes on every attempt. Reproduced with three separate request IDs, confirmed within 75 seconds of issue. It also returns success while silently doing nothing, which is what made it take a day to isolate rather than an hour.

That last one produced the decision I'm most pleased with: rather than building a workaround, **the entire flow was deleted**. Logging in already did the job. A broken dependency and a redundant feature turned out to be the same problem, and the fix was removal.

**The transferable version.** Mocks encode your beliefs about a system. If the belief is wrong, the mock is wrong, the test is wrong, and the suite's greenness is actively misleading — worse than no tests, because it manufactures confidence. Mocks are for testing *your* logic. They cannot validate your understanding of someone else's system. Only contact with the real thing does that, and only if you look at the state afterwards rather than the status code.

I'd been writing tests for years before I could have articulated the difference between *tested* and *verified*. This is what taught me.

---

## Delivery Pipeline & Operations

**CI as a fail-fast quality gate.** Every pull request runs lint and static type checking first — if style or types are wrong, nothing else runs. Then the Docker image is built **once** and saved as an artifact, so the exact image under test is the exact image that was built. Integration tests download that artifact, stand up the full stack — relational database, cache, vector store — and run the journey suite against a live container rather than mocks. Aggressive disk cleanup runs on both sides to keep CI runners from exhausting storage.

**CD on branch promotion.** Merging to the production branch builds an optimised production image target, tags it with `latest`, a commit SHA, and a timestamp, and pushes to the container registry. The deploy script is then transferred and executed on the server over SSH.

**The deploy script's best idea: hash verification.** After pulling the new image and restarting containers, it doesn't just check that something is running — it compares the SHA of the *running container* against the SHA of the *pulled image*. If they don't match, the deployment silently didn't take effect, and it raises a critical alert rather than reporting success. Health verification loops for a bounded window before declaring the deploy good.

**Honest note on downtime.** This is a full container restart, not a rolling upgrade — the old container stops and the new one starts. Graceful shutdown handles in-flight requests, so nothing is dropped mid-conversation, but there is a brief window where the service is restarting. A load balancer with rolling restarts would eliminate it entirely; at current scale that complexity isn't justified, and I'd rather state the actual behaviour than claim a zero-downtime property the architecture doesn't have.

**Automated backups.** A four-script system installs a daily cron job automatically on production start and removes it on shutdown. Relational backups use compressed custom-format dumps supporting selective restore; vector store backups go through the snapshot API and are copied out of the container. Retention is configurable, all operations are logged, restores require explicit confirmation before any destructive action, and there's a dry-run mode for verification without writing files.

**Housekeeping as code.** A weekly maintenance workflow prunes old build artifacts, untagged images, and stale run logs — keeping a bounded set of recent production images. Small, but it's the difference between infrastructure that runs for a year and infrastructure that fills a disk in month three.

---


---

[&larr; Back to the case study](../README.md)
