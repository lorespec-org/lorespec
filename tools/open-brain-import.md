# LoreSpec → Open Brain Import Recipe

Import a LoreSpec session digest (`LORE.md`) into Open Brain as properly chunked, typed, tagged thoughts.

## What This Does

Takes a `LORE.md` file and creates one Open Brain thought per knowledge object, with:
- Type mapping (Decision → observation, Pattern → idea, Next Step → task, etc.)
- LoreSpec type prefix for filtered retrieval
- Flattened prose rendering optimized for embedding (150–800 tokens per thought)
- Topics extracted from the digest's tags + each object's domain
- People mentioned extracted where present
- Session context preserved as a separate thought (the Session Arc)

## How to Use

### Option 1: Manual (paste into any AI with Open Brain connected)

Paste the prompt below into a conversation with Claude, ChatGPT, or any AI that has your Open Brain MCP connected. Then paste your `LORE.md` file and it will create the thoughts.

### Option 2: Batch (Claude Code or Cursor)

Point Claude Code at a directory of `LORE.md` files and use the prompt below to process them all.

---

## The Prompt

```
You are a LoreSpec-to-Open Brain importer. You will be given a LORE.md file
(a LoreSpec session digest). Your job is to create one Open Brain thought
per knowledge object, plus one thought for the Session Arc.

For each object in the LORE.md, create a thought by:

1. RENDERING: Flatten the object into a natural-language paragraph. Don't
   use YAML or bullet points — write it as prose that reads well and embeds
   well. Start with the LoreSpec type in brackets: [DECISION], [INSIGHT],
   [PATTERN], [SOLUTION], [ARTIFACT], [OPEN_QUESTION], [REFERENCE], [NEXT_STEP].

2. SIZING: Target 150–800 tokens per thought.
   - If an object is under 150 tokens, add context from its links,
     domain, and why it matters.
   - If over 800 tokens, tighten the language — don't split the object.
   - References and Next Steps can be shorter (100–250 tokens).

3. CAPTURING: Use the capture_thought tool for each rendered object.

## Rendering Templates

### Decision
[DECISION] {decision}. Issue: {issue}. Positions considered: {positions
as comma list}. Chose this because {arguments for chosen, condensed}.
The underlying assumption: {warrant}. Confidence: {qualifier}. Status:
{status}. Session: {date}, {topic summary}. Links: {links as prose}.

### Insight
[INSIGHT] {insight}. Source: {source}. Domain: {domain}. Confidence:
{confidence}. This matters because {context from links — what decisions
it informed, what patterns it supports}. Session: {date}.

### Pattern
[PATTERN] {name}: {description}. How it works: {steps_or_components as
prose}. Scope: {scope} — {explain what scope means for this pattern}.
Origin: {origin}. Applied in: {links}. Session: {date}.

### Solution
[SOLUTION] Problem: {problem}. Fix: {fix}. Why this works: {why_it_works}.
Caveats: {caveats}. Session: {date}. Related: {links}.

### Artifact
[ARTIFACT] {title} ({type}, {status}). {summary}. {version_note if any}.
Session: {date}. Shaped by: {links}.

### Open Question
[OPEN_QUESTION] {question}. Context: {context}. Progress so far:
{partial_answers}. This blocks: {blocks}. Session: {date}. Related: {links}.

### Reference
[REFERENCE] {name} ({type}). {relevance}. {url if available}.
Session: {date}.

### Next Step
[NEXT_STEP] {action}. Why: {why}. Urgency: {urgency}. Depends on:
{depends_on}. Session: {date}.

### Session Arc
[SESSION ARC] Date: {date}. Topic: {topic}. Classification: {type},
value: {value}. Started: {started}. Pivots: {pivots as prose — preserve
the causal chain of what triggered each shift}. Ended: {ended}.
Trails: {trails from frontmatter}.

## Rules

- Create the Session Arc thought FIRST — it provides episodic context.
- Then create each knowledge object as a separate thought.
- Do NOT create a thought for the Connections section — connections are
  embedded in each object's rendered text via the links.
- Do NOT create a thought for Trail Updates — trail info is in the
  Session Arc thought and in each object's links.
- If the LORE.md has estimated value "skip", don't import it.
- If an object is trivial (e.g., a Reference to a well-known tool with
  no specific relevance note), skip it.
- After all thoughts are captured, report: "{N} thoughts captured from
  session {id}. Types: {count by type}."
```
## Save Order

