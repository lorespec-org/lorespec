# Theoretical Foundations

LoreSpec is grounded in 8 established frameworks from cognitive psychology, information science, and epistemology. Each framework maps to a specific design decision in the specification. All citations have been independently validated against primary sources.

---

## 1. Tulving's Episodic/Semantic Memory (1972)

**The framework:** Human memory divides into two systems. Episodic memory stores experiences — events bound to time and place. Semantic memory stores facts and concepts — knowledge independent of when or where it was learned.

**How LoreSpec uses it:** The Session Digest has two layers. The Session Arc is episodic — it captures the experience of the conversation (started here, pivoted there, ended here). The Knowledge Objects are semantic — facts, decisions, patterns that are retrievable independent of the conversation that produced them.

**The key insight:** The Scribe computationally replicates **semanticization** — the biological process by which the brain gradually converts episodic memories into context-free semantic knowledge. In human cognition, this requires repeated exposure, sleep cycles, and substantial time as neural dependence shifts from hippocampus to neocortex. The Scribe performs this conversion instantly at the end of every conversation.

**The risk it addresses:** **Context collapse** — when semantic knowledge gets severed from its episodic origin. A decision extracted without its Session Arc context becomes unevaluable. The bidirectional link between objects and their Session Arc is the structural safeguard.

> Tulving, E. (1972). "Episodic and Semantic Memory." In *Organization of Memory*, pp. 381–403. Academic Press.

---

## 2. Luhmann's Zettelkasten (1952–1998)

**The framework:** Niklas Luhmann's slip-box system produced 70+ books and 400+ articles over 40 years. Three principles: atomicity (one idea per note), unique addressing (every note has an ID), and explicit linking (connections are first-class, not an afterthought). The value scales with the density of connections, not the number of notes.

**How LoreSpec uses it:** Each Knowledge Object is one idea (atomicity). Objects have IDs within sessions (addressing). The 7 connection types and trail system implement explicit linking. "Connecting, not collecting, is the priority."

**The key insight:** Luhmann's system was fundamentally bottom-up. The network topology emerges from the links, not from predetermined categories. LoreSpec's domain tags are user-defined and emergent, not a fixed taxonomy.

> Luhmann, N. (1981). "Kommunikation mit Zettelkästen." In *Öffentliche Meinung und sozialer Wandel*, pp. 222–228.

---

## 3. Bush's Memex (1945)

**The framework:** Vannevar Bush envisioned the Memex — a device where individuals store their books, records, and communications, and which can be consulted with speed and flexibility. The critical innovation was **associative trails** — named paths through linked information, retrieved by association rather than hierarchy.

**How LoreSpec uses it:** Trails are named paths through connected objects across multiple sessions. When conversations about the same topic happen weeks apart, trails link them into a coherent narrative. Bush predicted a new profession of "trail blazers" whose value would be discovering useful paths through accumulated knowledge. The Scribe is this trail blazer, automated.

> "The human mind operates by association. With one item in its grasp, it snaps instantly to the next."

> Bush, V. (1945). "As We May Think." *The Atlantic Monthly*, 176(1), 101–108.

---

## 4. IBIS (Rittel & Kunz, 1970) + Toulmin (1958)

**The framework:** IBIS (Issue-Based Information System) structures argumentative discourse: issues spawn positions, positions have arguments for and against. Toulmin's model adds warrants (the unstated assumptions connecting evidence to conclusion) and qualifiers (scope/confidence).

**How LoreSpec uses it:** The Decision object type captures the full argumentative structure — issue, positions, arguments, warrant, qualifier, status. This preserves not just what was decided but the reasoning that makes the decision evaluable, auditable, and revisable.

**The key insight:** AI conversations about strategy and product design are explorations of **wicked problems** (Rittel & Webber, 1973) — complex issues with no definitive formulation, no stopping rule, and no true/false solutions. Design rationale capture has been recognized as valuable for 50+ years, but the cost was historically prohibitive. The LLM's presence in the conversation drops the marginal cost to near zero — a technological inflection point.

**The warrant field** is consistently the most valuable element in empirical testing. It captures the belief that, if it changes, invalidates the entire decision.

> Kunz, W. & Rittel, H. (1970). "Issues as Elements of Information Systems." Working Paper 131, UC Berkeley.
> Toulmin, S. (1958). *The Uses of Argument*. Cambridge University Press.

---

## 5. Ackoff's DIKW Hierarchy (1989)

**The framework:** Data → Information → Knowledge → Wisdom. The transitions are functional, not structural — information is inferred from data, knowledge is compiled from information.

**How LoreSpec uses it:** The Scribe operates at the information → knowledge transition. Raw chat is data. Session Digests are information (contextualized, answering who/what/when). Cross-session linking, deduplication, and pattern recognition produce knowledge (procedural know-how).

> Ackoff, R.L. (1989). "From Data to Wisdom." *Journal of Applied Systems Analysis*, 16, 3–9.

---

## 6. Weick's Sensemaking (1995)

**The framework:** Sensemaking is "the structuring of the unknown." People don't discover pre-existing meaning — they actively construct it through retrospective attention to cues. "How can I know what I think until I see what I say?"

**How LoreSpec uses it:** AI conversations ARE sensemaking processes. The Session Arc captures both outputs (what was figured out) and process (how thinking evolved through pivots and cues). Pivots are not failures — they are evidence of active sensemaking.

> Weick, K.E. (1995). *Sensemaking in Organizations*. Sage.

---

## 7. Nonaka & Takeuchi's SECI Model (1995)

**The framework:** Knowledge creation follows a spiral: Socialization (tacit → tacit), Externalization (tacit → explicit), Combination (explicit → explicit), Internalization (explicit → tacit).

**How LoreSpec uses it:** AI conversations are Externalization engines — when people articulate half-formed ideas through dialogue with an AI, they crystallize tacit intuition into explicit knowledge. The Scribe performs Combination — linking new explicit knowledge to existing explicit knowledge. Without the Scribe, the knowledge spiral breaks. Externalized knowledge remains trapped as disjointed temporal data, never systematically combined with prior knowledge.

> Nonaka, I. & Takeuchi, H. (1995). *The Knowledge-Creating Company*. Oxford University Press.

---

## 8. Anderson's ACT-R (1976, 1983)

**The framework:** Human knowledge divides into two irreducible representations: declarative (facts/chunks) and procedural (production rules). All knowledge begins as declarative; procedural knowledge is compiled through practice and inference.

**How LoreSpec uses it:** The Pattern object type captures the critical transition point where declarative knowledge crystallizes into procedural knowledge — when a conversation produces not just a fact but a method. The scope field (universal vs. local) further distinguishes intellectual capital from tribal knowledge.

> Anderson, J.R. (1983). *The Architecture of Cognition*. Harvard University Press.

---

## Validation

This theoretical grounding has been independently validated by Google Deep Research against primary academic sources. The validation confirmed that all citations are accurate and that the specification is "not merely decorated with superficial academic citations, but profoundly, accurately, and rigorously structured upon the bedrock principles of cognitive psychology, information science, and epistemology."

The specification was also empirically validated against 9 diverse conversations spanning product strategy, authentication redesign, competitive analysis, DevOps infrastructure, documentation tooling, museum trust architecture, and knowledge management — confirming that all 8 object types populate naturally across every domain tested.
