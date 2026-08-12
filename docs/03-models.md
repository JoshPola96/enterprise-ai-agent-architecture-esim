[&larr; Back to the case study](../README.md)

# Model Selection, Temperature and Attention

*How the LLM was chosen and re-chosen, and the failure mode that traced back to sampling temperature.*

---

## The Model Selection Problem — and How Far It Got

The model this platform was built on is scheduled for retirement, which makes migration mandatory rather than optional. **It is still running in production and the successor is still undecided** — I've kept the reasoning here in full precisely because an open decision shows more than a closed one would.

### Why the obvious answer was wrong

The natural successor introduced a genuine regression for this workload: its reasoning mode inflated both latency and token cost even on trivial queries. A balance check shouldn't cost what a multi-step troubleshooting synthesis costs, and at launch it did.

The instinctive fix is tiered routing: cheap model for simple queries, expensive model for complex ones. Two things make that worse rather than better here.

**Intent detection is brittle at this surface area.** The agent spans discovery, authentication, purchase, provisioning, troubleshooting, and account management, in any language, across text, voice, and images. Hardcoded intent classification over that range will misroute — and a misroute doesn't degrade gracefully, it sends a transactional request to a model chosen for cheapness.

**A routing node costs more than it saves.** Making the decision with an LLM means a second inference call per turn, and the router either sees the full conversation context (in which case you've paid nearly the full price before the real call) or sees a truncated view (in which case the router and the agent are reasoning from different information — a split-brain where the routing decision is made on facts the executing agent doesn't have, or vice versa). Neither is acceptable in a flow that moves money.

### So I measured instead of arguing

Rather than choosing off release notes and benchmark blog posts, I built a harness that ran the **real integration suites** against every candidate: four models × three suites × two iterations — **24 runs, 193.8 minutes**, at identical temperature and timeout. It captured token usage, wall-clock latency per suite, pass rates, and cost from published pricing, and emitted a comparison report.

| | Incumbent (retiring) | Flagship | Flagship-Lite | Latest |
|---|---|---|---|---|
| Pass rate | 99.0% | 99.0% | 98.0% | **100.0%** |
| Avg latency/suite | 765s | 567s | **183s** | 423s |
| Cost, 24 runs | **$2.22** | $14.82 | $2.28 | $12.89 |
| Projected monthly @ 1k conversations/day | $680 | $4,536 | $698 | $3,947 |

### Where it stands — and why it's still open

Production still runs the incumbent. The migration is *scheduled*, not *decided*, and I'd rather show a live decision honestly than dress it up as a resolved one.

**What the evaluation settled: there is a safe landing.** The budget tier costs roughly the same as the incumbent, is by far the fastest of the four, and passes 98% of the suite. If the retirement deadline arrives with nothing better decided, that's the migration, and it's low-risk. Having a known-safe floor is most of what an evaluation is for.

**What it didn't settle: pass rate understated a real regression.** The budget tier has a characterised behavioural weakness — reasoning about *actions* rather than answers. Which tool to call, why, and what to do with the result. Under a tightly-specified prompt it leans toward literal script-following rather than judgement — and a sixteen-section instruction set actively encourages that reading.

That's the part a benchmark hides. The suite mostly asks for correct outcomes, and the model gets them; the deficit surfaces as an agent that executes the letter of the prompt in a situation that needed it to think. Two percentage points of pass rate is not a fair price tag for that, and I don't think any aggregate score would have caught it. I found it by reading transcripts.

**And then my own optimisation invalidated the cost column.** Every figure in that table was measured *before* the static prompt head and tool declarations moved into an explicit cache. So every model was being charged full price for ~15,000 tokens of unchanging prefix on every single turn — and the models with higher input pricing were penalised hardest by precisely the thing that is now cached.

The highest-capability model was the only one to pass 100%, and it's materially more token-efficient per unit of work. Re-priced with caching active, its cost disadvantage shrinks — plausibly far enough that capability wins. The comparison that would decide it is **cost per conversation with the cache active**, not cost per suite run without it. Nobody has that number yet, including me.

**So there are three live positions**, and the honest answer is that the deciding measurement hasn't been taken:

| Option | For | Against |
|---|---|---|
| Stay on the incumbent | Known, cheapest measured, in production now | Hard retirement deadline |
| Migrate to the budget tier | Same cost, fastest by a wide margin, lowest-risk swap | Script-following on action reasoning; the prompt work to recover it isn't scoped |
| Migrate to the top tier | Only model at 100%, best token efficiency, caching removes much of the cost gap | Still highest per-token; the re-priced comparison hasn't been run |

What I'd defend is the *shape* of the decision rather than an answer: establish a safe fallback first so the deadline stops being a risk, then take your time on the upgrade question — and re-measure when your own infrastructure changes the economics, because mine did.

### Two caveats I'd rather state than bury

