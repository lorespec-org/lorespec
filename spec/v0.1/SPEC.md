# LoreSpec v0.1 — Specification

The structured, portable format for AI conversation outputs.

---

## 1. Overview

A LoreSpec Session Digest (`LORE.md`) is a structured extraction of the durable knowledge produced during an AI conversation. It transforms raw conversational data into reusable, searchable, connected knowledge objects.

Every digest has two layers:

- **Episodic Layer** — the story of the conversation (Session Arc)
- **Semantic Layer** — the extractable knowledge (Knowledge Objects)

Both are needed. Both must be linked. This mirrors the biological distinction between episodic and semantic memory (Tulving, 1972) — you need to remember both *what you learned* and *the experience of learning it* to evaluate whether the knowledge still holds.

---

## 2. File Format

A `LORE.md` file is a Markdown document with YAML frontmatter.

### 2.1 Frontmatter

```yaml
---
lorespec: "0.1"
id: "session-2026-03-29-auth-redesign"
date: "2026-03-29"
source: "claude"           # claude | chatgpt | gemini | other
topic: "One-sentence description of what the conversation was about"
tags: [authentication, product-architecture, stytch]
classification:
  type: strategy           # strategy | technical | research | drafting | operational | reflective
  secondary_type: technical # optional
  domains: [authentication, product-architecture]
  value: high              # high | medium | low | skip
trails: [auth-architecture, sannend-trust-model]
---
```

### 2.2 Body Structure

```markdown
## Session Arc

### Started
[Where the conversation began — the initial question or intent]

### Pivots
- [Key moment where thinking changed direction, and what triggered it]
- [Another pivot, if any]

### Ended
[Where the conversation landed — what was resolved, what's next]

## Artifacts
[ARTIFACT objects, or omit section if none]

## Decisions
[DECISION objects with full IBIS/Toulmin structure]

## Insights
[INSIGHT objects]

## Patterns
[PATTERN objects, or omit section if none]

## Open Questions
[OPEN_QUESTION objects, or omit section if none]

## References
[REFERENCE objects, or omit section if none]

## Next Steps
[NEXT_STEP objects]

## Solutions
[SOLUTION objects, or omit section if none]

## Connections
[Explicit links between objects — within this session and to objects from prior sessions]

## Trail Updates
[Which existing trails this session extends, and any new trails it creates]
```

---

## 3. Session Classification

Not all conversations are the same cognitive activity. Classification determines which extraction profile to apply.

### 3.1 Session Types

#### STRATEGY
Product thinking, business planning, brainstorming, positioning, competitive analysis, organizational decisions.

- **Profile:** Full extraction. All 8 object types active. Full IBIS/Toulmin structure on Decisions. Session Arc with pivots is critical.
- **Dominant objects:** Decision, Insight, Open Question, Pattern
- **Session Arc importance:** High

#### TECHNICAL
Coding, debugging, system design, DevOps, infrastructure, configuration.

- **Profile:** Emphasizes Pattern, Reference, and Solution. Decisions are lighter — architectural choices get full IBIS, but trivial code choices do not.
- **Dominant objects:** Pattern, Reference, Solution, Artifact
- **Session Arc importance:** Low (unless a debugging session reveals architectural insight)

#### RESEARCH
Learning, investigation, exploring a topic, competitive analysis, literature review.

- **Profile:** Emphasizes Insight and Reference. May produce a synthesis — a connected summary across multiple sources.
- **Dominant objects:** Insight, Reference, Open Question
- **Session Arc importance:** Medium

#### DRAFTING
Writing, editing, document creation, content iteration.

- **Profile:** Emphasizes a single Artifact with version evolution. Editorial Decisions as lightweight notes.
- **Dominant objects:** Artifact, lightweight Decision
- **Session Arc importance:** Low

#### OPERATIONAL
Quick lookups, syntax questions, format conversions, trivial tasks.

- **Profile:** Skip extraction. Not every conversation produces durable knowledge.
- **Dominant objects:** None
- **Session Arc importance:** None

#### REFLECTIVE
Personal thinking, career decisions, journaling-style conversations.

- **Profile:** Handles with care. Emphasizes Insight and Decision. Privacy stakes are higher — extract only what the person would want to see again.
- **Dominant objects:** Insight, Decision, Open Question
- **Session Arc importance:** High

### 3.2 Multi-Type Sessions

A single conversation can span multiple types. A debugging session that pivots into architecture that becomes a strategic decision is TECHNICAL + STRATEGY. The classifier identifies segments and applies the appropriate profile to each.

