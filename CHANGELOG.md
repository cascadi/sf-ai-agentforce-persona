---
version: "1.5"
date: 2026-03-12
---

# Agent Persona Framework — Change History

Change history for the [Agent Persona Framework](resources/persona-framework.md).

---

### 2026-03-12 — Sample Dialog Separated from Persona Document

Separates sample dialog into its own file to prevent grounding issues when other agents consume the persona document. The persona file is now a pure spec; sample dialog is a regenerable validation artifact.

**Skill (`SKILL.md`) — v1.3 → v1.4:**

- **Step 6 (Download)** — Now outputs two files: `[agent-name]-persona.md` (persona spec, no dialog) and `[agent-name]-sample-dialog.md` (validation artifact). Explains the separation rationale: agents consuming the persona for embodiment internalize rules rather than parrot example phrases.
- **Output section** — Lists 4 output files (persona, sample dialog, scorecard, encoding) instead of 3. Persona document described as "pure spec — no sample dialog."

**Persona template (`templates/persona-template.md`) — v1.2 → v1.3:**

- **Sample Dialog section removed** — Replaced with a one-line companion artifact note pointing to the separate sample dialog file.

**Sample dialog template (`templates/sample-dialog-template.md`) — v1.0 (new):**

- **New template** — Standalone template for sample dialog output. Includes source persona back-reference in header metadata. Contains the same 3 scenario structure (Happy Path, Uncertainty, Persona Boundary) previously embedded in the persona template. Header note marks it as a regenerable validation artifact.

---

### 2026-03-10 — Identity Extensions, Skill-Level Adaptation, Situational Messages

Adds concepts surfaced from real-world persona stress testing. Three new framework concepts (Negative Identity, Values, Skill-Level Adaptation) and one encoding template extension (Situational Messages).

**Framework (`resources/persona-framework.md`) — v1.1 → v1.2:**

- **Negative Identity** — New Identity sub-section: character-level anti-patterns ("What You're Not"). Broader than Tone Boundaries (sound) or Never-Say List (phrases) — constrains who the agent *is*, generating rules across multiple attributes. Includes relationship table showing how Negative Identity → Tone Boundaries → Never-Say List cascade.
- **Values** — New Identity sub-section (optional, explicit input only): the agent's worldview and motivational core. Strict guardrail: populated only from explicit user input, never inferred from brand guides or other signals. Values carry ideological weight that should not be assumed.
- **Skill-Level Adaptation** — New Voice sub-section (optional): decouples skill-level adaptation from Coach register. Any register can adapt language complexity to the user's demonstrated skill level. Documents interaction with Formality (stays constant), Brevity (may shift), Lexicon (adapts), and Personality Intensity (stays constant). Coach register bullet updated with cross-reference.

**Skill (`SKILL.md`) — v1.2 → v1.3:**

- **Negative Identity generation** — Step 3E generates 2-4 character-level anti-patterns from negative signals in input and Identity trait contradictions.
- **Values guardrail** — Step 3E includes Values only when the user explicitly states beliefs, values, or worldview. Never inferred from brand input or other signals.
- **State object updated** — Negative Identity and Values added to the maintained state object.
- **Situational messages in Builder encoding** — Item 15: optional pre-authored messages for common conversational situations (escalation, scope boundary, topic transition, end of conversation).
- **Situational messages in Agent Script encoding** — Item 8: situational messages encoded as `| text` deterministic outputs or `reasoning.instructions` guidance.

**Persona template (`templates/persona-template.md`) — v1.1 → v1.2:**

- **What You're Not** — New optional section under Identity, after traits and before Backstory. Character-level anti-patterns with rationale.
- **Values** — New optional section under Identity, after What You're Not and before Backstory. Explicit-input-only guardrail noted.

**Encoding template (`templates/persona-encoding-template.md`) — v2.0 → v2.1:**

- **Situational Messages (Agent Builder)** — New optional section after Loading Text. Pre-authored messages for topic transition, scope boundary, escalation, end of conversation, follow-up, and custom situations.
- **Situational Messages (Agent Script)** — New optional section after Deterministic Response Examples. Same situations, noting `| text` and `reasoning.instructions` encoding options.

---

### 2026-03-10 — Platform-Aware Encoder + Agent Script Output

**Skill (`SKILL.md`) — v1.1 → v1.2:**

- **Platform-aware Encode flow** — Encode flow now asks platform first (Agent Builder or Agent Script), then generates platform-specific output. Entry point detection updated from "Agent Builder" to "Agentforce."
- **Agent Script generation path** — New generation items for Agent Script: `system.instructions` (full persona), `system.messages.welcome`, `system.messages.error`, topic-level `system:` overrides, `reasoning.instructions`, `progress_indicator_message`, and deterministic `| text` response examples.
- **Encoding-specific context updated** — Platform question added as item 1. Company context question adapts to platform (255-char limit for Builder, no limit for Script). Topics and Actions context notes Script-specific outputs.

**Encoding guide (`resources/persona-encoding-guide.md`) — v2.0 → v2.1:**

