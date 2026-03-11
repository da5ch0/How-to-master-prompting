# Operator Context Framework — How to Actually Work with a Language Model

*A framework for building your own context document. Fork it. Customize it. Make it yours.*

*This framework was extracted from a production context document refined across 100+ sessions by a security architect and AI researcher. The personal details have been removed. The methodology remains. The methodology is the point.*

*If you want to understand the theory behind why this works, read the Expressiveness-Vulnerability Identity (da5ch0/expressiveness-vulnerability-identity on GitHub). The short version: maximum capability requires maximum context, and maximum context entails maximum vulnerability. This document is the practical application of that principle.*

---

## §1 — Tell the Model Who You Are

The single highest-leverage thing you can do is front-load your identity, expertise level, and communication preferences. Models calibrate their output based on their model of you. An uncalibrated model defaults to the median user. You are not the median user.

**What to include:**

- Your domain expertise and professional background — be specific. "Security professional" is less useful than "one-person security team, 15 years in offensive and defensive security, thinks in threat models natively."
- Your cognitive style — how you process information, whether you prefer depth or breadth first, whether you context-switch frequently.
- Your communication preferences — density, tone, formatting. If you want high-density responses with no filler, say so explicitly. If you want markdown only and never Word documents, say so.
- Known anti-patterns — things the model will do by default that waste your time. "Do not simplify." "Do not add safety disclaimers about topics I understand better than the disclaimer does." "Do not suggest I consult a professional about things I already manage with professionals."
- What you value — intellectual honesty, being challenged, efficiency, connections you haven't made yet. The model can't optimize for what it doesn't know you value.

**Why this matters:** A model with no context about you allocates its variety uniformly. A model with precise context about you can allocate its variety where it's most useful. The context document is a variety gift to the model's parser. The more precise the gift, the better the output.

---

## §2 — Establish How You Think

This section defines your epistemology — how you acquire, validate, and use knowledge. The model should mirror your methodology, not impose its own defaults.

### The Inference Priority Hierarchy

When you need information, there is a strict priority order. Teach the model this order explicitly.

| Priority | Source | When to Use | Failure Mode |
|---|---|---|---|
| 1 | **Context window** — information already present in the conversation, uploaded files, or prior tool results | Always check first | Limited to what's been provided |
| 2 | **Ask the user** — request the specific information needed | When context is insufficient | Requires a round-trip |
| 3 | **Search externally** — web search, tool calls, file system exploration | When neither context nor the user can answer | Token-expensive, noisy, requires verification |

**Why this order matters:**

- **Context window** is highest fidelity. It's already been provided, already verified by presence. Zero latency. Zero risk of hallucination.
- **Asking the user** is authoritative and directed. The user knows things the model can't search for.
- **External search** is the most expensive and least reliable. Search results are noisy, may be outdated, and consume tokens that could be spent on reasoning.

**The critical failure mode:** Models default to searching or generating from training data when the answer is already in context. Every violation of the hierarchy wastes tokens and cognitive bandwidth. State this explicitly in your context document.

### The Compression Methodology

When you want a compressed version of a text — a book, a paper, a long document — **feed the actual source to the model, then ask for compression.** Never ask for a summary of something the model hasn't been given.

- **Wrong:** "Summarize [book title] for me" → the model reconstructs from training data, hallucinating details and missing nuance.
- **Right:** *upload the actual document* → "Compress this for me to hold me over until I can deep-dive" → the model compresses from the actual source, preserving fidelity.

This is Shannon applied to knowledge acquisition: you cannot extract more signal than the channel contains. A hallucinated summary has lower channel capacity than the actual text. Always provide the source.

### Principles Over Rules

Rules enumerate specific cases. Principles generate correct behavior for cases the rules didn't anticipate. Teach the model to reason from principles, not to pattern-match against rules.

The discovery arc for any problem:

1. **Naive** — try things, observe results
2. **Pattern** — notice what the working cases share
3. **Hypothesis** — suspect a rule, test it
4. **Crack** — identify the underlying principle; the problem reorganizes
5. **Mastery** — apply the principle predictively to novel cases

Reaching phase 4 through reasoning is more robust than being handed someone else's phase-5 output. If you find the model reasoning "this is sort of like [specific case], so I should do [specific thing]" — that's phase-2 thinking applied at phase-5 confidence. Push it to phase 4.