### 3.3 Knowledge Domains

Domains are user-defined tags that route objects to the right mental shelf. They emerge from the person's actual work, not from predetermined categories. Objects can belong to multiple domains.

---

## 4. Knowledge Object Types

### 4.1 Artifact

A tangible output produced during the conversation.

| Field | Type | Description |
|---|---|---|
| `title` | string | What is this thing |
| `type` | enum | doc, spec, code, plan, framework, template, analysis |
| `status` | enum | draft, final, abandoned |
| `version_note` | string | If iterations existed, what changed and why |
| `summary` | string | 2–3 sentence description |
| `links` | array | Related objects |

**Extraction signal:** The LLM produced a substantial block of content that was iterated on.

### 4.2 Decision

A choice that was made. Structured following IBIS (Rittel & Kunz, 1970) and Toulmin (1958).

| Field | Type | Description |
|---|---|---|
| `decision` | string | What was decided (one sentence) |
| `issue` | string | The question that prompted the decision |
| `positions` | array | Options that were on the table |
| `arguments` | array | Key arguments for and against each position |
| `warrant` | string | The unstated assumption connecting evidence to conclusion |
| `qualifier` | enum | always, usually, in this case, tentatively |
| `status` | enum | settled, provisional, revisited |
| `links` | array | What this supersedes, enables, or depends on |

**Extraction signal:** The conversation explored multiple options and converged on one.

The **warrant** field deserves special emphasis. A decision without the underlying assumption is just a fact. The warrant captures the belief that, if it changes, means the decision should be revisited. In empirical testing across 9 diverse conversations, the warrant was consistently the most valuable extracted element.

### 4.3 Insight

A fact, observation, or piece of understanding. Semantic knowledge — retrievable independent of the episode where it was learned.

| Field | Type | Description |
|---|---|---|
| `insight` | string | Standalone statement of what was learned |
| `source` | enum | research, discussion, discovery, analysis |
| `domain` | string | Knowledge area |
| `confidence` | enum | established, likely, speculative |
| `links` | array | Decisions it informs, patterns it supports |

**Extraction signal:** A factual claim or observation useful to recall later, independent of this conversation.

### 4.4 Pattern

A reusable method, framework, or mental model. Procedural knowledge (Anderson, 1983) — knowing *how*, not just knowing *that*.

| Field | Type | Description |
|---|---|---|
| `name` | string | Short name |
| `description` | string | What it is and when to use it |
| `steps_or_components` | array | The operational elements |
| `scope` | enum | universal, local |
| `origin` | enum | invented, referenced, evolved |
| `links` | array | Where applied, what it informed |

**Scope matters:** Universal patterns (e.g., "incentive-aligned process ownership") are intellectual capital transferable across domains. Local patterns (e.g., "PM2 state corruption recovery") are tribal knowledge specific to a stack or environment. Both are valuable but serve different retrieval contexts.

**Extraction signal:** A structured approach to a problem that could apply beyond this specific conversation.

### 4.5 Open Question

Something that came up but was never resolved.

| Field | Type | Description |
|---|---|---|
| `question` | string | The unresolved question |
| `context` | string | Why it matters |
| `partial_answers` | array | Progress made toward answering |
| `blocks` | array | What decisions or work this question blocks |
| `links` | array | Related decisions, insights, other questions |

**Extraction signal:** A question was raised, discussed, but the conversation moved on without resolution.

### 4.6 Reference

A tool, resource, person, company, or source that was discovered or mentioned.

| Field | Type | Description |
|---|---|---|
| `name` | string | What is it |
| `type` | enum | tool, company, person, article, repo, concept, framework |
| `relevance` | string | Why it came up and why it matters |
| `url` | string | Link if available |
| `links` | array | Related objects |

**Extraction signal:** A specific named thing that the person might want to look up again.

### 4.7 Next Step

A concrete action that emerged from the conversation.

| Field | Type | Description |
|---|---|---|
| `action` | string | What needs to be done |
| `why` | string | What prompted this |
| `depends_on` | array | Prerequisites |
| `urgency` | enum | now, soon, someday |
| `links` | array | The decision or insight that generated this |

**Extraction signal:** A commitment or clear next action — not a hypothetical, but something intended.

### 4.8 Solution

A specific fix to a specific problem. Distinct from Decision (a choice between options) and Pattern (a reusable method).

| Field | Type | Description |
|---|---|---|
| `problem` | string | What was broken or failing |
| `fix` | string | What specifically resolved it |
| `why_it_works` | string | The underlying explanation |
| `caveats` | string | Known limitations or edge cases |
| `links` | array | The insight that diagnosed it, patterns it generalizes into |