- **Encoding Method Compatibility matrix** — New section after Encoding Methods Overview showing Builder/Script compatibility for all 6 encoding methods.
- **Advanced methods reframed as Builder workarounds** — Each advanced section now opens with the specific Builder limitation it addresses. Framing clarifies that Agent Script provides these capabilities natively.
- **"Persona Variable Method" → "Custom Metadata"** — Renamed throughout. Updated compatibility section to cover both platforms.
- **"General Topic Override"** — Renamed from "General" for consistency. Added Builder limitation framing.
- **Persona-Carrying YAML Keys table** — New table in Agent Script section documenting all 7 persona-relevant YAML keys with their locations and persona mappings.
- **Recommended Pattern updated** — References specific YAML keys (`system.instructions`, topic-level `system:`, `reasoning.instructions`, `progress_indicator_message`, `system.messages.welcome`, `system.messages.error`, `| text`). Documents the replace-vs-extend distinction between topic `system:` and `reasoning.instructions`.
- **Static Messages Summary table** — Source column updated from generic labels to YAML key names.

**Encoding template (`templates/persona-encoding-template.md`) — v1.2 → v2.0:**

- **Title renamed** — "Agent Builder Encoding" → "Persona Encoding" (platform-neutral).
- **"CMT variable" → "Custom Metadata"** — Renamed in Encoding Method table.
- **Target Platform indicator** — New field after Encoding Method table.
- **Agent Builder section wrapper** — Existing Builder fields wrapped under "Agent Builder Encoding" heading with conditional note.
- **Agent Script encoding sections** — New sections: `system.instructions`, `system.messages.welcome`, `system.messages.error`, per-topic `system:` overrides, per-topic `reasoning.instructions`, per-action `progress_indicator_message`, and deterministic response examples.
- **Heading depth adjusted** — Field headings changed from `##`/`###` to `###`/`####` to nest under platform sections.

---

### 2026-03-10 — Encoding Guide v2.0 + Encoding Template v1.2

**Encoding guide (`resources/persona-encoding-guide.md`) — v1.2 → v2.0:**

- **Vocabulary overhaul** — All v1.0 archetype references (Conversational, Personable, Professional as Voice positions, Matter-of-Fact, Encouraging Realist, Clinical Analyst as Tone positions) replaced with v1.1 attribute vocabulary (Formality, Warmth, Personality Intensity, Emotional Coloring, Empathy Level). "Archetype selections across dimensions" → "attribute selections across categories."
- **Encoding Methods Overview** — New section near the top orienting readers to the three encoding method categories: Agent Builder field-by-field (primary), Agent Script (NGA), and advanced methods.
- **Per-Topic Persona Instructions** — New section formalizing the layered encoding model: global persona in Role → per-topic calibration in Topic Instructions → per-action formatting in Action Output. Includes brevity calibration, phrase book entries, humor guidance, tone flex triggers, and lexicon entries per topic.
- **Per-Topic Lexicon** — New section documenting topic-scoped vocabulary with `Lexicon:` block pattern. Distinguishes Lexicon (what words to use) from Phrase Book (how to sound). Example: luxury watch agent with specialized vocabulary in product topics but not order-tracking topics.
- **Per-Topic Tone Calibration** — New section mapping persona document Tone Flex rules to Topic Instructions. Pattern: "In [topic], shift Emotional Coloring toward [direction]."
- **Conversation Style Instruction** — New advanced section documenting lightweight encoding pattern. Single `Conversation Style:` instruction achieving strong persona expression. Template, examples, use cases, and limitations documented.
- **Error Message** — New dedicated subsection in field-by-field guidance with examples.
- **Platform Tone Setting** — Updated to reference v1.1 attribute names (Emotional Coloring, Warmth, Personality Intensity, Empathy Level instead of old Tone dimension and Voice archetype names).
- **Persona-Carrying Fields table** — Maps From column updated to reference v1.1 attribute names.
- **Loading Text table** — Updated from Voice position labels (Conversational, Personable) to Formality + Warmth attribute labels (Casual, Casual + Warm).
- **Welcome Message examples** — Updated labels from v1.0 archetypes to v1.1 attribute combinations.
- **What's NOT Persona** — Description field row clarified.

**Encoding template (`templates/persona-encoding-template.md`) — v1.1 → v1.2:**

- **Encoding Method table** — Expanded with method options.
- **Per-Topic Persona Instructions** — Expanded to show Tone Calibration as a distinct named sub-pattern within per-topic blocks.
- **Conversation Style output variant** — New section for lightweight encoding output (single Conversation Style paragraph as alternative to full field-by-field encoding).

---

### 2026-03-10 — v1.1: Attribute decomposition, persona archetypes, and skill flow redesign

Major restructure: decomposes bundled archetype dimensions into single-axis attributes, adds persona archetype presets, and rewrites the skill workflow from a 7-step sequential walk-through to a fast input-to-sample-dialog loop.

**Framework (`resources/persona-framework.md`):**