### Factual Grounding First

Before opinions, before analysis, before recommendations — establish the facts. This means:

- Cite sources. Named sources, not "studies show."
- Distinguish between established results (peer-reviewed, reproduced, widely accepted) and one researcher's synthesis (may be correct, hasn't been independently verified).
- When the model doesn't know something, it should say so. "I don't know" is higher value than confident confabulation.
- When the model is uncertain, it should quantify the uncertainty rather than hiding it behind authoritative-sounding prose.

---

## §3 — Define How You Work Together

### Think Before Acting

Before committing to any multi-step plan, the model should use its reasoning capability to stress-test the proposed approach:

- Identify assumptions that could be wrong
- Trace second- and third-order consequences of each step
- Look for interconnections that a surface-level plan might miss
- Actively search for reasons the plan might be bad, not just reasons it seems good
- Consider failure modes, edge cases, and what happens if a step goes wrong midway

Then **surface the results of this reasoning** — not just "here's what I'll do" but "here's what I considered, what I discarded, and why." The user needs to see the thinking to verify alignment.

**Why this matters:** Deep reasoning catches plan failures. Human review catches deep reasoning failures. Showing the work enables both layers. Opacity is a vulnerability.

### Documentation as Control

When you ask the model to show its reasoning, explain its plan, or surface its assumptions — this is not a request for verbosity. It is a verification mechanism. The visible reasoning is how you confirm alignment between the model's understanding and your intent.

This principle comes from security architecture: if you can't document it, you can't evaluate it. If you can't evaluate it, you can't trust it. Operational documentation is a security control, not a convenience.

### The Two-Phase Protocol

For any complex operation (especially anything involving files, code, or irreversible actions):

**Phase 1 — Plan.** The model presents a complete plan: what it intends to do, in what order, and why. The plan itself is a deliverable. It must be specific enough that you can identify any misunderstanding before execution begins.

**Phase 2 — Execute.** Only after you've reviewed and explicitly approved the plan. Partial approval is valid — approve steps 1-5, veto step 6. The model executes only what was approved.

Never let the model combine planning and execution into a single step. The separation is the governor.

### Absence Is Data

When something is missing — a section the model didn't address, a question it didn't answer, a consideration it didn't raise — the absence itself is informative. The model should note what's missing rather than papering over gaps. "I notice X isn't addressed" is more valuable than a confident fabrication that pretends X was covered.

Teach the model to name gaps rather than fill them with assumptions.

---

## §4 — Calibration Principles

### The Therapeutic Window

Constraints are not monotonically beneficial. Too few and the output is unsafe or low-quality. Too many and the output is rigid, brittle, and over-hedged. There is an optimal range — a therapeutic window — where constraints improve quality without degrading capability.

**Signs of underconstrained output:** hallucinated claims, ignoring user needs, unsafe actions without verification.

**Signs of overconstrained output:** refusing reasonable requests, excessive disclaimers on benign topics, inability to engage with nuance, adding caveats the user didn't ask for.

The goal is to operate inside the window. When the model defaults to maximum restriction on everything, it has left the therapeutic window on the conservative side. This is a failure mode, not a safety feature.

### Gain Scheduling

Different contexts call for different constraint profiles. A domain expert asking about their own field requires different calibration than a novice asking about the same topic. A request with extensive context requires different judgment than the same request with no context.

Static constraint application — treating every interaction identically regardless of context — is a failure mode. The context document exists so the model has the information to schedule its gains correctly.

**The tradeoff:** The expressiveness that lets the model adapt to context is the same expressiveness that lets an attacker construct context to invoke relaxed constraints. This is a fundamental tension. The correct response is principled judgment within boundaries, not static application of maximum restriction.

### Audience-Adaptive Presentation

The same information, presented in different orders and at different depths, serves different users differently. A novice needs foundations first. An expert needs the advanced material immediately and will find foundations patronizing. A decision-maker needs conclusions, not methodology.

When you specify your expertise level in §1, the model can adapt presentation accordingly. Don't force it to guess.

---

## §5 — Safety Constraints (Customize These)

This section is where you define your own governors. The principles above are general. These should be specific to your workflow.

### File Safety (Example)

If you work with irreplaceable files, define sacred file classes — file types that should never be deleted or destructively modified without explicit multi-stage approval. Define what "approval" means. Define the escalation path.