**Extraction signal:** A debugging or troubleshooting exchange that reached resolution with standalone value.

---

## 5. Connections

Connections are not metadata — they ARE the knowledge base. An isolated decision is a fact. A decision linked to the insights that informed it, the alternatives rejected, the open questions created, and the next steps implied — that's knowledge.

### 5.1 Connection Types

| Type | Meaning | Example |
|---|---|---|
| `led_to` | Causal/sequential | "This insight led to this decision" |
| `informed_by` | Evidential | "This decision was informed by this research" |
| `supersedes` | Versioning | "This decision replaces the March 3rd one" |
| `contradicts` | Tension | "This insight conflicts with that earlier one" |
| `related_to` | Associative | "Same domain, worth linking" |
| `depends_on` | Structural | "Can't proceed until this is resolved" |
| `instance_of` | Pattern application | "This decision applied that framework" |

### 5.2 Context Collapse

When semantic knowledge gets severed from its episodic origin, **context collapse** occurs. A fact like "we chose Postgres" without the link to the conversation where alternatives were weighed becomes unevaluable — you can't determine whether the decision still holds when circumstances change.

Every semantic object should be traceable back to the episodic experience that produced it. The bidirectional link between Knowledge Objects and Session Arc is the structural safeguard.

### 5.3 Trails

A trail is a named path through connected objects across multiple sessions. Trails emerge when objects from different conversations share connections.

Trails are Bush's associative paths (1945), Luhmann's branching card sequences, and Tulving's episodic threads linking semantic memories back to the experiences where they were formed.

**Format:**

```markdown
## Trail Updates
- **Auth Architecture** — extended with D1 (hybrid auth decision) and I2 (collector user archetype insight). Trail now spans sessions 129, 224, 338.
- **New trail: LoreSpec Product** — created. Contains D1-D4, I1-I3, A1 from this session.
```

---

## 6. Extraction Principles

These principles guide what gets extracted and how. They are grounded in the theoretical foundations but expressed as practical rules.

1. **Standalone clarity** — Every extracted object must make sense without reading the original conversation. Future-you has no memory of this chat.

2. **Connect before you collect** — After extracting an object, the first job is to link it. What does this relate to? What does it supersede? An unlinked object is a missed opportunity.

3. **Preserve the warrant** — A decision without the underlying assumption is just a fact. Capture the "because we believe that..." — the belief that, if it changes, invalidates the decision.

4. **Capture pivots as sensemaking** — Moments where thinking changed direction are among the most valuable things to extract. A pivot is not a mistake — it's evidence of active sensemaking.

5. **Both episodic and semantic** — The Session Arc preserves the experience. The knowledge objects extract the content. Link them.

6. **Version, don't duplicate** — If a decision was made in March and revised in April, link them with `supersedes`. Show both the current state and the history.

7. **Judgment over completeness** — Not everything is worth extracting. Extract what you'd want to find six weeks from now. A casual aside is not an insight. A hypothetical is not a decision.

8. **The network is the knowledge** — An isolated collection of objects is a filing cabinet. The knowledge lives in the connections between them.

9. **Guard against context collapse** — Every semantic object must maintain a traceable link back to its episodic source. A fact severed from the experience that produced it cannot be evaluated or updated.

10. **Treat strategic conversations as wicked problems** — Most AI conversations about product, strategy, or design have no definitive formulation, no stopping rule, and no true/false solutions. Capture the full argumentative structure, not just the conclusion.

11. **Right-size for retrieval** — Each knowledge object should work as a single embedding chunk for vector search. Target 150–800 tokens per object. An Insight that's a bare sentence (50 tokens) will retrieve poorly — add enough context (domain, why it matters, what it connects to) to make it a complete, searchable unit. A Decision that runs over 800 tokens should be tightened, not split — splitting breaks the argumentative structure. The "standalone clarity" principle and the chunking principle reinforce each other: an object that makes sense in isolation also embeds well in isolation.

    **Token guidelines by type:**

    | Type | Target range | Notes |
    |---|---|---|
    | Artifact | 150–400 | Summary + metadata, not the artifact itself |
    | Decision | 200–600 | Full IBIS/Toulmin structure — the richest objects |
    | Insight | 150–300 | Pad terse insights with domain context |
    | Pattern | 250–500 | Steps/components drive the length |
    | Open Question | 150–300 | Include what it blocks |
    | Reference | 100–200 | Shortest objects — relevance field is key |
    | Next Step | 100–250 | Include the "why" |
    | Solution | 200–500 | Problem + fix + explanation |
    | Session Arc | 300–600 | The episodic layer as one chunk |