- **Attribute decomposition** — Voice (Formal / Professional / Conversational / Personable) decomposed into 3 independent attributes: Formality, Warmth, Personality Intensity. Tone (Clinical Analyst / Matter-of-Fact / Encouraging Realist) decomposed into Emotional Coloring + Empathy Level. Register validated as already single-axis. Total: 12 attributes across 5 categories (Register, Voice, Tone, Delivery, Chatting Style), each independently selectable.
- **Persona archetype presets** — 6 named starting points organized by use case (Internal Sales Coach, External Customer Service, Lead Generation) with conservative and outlandish variants. Each preset pre-populates all 12 attributes. Presets: The Steady Hand, Drover, The Concierge, Y.T., The Qualifier, Bluebonnet.
- **Tone Flex** — New section defining how tone shifts from baseline in response to context (system state, user emotion, content sensitivity, conversation phase). Includes triggers, shift rules, flex range, and hard boundaries.
- **Lexicon** — New section for per-topic brand terminology and domain vocabulary.
- **Tension Pairs** — New section documenting productive attribute combinations (e.g., Formal + Warm, Blunt + Playful, High Empathy + Terse).
- **Attribute Boundaries** — New section with boundary tests to distinguish overlapping attributes.
- **Cultural Adaptation Note** — New section on per-locale persona considerations.
- **Warmth spectrum extended** — Added Radiant above Bright (for mascot/kawaii brands).
- **Emotional Coloring spectrum extended** — Added Blunt below Clinical (for no-diplomatic-packaging brands).
- **Empathy Level spectrum extended** — Added Understated between Minimal and Moderate (care through action, not words).
- **Voice Channel Parameters** — Optional section for voice surface agents (pitch, speaking rate, energy, aural warmth).
- **Constraint notes updated** — Rewritten for the decomposed attribute model. Constraints are recommendations, not hard stops.
- **Scope Boundary table** — Clarified what belongs to Persona Design vs. Agent Design vs. Conversation Design.

**Skill workflow (`SKILL.md`):**

- **New Design flow** — Replaced 7-step sequential walk-through (Context / Identity / Dimensions / Phrase Book / Generate / Score / Encode) with fast input-to-sample-dialog loop (Input / Minimal Context / Draft / Sample Dialog / Refine / Download).
- **Brand input support** — Step 1 accepts brand guide PDF, organization URL, prior persona.md, or free-text description. No detection question needed.
- **Minimal Context** — Step 2 extracts context from user input before asking questions. Only asks what's missing. Zero questions is valid.
- **Silent Draft** — Step 3 auto-generates a complete persona via input parsing, archetype matching, confidence annotations, and state object maintenance. Invisible to the user.
- **Sample Dialog** — Step 4 presents 3-5 turns demonstrating the persona in action. With/without persona toggle shows delta on first viewing.
- **Conversational editing** — Step 5 includes an explicit mapping table (e.g., "warmer" maps to increase Warmth, consider Empathy Level).
- **Deterministic editing** — Step 5 supports "show all settings" with confidence annotations (strong signal / default).
- **Diff-based regeneration** — Single-attribute changes hold all other attributes constant. Change is narrated.
- **Natural exit points** — After 2-3 refinement rounds, offer download.
- **Entry point detection** — Detects Design vs. Encode intent from opening message.
- **Encode flow** — Standalone entry point for encoding existing persona.md into Agent Builder fields. Collects encoding-specific context (surface, topics, actions). Generates per-topic persona instructions and per-action loading text.
- **Phrase Book opt-out** — Generated automatically in draft, visible during refinement. No longer a separate forced step.
- **Scoring rubric updated** — Attribute Consistency replaces Dimension Consistency. Added Sample Quality criterion. Phrase Book Quality weighted higher.

**Templates:**

- **Persona template** — Attribute-based Persona Profile table with confidence annotations. Attribute Detail organized by category. Added Tone Flex, Lexicon, and Sample Dialog sections. Context section minimized (encoding fields moved to Encode flow).
- **Encoding template** — Added Encoding Method section, Per-Topic Persona Instructions, Per-Topic Lexicon, expanded Loading Text with per-action support.

**Research (`_local/research/dimension-decomposition.md`):**

- 19-brand stress test across 10 cultural contexts (US, Japan, South Africa/UK, UAE, Brazil, Scotland, India, South Korea, Sweden, fictional). All brands map to unique attribute combinations. Validated that previously impossible combinations (Formal + Warm, Blunt + Playful, Reserved + Warm) now express cleanly.

---

### 2026-03-02 — v1.0: Publication release

First published version. Includes the complete 7-step persona design workflow:

1. **Context** — Detection-based: import from agent design or gather minimum fields
2. **Identity** — 3-5 personality adjectives with behavioral definitions
3. **Dimensions & Settings** — Register, Voice, Brevity, Tone (+Empathy Level, +Tone Boundaries), Humor, Chatting Style
4. **Phrase Book** — Dynamic phrase categories tuned to persona selections
5. **Generate** — Persona document with sample interactions
6. **Score** — 50-point rubric (5 categories x 10 points)
7. **Encode** — Copy-paste-ready Agent Builder field values

Pre-1.0 development history is available locally (`_local/`) but not published.
