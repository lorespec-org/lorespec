# LoreSpec

**The Open Standard for AI Conversation Outputs**

A structured, portable format for extracting and preserving knowledge from AI conversations. Works with any LLM that can read a system prompt.

[SoulSpec](https://soulspec.org) defines who the agent is. LoreSpec defines what the conversation produced.

`SOUL.md` → `LORE.md`

---

## The Problem

Every day, millions of people have substantive AI conversations — making decisions, debugging systems, designing products, researching topics. Valuable knowledge is produced: decisions with rationale, insights, patterns, open questions, concrete next steps.

Then the conversation ends, and all of it vanishes into a chat log.

Chat is an interaction medium pretending to be a storage medium. The knowledge you generated is trapped in chronological transcripts with no structure, no searchability, and no connections to your other conversations. You can't ask "what did I decide about authentication last month?" and get a structured answer. You can't trace how your thinking on pricing evolved across six conversations. You can't hand your AI a map of everything you've figured out so far.

LoreSpec fixes this. It defines a structured format — `LORE.md` — for extracting the durable knowledge from any AI conversation and preserving it in a way that compounds over time.

## How It Works

After a conversation, an extractor (we call it the **Scribe**) reads the transcript and produces a structured Session Digest:

```
session-2026-03-29/
└── LORE.md          ← structured knowledge from one conversation
```

Each `LORE.md` contains two layers:

**The Episodic Layer** — what happened. Where the conversation started, how thinking evolved through pivots, where it landed. The story of the session.

**The Semantic Layer** — what was figured out. Structured knowledge objects extracted from the conversation, each designed to be retrievable and useful months or years later without re-reading the original transcript.

## The Ontology

LoreSpec defines 8 knowledge object types:

| Type | What it captures | Example |
|---|---|---|
| **Artifact** | Tangible outputs — docs, specs, code, plans | "Product spec v2 covering auth architecture" |
| **Decision** | Choices with full argumentative structure | "Use Postgres, not MongoDB, because..." |
| **Insight** | Facts and observations, context-free | "Collectors care about provenance post-sale, dealers don't" |
| **Pattern** | Reusable methods and mental models | "Incentive-aligned process ownership" |
| **Open Question** | Unresolved issues | "Should we gate sign-in before or after first document?" |
| **Reference** | Tools, resources, people discovered | "Neon — serverless Postgres with database branching" |
| **Next Step** | Concrete actions that emerged | "Register lorespec.org domain" |
| **Solution** | Problem → fix → why it works | "PM2 silently drops env vars when..." |

### Decisions Get Special Treatment

Most knowledge tools treat decisions as flat facts. LoreSpec captures the full argumentative structure, drawing from IBIS (Rittel & Kunz, 1970) and Toulmin (1958):

```yaml
decision: Use buyer-request/seller-approve transfer model
issue: How should ownership transfer work at point of sale?
positions:
  - Dealer-push — dealer enters buyer email
  - Buyer-request — buyer scans QR, requests transfer
  - Mark-as-sold with open claim
arguments:
  - Dealer-push requires real-time coordination
  - Buyer-request aligns with incentive asymmetry
warrant: The party with the strongest incentive should drive the process
qualifier: Settled for Phase 1
status: settled
```

The **warrant** — the unstated assumption connecting evidence to conclusion — is consistently the most valuable field. It tells future-you not just what was decided, but what belief would need to change to revisit the decision.

### Connections

Objects don't exist in isolation. LoreSpec defines 7 connection types that form a directed knowledge graph:

- **led_to** — causal/sequential
- **informed_by** — evidential
- **supersedes** — versioning
- **contradicts** — tension
- **related_to** — associative
- **depends_on** — structural
- **instance_of** — pattern application

### Trails

A trail is a named path through connected objects across multiple sessions. When conversations about the same topic happen weeks apart, trails link them into a coherent narrative of how your thinking evolved.

## Quick Start

### Option 1: Use the Scribe system prompt

Copy the [Scribe prompt](tools/scribe.md) into a Claude Project, ChatGPT custom instruction, or any LLM system prompt. Then paste a conversation export and ask it to produce a `LORE.md`.

### Option 2: Use the MCP server (coming soon)

```bash
npm install -g lore-mcp
```

### Option 3: Use the CLI (coming soon)

```bash
npm install -g lore-cli
lore extract conversation.json
```

## The Spec

The full specification is at [spec/v0.1/SPEC.md](spec/v0.1/SPEC.md). It covers:

- Session Classification (strategy, technical, research, drafting, operational, reflective)
- All 8 knowledge object types with fields and extraction signals
- Connection grammar and trail mechanics
- The Session Digest format
- 10 extraction principles grounded in academic research

## Theoretical Foundations

LoreSpec is grounded in 8 established frameworks from cognitive psychology, information science, and epistemology:

- **Tulving (1972)** — Episodic vs. semantic memory → the two-layer digest structure
- **Luhmann's Zettelkasten** — Atomicity and explicit linking → knowledge objects and connections
- **Bush's Memex (1945)** — Associative trails → cross-session trails
- **Rittel & Kunz IBIS (1970)** — Argumentative structure → Decision objects
- **Toulmin (1958)** — Warrants and qualifiers → the "why behind the why"
- **Ackoff's DIKW (1989)** — Information → knowledge transition → what the Scribe does
- **Weick's Sensemaking (1995)** — Pivots as cognitive evolution → Session Arc
- **Nonaka & Takeuchi SECI (1995)** — Externalization → Combination → the knowledge spiral
- **Anderson's ACT-R (1983)** — Declarative → procedural knowledge → Pattern objects

Full details with citations: [research/foundations.md](research/foundations.md)

## Ecosystem

| Component | What it does | Status |
|---|---|---|
| [LoreSpec](spec/v0.1/SPEC.md) | The standard — defines `LORE.md` | v0.1 |
| [Scribe](tools/scribe.md) | System prompt that extracts `LORE.md` from conversations | Ready |
| [Open Brain Import](tools/open-brain-import.md) | Import `LORE.md` into Open Brain as structured thoughts | Ready |
| Lore CLI | Process conversation exports → `LORE.md` | Planned |
| Lore MCP | Serve your lore library to any AI client | Planned |
| [lorespec.org](https://lorespec.org) | Landing page | Planned |

## Contributing

LoreSpec is an open standard. Contributions welcome:

- **Test the Scribe** against your own conversations and report what works/breaks
- **Propose new object types** backed by real extraction needs
- **Build integrations** — importers for Obsidian, Notion, Open Brain, etc.
- **Improve the spec** — file issues or submit PRs

## License

MIT — use it however you want.

---

*SoulSpec defines who the agent is. LoreSpec defines what the conversation produced.*