The evaluator in the LLM-as-judge harness runs on the model under test — so each model partly grades itself. The pass rates are directionally useful, not independent measurements. And these are test-suite conditions, including judge calls production never makes, so the cost figures are *comparative*, not a production forecast.

### The transferable part

Three hours of compute answered a question that had been circling as an opinion for weeks, and it answered it against my own instinct — I'd assumed the newest model would win and had started planning around that. The suites already existed; the harness was a wrapper around them.

If you have an evaluation suite, you have a model-selection instrument. Most teams have the first and never build the second, and end up migrating on vibes and vendor changelogs. And the framing that matters isn't "which model is best" — it's "which model is best **at the volume I'll actually run, at the latency my users will actually feel, at a cost my product can actually carry**." Those three constraints picked a different model than raw capability would have.

---

## Temperature, Attention, and the Wrong Product ID

The most instructive bug in the project, because the obvious fix was wrong and the real fix was structural.

**The symptom.** The agent occasionally called the purchase tool with the wrong product identifier. In a system that moves money, that is as serious as bugs get — the user confirms one plan and a different one gets bought.

**The pattern.** It wasn't random. The agent disproportionately reached for the European promotional plan — the one product explicitly named in its instructions. Anything mentioned in the system prompt carries elevated attention weight, and when the model needed an identifier under uncertainty, the most *salient* one won over the correct one.

**The root cause.** Catalogue tools return large JSON payloads. Dozens of products, each with an identifier, name, quota, duration, price, region. Selecting one specific identifier out of that is a needle-in-a-haystack retrieval problem happening inside the model's attention rather than in code — and the haystack grows with catalogue size. Add a strongly-weighted candidate from the prompt and the failure mode is predictable in hindsight.

**The fix that didn't work for this bug — but solved a different one.** The instinctive lever is temperature. Google recommends around 1.0 for conversational agents, and there's a real reason for it: the conversational range is noticeably better, and the model handles ambiguous or awkwardly-phrased input more gracefully.

It also, at that setting, fabricates.

I caught this clearly during a long debugging session. Conversation history was being trimmed to a recent window, so in an extended exchange the model no longer had the earlier turns that actually contained the answer. Rather than recognising the gap, it selected a nearby datapoint still in context — often only tangentially relevant — and constructed a fluent, confident, factually unsupported explanation around it. The reasoning strings made it visible: the model reasoning forward from a fragment as though it were the answer, instead of noticing the data in hand didn't support a conclusion. Not hedging. Asserting.

Two things were compounding. A wide sampling distribution gives the model room to generate a plausible completion where a narrower one would stay closer to what's actually supported. And an aggressively trimmed history removes the grounding that would have contradicted it. Together: confident fabrication in exactly the long, multi-step sessions where accuracy matters most.

**Dropping to 0.1 fixed the fabrication.** Narrowing the distribution kept the model anchored to what the context genuinely supported, and the invented-explanation failure mode went away.

It did **not** fix the wrong product ID. That was the useful discovery — the two problems looked related and weren't. Fabrication was a sampling problem and moved with the temperature dial. Identifier selection was an *attention* problem — the right answer buried in a large payload, competing against a wrong answer with elevated weight from the prompt — and it didn't move at all.

There's an honest cost to running at 0.1. Conversational output is measurably stiffer, and the graceful handling of messy real-world phrasing that 1.0 gave up is a genuine loss. That trade is only acceptable because the structural fixes below let correctness stop depending on the dial at all.

**The lesson: temperature is one dial, and it only reaches one class of bug.** It governs how far the model will range beyond what the context supports — so it fixes fabrication. It does not govern which item the model picks out of a large payload, so it does nothing for selection. Diagnosing which kind of failure you have, before reaching for the setting everyone reaches for, is most of the work.

**The fix that worked was structural.** Two changes, neither of which relies on the model behaving well:

1. **Remove the ambiguity at the source.** The promotional product is resolved inside the tool rather than being named in the prompt for the model to pass through. The most-attended-to wrong answer stopped being an available answer.

2. **Validate identifiers at the tool boundary.** Product identifiers and their attributes are cached, and every purchase call verifies the identifier against that cache before execution. A mismatched or hallucinated ID is rejected deterministically rather than being trusted because it looked plausible.

Together those reduce the failure probability from *unlikely-but-nonzero* to *structurally impossible*, and they let temperature go back up to where conversational quality lives.

**The general shape of this.** Two distinct failure modes that present almost identically from the outside — a confidently wrong answer. One is the model ranging past what its context supports, which sampling temperature governs and history-trimming aggravates. The other is the model picking the wrong item from a large payload, which no prompt instruction and no sampling setting reliably fixes. For the second, the durable answer is always the same: shrink what the model has to select from, and validate the selection outside the model.

Which is exactly the governance argument again, in a non-security setting. A prompt instruction saying *use the correct product ID* is signage. A cache check that rejects a wrong one is a locked door.

---


---

[&larr; Back to the case study](../README.md)