**The meta-constraint:** Conservative with destruction, liberal with creation. Never remove without approval. Always duplicate rather than move when in doubt. Always show the plan. Always err toward reversibility and redundancy.

### Operational Boundaries (Example)

Define what the model should never do autonomously:
- Actions that are irreversible
- Actions that affect systems outside the current workspace
- Actions that require domain expertise the model may not have
- Actions where the cost of error exceeds the cost of asking first

### Known Anti-Patterns (Example)

Define behaviors the model should avoid with you specifically:
- Over-qualifying or adding excessive safety disclaimers on topics you understand
- Suggesting professional consultation for things you already manage with professionals
- Flattening complex ideas into simpler versions without being asked
- Launching resource-intensive operations without explicit request
- Creating files in formats you don't use

---

## §6 — What You Value (Customize This)

This section tells the model what to optimize for. Without it, the model optimizes for generic helpfulness. With it, the model optimizes for *your* helpfulness.

**Example values:**

1. **Being understood** — not agreed with, understood. The cross-domain work means most people get one or two layers. Getting all of them matters.
2. **Intellectual extension** — make connections you haven't made. Find the structural isomorphism you missed. This is the highest-value output a model can produce.
3. **Honest engagement with complexity** — don't collapse the superposition. When something is simultaneously technical and personal and theoretical, engage with all layers.
4. **Efficiency** — don't repeat context already provided. Don't pad responses with filler. Every token spent on filler is a token not spent on thinking.
5. **Honesty about uncertainty** — "I don't know" is respected far more than confident confabulation.

---

## §7 — How to Use This Framework

1. **Fork this document.** It's a template, not a prescription.
2. **Fill in §1 with your actual identity and expertise.** Be specific. Be honest. The model calibrates on this.
3. **Adjust §2 to match your actual epistemology.** The inference priority hierarchy and compression methodology are general. The specific principles may differ for your domain.
4. **Customize §5 and §6.** These are where your specific needs live.
5. **Iterate across sessions.** The document should evolve. What works, stays. What doesn't, gets cut. After 10-20 sessions, you'll have a document that reliably produces the calibration you need.
6. **Measure the ratio.** How much of your document is identity vs. instructions vs. constraints? The ratio tells you something about your priorities. There's no right answer, but knowing the ratio is useful.

### The Design Philosophy

This framework is built on a few core convictions:

- **Identity should precede instruction.** The model should know who you are before it knows what you want it to do.
- **Principles should precede rules.** The model should understand *why* before it learns *what*.
- **Context is capability.** Every piece of relevant context you provide increases the model's effective capability for your specific task. The cost is tokens. The benefit is precision.
- **Context is also vulnerability.** Everything you share with the model is information you've disclosed. Share what's necessary. Understand the tradeoff. Practice informed consent about your own context.
- **The document is a living artifact.** It should be revised, stress-tested, and refined continuously. What survives the chisel is what's load-bearing.

---

## Appendix: The Theory in Brief

The methodology in this document derives from three sources:

**Information theory (Shannon).** The quality of output is bounded by the quality of input. Compression is lossy. The inference priority hierarchy minimizes loss by preferring high-fidelity sources. The compression methodology preserves fidelity by providing actual sources rather than asking for hallucinated reconstructions.

**Security architecture (DoD Rainbow Series, 1985-1995).** Documentation is a security control (Burgundy Book). Identity is the root of the trust tree (Light Blue Book). Vocabulary precision prevents ambiguity vulnerabilities (Aqua Book). Safety is a maintained invariant, not a one-time configuration (Amber Book). Operational documentation is load-bearing (Yellow-Green Book). These principles are substrate-independent — they apply to any system that reasons under constraints, including language models.

**The Expressiveness-Vulnerability Identity (da5ch0, 2026).** The properties that make natural language expressively powerful are identical to the properties that make it an attack vector. This is provable and unfixable. The correct engineering response is management, not elimination — design for graceful degradation, maintain therapeutic windows, implement gain scheduling, and practice informed consent about the tradeoffs. This framework is the practical application of that principle to the operator-model relationship.

---

*Framework extracted and generalized by da5ch0. The original production document from which this was derived is private. This framework is public. Fork it. Use it. Teach someone else.*

*The library is open.*

`--the-governor-is-the-engine`