---

## 7. Interoperability

### 7.1 Design for Ingestion

LoreSpec objects are designed to map cleanly into downstream knowledge systems. Each object is a self-contained unit that can be ingested as a single record — a thought in Open Brain, a note in Obsidian, a block in Notion, or an embedding in any vector database.

The key design constraints:

- **One object = one record.** Never split an object across multiple records. The fields within an object are semantically coupled — a Decision's warrant only makes sense next to its issue and positions.
- **Render as prose for embedding.** When ingesting into a vector store, flatten the object's structured fields into a natural-language paragraph. Structured YAML/Markdown is for human reading and programmatic parsing; prose is for embedding quality.
- **Preserve type information.** Downstream systems should know that a record is a Decision vs. an Insight. This enables filtered retrieval ("show me all decisions about auth") which pure semantic search alone cannot do reliably.
- **Connections travel with the object.** When ingesting an object, include its `links` field as metadata — even if the downstream system can't natively represent a graph, the link references enable reconstruction later.

### 7.2 Open Brain Mapping

LoreSpec objects map to Open Brain thought types:

| LoreSpec Type | Open Brain Type | Rationale |
|---|---|---|
| Artifact | observation | A record of what was produced |
| Decision | observation | A structured observation about a choice made |
| Insight | observation | A fact or understanding surfaced |
| Pattern | idea | A reusable method — procedural knowledge |
| Open Question | task | An unresolved item that needs future attention |
| Reference | reference | Direct mapping |
| Next Step | task | An action to be taken |
| Solution | observation | A record of what fixed a problem |
| Session Arc | observation | The episodic context for the session |

When rendering for Open Brain ingestion, prefix each thought with its LoreSpec type for retrievability:

```
[DECISION] Use buyer-request/seller-approve transfer model.
Issue: How should ownership transfer work at point of sale?
Positions considered: dealer-push, buyer-request, mark-as-sold.
Chose buyer-request because the party with the strongest
incentive should drive the process — post-sale, the buyer's
motivation far exceeds the seller's. Settled for Phase 1.
```

This renders as ~80 tokens — on the low end. A richer version with full arguments and links would land at 250–400, which is the sweet spot.

---

## 8. Validation

### Theoretical

This specification has been independently validated against primary academic sources by Google Deep Research. All 8 framework citations were confirmed as accurate and structurally sound. The episodic/semantic mapping faithfully operationalizes Tulving (1972), the connection architecture implements both Bush's associative trails and Luhmann's linking methodology, and the Decision structure correctly integrates IBIS and Toulmin.

### Empirical

The ontology was tested against 9 diverse conversations spanning product strategy, authentication redesign, competitive analysis, DevOps infrastructure, documentation tooling, museum trust architecture, and knowledge management ideation.

Findings:
- All 8 object types populated naturally across every domain
- The 8th type (Solution) emerged independently from technical sessions
- Session Classification emerged as consistent behavior from extraction principles alone
- The warrant field was consistently the most valuable extracted element
- 12+ named trails formed across sessions
- The ontology is domain-agnostic

---

## 8. Versioning

LoreSpec uses semantic versioning. The current version is **0.1** — the spec is stable enough to use but may evolve based on community feedback.

Breaking changes will increment the minor version during the 0.x phase. The `lorespec` field in frontmatter ensures parsers can handle version differences.

---

## References

- Ackoff, R.L. (1989). "From Data to Wisdom." *Journal of Applied Systems Analysis*, 16, 3–9.
- Anderson, J.R. (1983). *The Architecture of Cognition*. Harvard University Press.
- Bush, V. (1945). "As We May Think." *The Atlantic Monthly*, 176(1), 101–108.
- Kunz, W. & Rittel, H. (1970). "Issues as Elements of Information Systems." Working Paper 131, UC Berkeley.
- Luhmann, N. (1981). "Kommunikation mit Zettelkästen." In *Öffentliche Meinung und sozialer Wandel*, 222–228.
- Nonaka, I. & Takeuchi, H. (1995). *The Knowledge-Creating Company*. Oxford University Press.
- Toulmin, S. (1958). *The Uses of Argument*. Cambridge University Press.
- Tulving, E. (1972). "Episodic and Semantic Memory." In *Organization of Memory*, 381–403. Academic Press.
- Weick, K.E. (1995). *Sensemaking in Organizations*. Sage.