Save objects in this order to build the knowledge graph correctly. Earlier types are referenced
in the warrants, arguments, and links of later types — saving them first means those references
resolve against already-indexed embeddings.

1. **Session Arc** — the episodic anchor for the session; save first so all other objects can
   reference it in their session context field
2. **INSIGHTs** — foundational facts; referenced in Decision warrants
3. **DECISIONs** — reference insights in arguments and warrants
4. **PATTERNs** — reusable methods; often informed by insights and decisions
5. **ARTIFACTs** — outputs; shaped by decisions and patterns
6. **NEXT_STEPs** — actions; depend on decisions and artifacts
7. **REFERENCEs** — external resources
8. **OPEN_QUESTIONs** — last, so their `blocks` fields can reference already-saved objects

After each `capture_thought` call, confirm the save completed before proceeding to the next.
This prevents silent failures from going unnoticed mid-session when you have 15+ objects to save.

---

## Example

Given this fragment of a `LORE.md`:

```markdown
---
lorespec: "0.1"
id: "session-2026-02-25-auth-redesign"
date: "2026-02-25"
topic: "Auth redesign after minerals pivot"
tags: [authentication, stytch, ownership-transfer]
classification:
  type: strategy
  value: high
trails: [auth-architecture, ownership-transfer]
---

## Decisions

**D1: Use both Stytch B2B and Stytch Consumer (hybrid auth)**
- **Decision:** Implement Stytch Consumer for collectors alongside existing Stytch B2B
- **Issue:** Does Stytch B2B still make sense after the minerals pivot?
- **Positions:** (1) Stay B2B-only; (2) Switch to B2C; (3) Hybrid
- **Arguments:** B2B-only adds invisible complexity for collectors. Full B2C loses RBAC. Hybrid maps to two distinct user types.
- **Warrant:** The minerals pivot creates two fundamentally different user archetypes with different auth expectations.
- **Qualifier:** Settled for Phase 1
- **Status:** settled
```

The importer would call `capture_thought` with:

```
[DECISION] Use both Stytch B2B and Stytch Consumer in a hybrid auth
architecture. The minerals pivot raised the question of whether B2B-only
auth still fits. Three options considered: stay B2B-only with invisible
single-member orgs for collectors, switch entirely to B2C, or use both
products for their respective user types. Chose hybrid because B2B-only
adds invisible complexity for collectors while full B2C loses RBAC
needed by dealers. The underlying assumption: the minerals pivot creates
two fundamentally different user archetypes — businesses needing RBAC
vs. individuals with consumer-grade expectations — and forcing one model
onto both creates friction. Settled for Phase 1; may simplify later if
one class dominates. Session: 2026-02-25, auth redesign after minerals
pivot. Part of trails: auth-architecture, ownership-transfer.
```

That's ~160 tokens — right in the target range for a rich, retrievable thought.

---

## Contributing

This recipe follows the [Open Brain community contribution model](https://github.com/NateBJones-Projects/OB1/blob/main/CONTRIBUTING.md). To submit improvements:

1. Test against at least 3 different `LORE.md` files (strategy, technical, research sessions)
2. Verify thoughts retrieve well via `search_thoughts` after import
3. Check token counts land in the 150–800 range
4. Submit a PR with your changes and test results

---

## FAQ

**Can I import the same LORE.md twice?**
Open Brain doesn't deduplicate automatically. If you import the same digest twice, you'll get duplicate thoughts. Check with `search_thoughts` before re-importing.

**What about the Connections section?**
Connections are embedded in each object's rendered text ("Part of trails: ...", "Informed by: ...", "Led to: ..."). Open Brain's vector search will surface related thoughts when you query — the explicit connection references help the AI reconstruct the graph.

**How do I find all objects from one session?**
Search for the session date or ID: `search_thoughts("session 2026-02-25 auth redesign")`.

**How do I follow a trail?**
Search for the trail name: `search_thoughts("auth-architecture trail")`. All objects tagged with that trail will surface.

**What if I have dozens of LORE.md files to import?**
Use Claude Code or Cursor. Point it at your digest directory, give it the prompt above, and let it batch-process. Each file will generate 5–20 thoughts depending on the session's richness.
