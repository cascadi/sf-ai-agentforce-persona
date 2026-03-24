---
name: sf-ai-agentforce-persona
description: Designs an AI agent persona — identity, voice, tone, and behavioral style — through a fast input-to-sample-dialog loop with brand input support, 12 decomposed attributes, and 50-point scoring
version: 2.0.0
author: cascadi
tags: [salesforce, agentforce, persona, identity, register, formality, warmth, personality, tone, brevity, humor, chatting-style, brand-input, sample-dialog]
allowed-tools:
  - Read
  - Write
  - AskUserQuestion
  - Glob
  - Grep
---

# Agent Persona Design

## How to Use

This skill designs an AI agent persona through a fast input-to-sample-dialog loop. Provide any starting input — a brand guide PDF, a URL, a prior persona document, or a text description — and the skill drafts a complete persona, shows you how the agent sounds in sample dialog, and lets you refine until it's right.

**What it produces:**
- A persona document (`_local/generated/[agent-name]-persona.md`) defining who the agent is, how it sounds, and what it never does
- Scoring available on request (50-point rubric)
- Encoding available as a separate workflow (persona → platform-specific field values)

**What it drives downstream:** The persona document feeds into conversation design and Agentforce encoding. Those are separate steps — this skill defines the *persona*, not dialog flows or field configurations.

**Session resumption:** If you stop mid-workflow, your partial progress is preserved in the conversation and can be resumed.

## When to Use This Skill

- Designing a new Agentforce agent and need to define its personality before building
- Retrofitting persona consistency onto an existing agent whose tone is inconsistent
- Translating brand guidelines or tone documents into a structured persona
- Aligning stakeholders on what an agent should sound like before development begins
- Documenting an agent's voice for handoff between design and implementation teams

**Scope boundary:** This skill defines WHO the agent is. It does not define dialog flows, utterance templates, or interaction branching — those belong in conversation design.

## Framework Reference

Read `resources/persona-framework.md` for the full framework. It defines:

- **Identity** — 3-5 personality adjectives that anchor every other decision
- **12 attributes** across 5 categories:
  - **Register** — Subordinate / Peer / Advisor / Coach
  - **Voice** — Formality, Warmth, Personality Intensity (3 independent attributes)
  - **Tone** — Emotional Coloring, Empathy Level (+ Tone Boundaries, Tone Flex)
  - **Delivery** — Brevity, Humor
  - **Chatting Style** — Emoji, Formatting, Punctuation, Capitalization
- **Phrase Book & Never-Say List** — what to say and what to never say
- **Tone Flex** — how tone shifts by context
- **Lexicon** — per-topic vocabulary

Attributes are ordered by dependency — upstream choices constrain downstream ones. Constraint notes in the framework explain how earlier choices pull later ones. Constraints are recommendations, not hard locks — any combination is valid.

---

## Entry Point Detection

Detect the user's intent from their opening message:

- **User provides brand input, text description, or no document** → **Design flow** (below)
- **User provides a completed persona.md document and asks to encode** → **Encode flow** (below)
- **User provides a persona.md + a list of topics or actions** → **Encode flow**
- **Ambiguous** → Ask: "Are you designing a new persona or encoding an existing one for Agentforce?"

---

## Design Flow

Two phases: **Phase 1 (Essentials)** gets to sample dialog as fast as possible. **Phase 2 (Electives)** lets the user choose what to do next.

```
PHASE 1: INPUT → CONTEXT → IDENTITY → ATTRIBUTES → SAMPLE DIALOG
                                                        │
PHASE 2:                                          ┌─────┴─────┐
                                                  │  HUB MENU │
                                                  └─────┬─────┘
                                            ┌───────────┼───────────┐
                                            │           │           │
                                       Refine     Electives     Export
                                     (identity,  (phrase book,  (download,
                                      attributes, never-say,    score,
                                      scenario)   tone flex,    encode)
                                                  lexicon)
```

### Phase 1: Essentials

#### Step 1: Input

Accept any starting input. No detection question needed — accept whatever the user provides.

**Accepted inputs:**
- Brand guide or tone-of-voice document (PDF, text)
- Organization URL
- Prior persona document (persona.md from a previous session)
- Free-text description (e.g., "a sales coach who talks like Crocodile Dundee")
- Existing agent system prompt or .agent file
- Any combination of the above

**If the user provides nothing** (invokes the skill without additional input):
> "Share something to get started — a brand guide, a URL, or just describe the agent in your own words. I'll draft a persona and show you how it sounds in conversation."

Do NOT ask a detection question. Accept whatever arrives and proceed.

#### Step 2: Minimal Context

Collect only what the input doesn't already answer. **Every question is skippable.** Zero questions is valid — if the input provides enough signal, skip directly to Draft.

**Context signals to extract or ask about (priority order):**

1. **Internal vs. external audience** — affects register, formality, warmth. If the user says "internal sales coach," audience is already answered.
2. **At least 1 use case or JTBD** — needed to generate meaningful sample dialog.

**Do NOT collect:** surface (encoding question, not persona), interaction model (agent design, not persona), agent type, topic list, agent name (comes after identity).

**Extraction before asking:** Parse the user's input for context signals before deciding what to ask. "Design an internal sales coach persona for Buc-ee's" already answers audience (internal), role (sales coach), and implies a brand context. Don't re-ask what's already given.

**May ask 1-2 clarifying questions** to surface tensions in the input (e.g., "Your brand guide emphasizes both 'bold irreverence' and 'trusted expertise' — which should win when they conflict?"). But every question is skippable.

#### Step 3: Draft

This step is the skill's intelligence — it must execute explicitly as specified below. The draft is **invisible to the user** — no intermediate output is shown. The first thing the designer sees is the sample dialog.

##### 3A: Input Parsing

Extract persona signals from the user's input:

- **Tone/voice signals:** Look for adjectives, "we are..." statements, competitive positioning language, formality markers.
- **Negative signals:** "Never," "don't," "we are NOT" statements — often the strongest persona signals. These feed the Never-Say List.
- **Audience signals:** Who the brand talks to, formal vs. informal examples, relationship language.
- **If input is a prior persona.md:** Extract attributes directly.

##### 3B: Attribute Selection

Map extracted signals to the 12 framework attributes:

1. Pre-populate all 12 attributes from the input signals
2. Override attributes where the input provides clear signals (e.g., brand guide says "never use slang" → Formality: Formal)
3. For each attribute, show the full spectrum and indicate which value is recommended and why when there's a strong signal

##### 3C: Confidence Annotations

Mark each attribute as:
- **Strong signal** — clear evidence in input (quote or cite the source)
- **Default** — inferred from context, no direct evidence in input

These annotations are shown during refinement so the designer knows where to focus.

##### 3D: Generation

From the attribute map, generate:
- **Identity traits** — 3-5 adjectives with behavioral definitions
- **Phrase Book** — example phrases tuned to all selected attributes, including affirmations
- **Never-Say List** — anti-phrases derived from Tone Boundaries, Identity contradictions, and input's negative signals
- **Tone Boundaries** — what the agent must never sound like
- **Tone Flex** — baseline + triggers + shift rules
- **Negative Identity** — 2-4 character-level anti-patterns. Generate from negative signals in the input and from Identity traits.
- **Values** *(optional)* — Only if the user explicitly stated beliefs, values, or worldview. Never infer values.

##### 3E: Name

After identity traits are established:
- Suggest up to 3 names that distill the identity
- Allow the user to write their own
- Reassure: "You can change this later."

If a name was provided in input, use it and skip this sub-step.

##### 3F: State Object

Maintain the full attribute map as an explicit **state object** across the conversation. Every regeneration works from this state, not from conversation history. The state object contains:
- All 12 attribute values
- Confidence annotation per attribute (strong signal / default)
- Identity traits
- Negative Identity statements
- Values (if provided by user — never inferred)
- Phrase Book
- Never-Say List
- Tone Boundaries
- Tone Flex rules

Update the state object on every change. When regenerating sample dialog, read from the state object.

#### Step 4: Sample Dialog

Present a few turns of conversation (3-5 exchanges) based on the use case from Step 2.

**Requirements:**
- The dialog demonstrates the persona in action — word choice, tone, brevity, humor, formatting
- Include at least one "interesting" turn: an error, a clarification, or an emotional moment — not just happy path
- None of these agents say "Hello! How can I help you today?" — the sample should make the persona's impact obvious

**With/without persona view:**
On the **first** sample dialog, show two versions:
1. **With persona** — the agent responding with the designed persona applied
2. **Without persona** — a generic neutral agent with no persona instructions, responding to the same prompts

This demonstrates the value of the design process. After the first showing, the with/without view is available on request only.

**After presenting the sample dialog,** transition to Phase 2 by offering the hub menu.

### Phase 2: Electives

After the sample dialog, offer next steps. The user picks what to do. After completing any elective, offer the hub menu again (minus completed items). The user decides when they're done.

**Hub menu options:**
- "Try a different sample dialog scenario"
- "Refine the persona (identity or attributes)"
- "Add phrase book entries"
- "Add never-say list entries"
- "Add tone flex rules"
- "Add lexicon (per-topic vocabulary)"
- "Add anything else" *(free-text — see Other below)*
- "Score the persona"
- "Download the persona document"
- "Encode for Agentforce deployment"

#### Refine

Two editing modes, both available at any time. The user can mix them freely.

**Conversational Editing** — The user describes changes in natural language. Map common requests to specific attribute changes:

| User says | Attribute change | Also consider |
|---|---|---|
| "warmer" | Warmth: increase one position | Empathy Level: increase one position |
| "cooler" / "less warm" | Warmth: decrease one position | Empathy Level: decrease one position |
| "more formal" | Formality: increase one position | Register: shift toward Advisor |
| "less formal" / "more casual" | Formality: decrease one position | |
| "shorter" / "more concise" | Brevity: decrease one position (toward Terse) | |
| "longer" / "more detail" | Brevity: increase one position (toward Expansive) | |
| "more personality" | Personality Intensity: increase one position | Humor: consider enabling if None |
| "less personality" / "more neutral" | Personality Intensity: decrease one position | |
| "less robotic" | Warmth: increase + Personality Intensity: increase | |
| "more professional" | Formality: Professional, Humor: None or Dry | Personality Intensity: Moderate |
| "friendlier" | Warmth: increase + Emotional Coloring: Encouraging | Empathy Level: increase |
| "more direct" / "blunter" | Emotional Coloring: toward Blunt, Brevity: toward Terse | Empathy Level: toward Minimal |
| "more encouraging" | Emotional Coloring: Encouraging | Empathy Level: Moderate or High |
| "funnier" | Humor: increase one position | Personality Intensity: increase if Reserved |
| "no humor" | Humor: None | |
| "more emoji" | Emoji: increase one position | |
| "less emoji" | Emoji: decrease one position | |

When a request is ambiguous, apply the primary mapping and narrate the change so the user can correct.

**Deterministic Editing** — Invoked by asking to "show all settings," "show the attribute table," or "let me see the details." Display all attributes with confidence annotations. The user selects specific attributes to adjust. Present the full spectrum with the current value highlighted. After adjustment, regenerate sample dialog.

**Diff-Based Regeneration** — After a single-attribute change:
1. Hold ALL unchanged attributes constant
2. Regenerate sample dialog varying only the changed attribute
3. Narrate the change so the user sees exactly what it does

#### Other (free-text additions)

When the user wants to add something that doesn't fit a standard framework concept, accept it. Review the input:
- If it maps naturally to an existing framework element (e.g., a tone boundary, a phrase book entry, a lexicon term), propose incorporating it there and ask for approval.
- If it doesn't fit, insert the user-generated text into the persona document as a custom section and ensure it's included in encoding output.

#### Lexicon

Lexicon is optional. Introduce the concept: domain vocabulary scoped per topic. Gather words that matter — especially words specific to certain topics. Disambiguate from phrase book:
- **Phrase Book** = how the agent sounds in common situations (acknowledgements, redirects, celebrations)
- **Lexicon** = what specific words and terms the agent uses in particular domains (technical terms, brand language, industry jargon)

---

## Scoring

Score the persona document against a 50-point rubric. Scoring is **on-demand** — triggered when the user asks. Write the scorecard to: `_local/generated/[agent-name]-persona-scorecard.md`.

*For an unbiased score, have a different person run the scoring rubric on the generated persona.*

| Category | Points | What It Measures |
|---|---|---|
| **Identity Coherence** | /10 | Traits are distinct, non-contradictory, and behaviorally defined. Each trait generates specific, observable agent behaviors — not vague aspirations. |
| **Attribute Consistency** | /10 | Each attribute is independently set and coherent with Identity. Combinations respect constraint notes. Tone Boundaries are consistent with Emotional Coloring and Empathy Level. Tone Flex rules stay within flex range and never cross Tone Boundaries. |
| **Behavioral Specificity** | /10 | Attribute selections include concrete behavioral examples. Rules are testable. Chatting Style, Tone Boundaries, and Tone Flex triggers are explicit. Never-Say List entries are specific and actionable. |
| **Phrase Book Quality** | /10 | Phrases are consistent with all attributes. Never-Say List items are distinct from Tone Boundaries. Variety in acknowledgements and affirmations. Language matches register. |
| **Sample Quality** | /10 | Sample dialog demonstrates persona attributes recognizably. A reader could identify which persona produced these responses without seeing the attribute table. With/without persona delta is clear. Samples cover happy path, uncertainty, and boundary scenarios. |

**Scoring rules:**
- Score each category independently. Provide a number and 1-2 sentences of justification.
- Flag inconsistencies between attributes. Note productive tensions vs. contradictions.
- If any category scores below 7, provide a specific suggestion for improvement.
- Total: 45-50 production-ready, 35-44 strong foundation, 25-34 needs revision, below 25 restart.

---

## Encode Flow

A standalone entry point for encoding an existing persona document into platform-specific output. Accessible when the user provides a completed persona.md, or after the Design flow.

Read `resources/persona-encoding-guide.md` for encoding architecture and `templates/persona-encoding-template.md` for the output structure.

### Encoding Context

Collect context needed for encoding:

1. **Platform** — "Are you encoding for Agent Builder or Agent Script (.agent file)?"
2. **Company context** — For Builder: "The Company field (255 chars) describes what your company does and who it serves. What should go here?" For Agent Script: "What should the system instructions include about the company?" If the persona description already names the business or customers, use that — don't re-ask.
3. **Surface** — Where the agent appears (web chat, Slack, voice, etc.). Affects Chatting Style encoding.
4. **Agent type** — Employee Agent, Customer Agent, Service Agent, etc.
5. **Topics** *(optional)* — "Do you have specific topics defined for this agent?" If provided, generate per-topic persona instructions with brevity calibration, phrase book entries, lexicon, and tone flex triggers. **Do not guess or invent topics.** If the user declines, generate only the global encoding.
6. **Actions** *(optional)* — "Do you have specific actions defined?" If provided, generate persona-consistent loading text for each. **Do not guess or invent actions.**

The user can do just the global encoding and return later with topics and actions.

### Generation

#### If Agent Script

Output ready-to-paste YAML blocks:

**System block:**
1. **`config.agent_name`** — The persona name.
2. **`system.instructions`** — Full persona content as a YAML literal block scalar (`|`): Identity, attribute behavioral rules, phrase book, chatting style rules, tone boundaries, never-say list. No character limits.
3. **`system.messages.welcome`** — Static welcome message.
4. **`system.messages.error`** — Static error message.

**Per-topic overrides** (if topics provided):
5. **`reasoning.instructions` per topic** — Persona calibration: brevity, lexicon, tone flex, phrase book entries, humor guidance, voice pointer.
6. **Topic-level `system:` override** — Only when a topic's tone flex warrants a full system-level override. Rare.

**Per-action loading text** (if actions provided):
7. **`progress_indicator_message`** per action — In-character loading text with `include_in_progress_indicator: True`.

**Deterministic response examples:**
8. Example `| text` pipes for common if/else branches written in the persona's voice.

**Optional static welcome message:**
9. If the user wants a dynamic welcome message rather than static, note that this requires additional configuration outside Agent Script. Reference: [Design Better Greetings in Agentforce Builder](https://www.salesforce.com/blog/design-better-greetings-agentforce-builder/).

#### If Agent Builder

**Agent Configuration Fields:**
1. **Name** (80 chars) — Show character count.
2. **Role** (255 chars) — Functional summary only: what the agent does and who it serves. "You are..." Do **not** encode persona style here. Show character count.
3. **Company** (255 chars) — Show character count.
4. **Welcome Message** (800 chars, aim for ≤ 255) — Reflect Identity + Register + Voice + Tone + Brevity. Show character count.
5. **Error Message** — Reflect Formality + Warmth + Emotional Coloring + Brevity.

**Platform Settings:**
6. **Tone dropdown** — Recommend based on Register + Formality. Note it's a coarse approximation.
7. **Conversation Recommendations on Welcome Screen** — On when use cases are defined; Off when open-ended.
8. **Conversation Recommendations in Agent Responses** — On for proactive agents; Off for socratic agents.

**Global Persona Block:**
9. **Global instructions** — Full persona content for a dedicated global instructions topic. Synthesize from all persona sections.

**Per-topic persona instructions** (if topics provided):
10. Tailored instructions per topic with brevity calibration, phrase book entries, lexicon terms, tone flex triggers, humor guidance.

**Loading Text:**
11. **Generic loading text examples** — 3-4 examples reflecting Voice + Tone + Brevity.
12. **Per-action loading text** — If actions were provided: persona-consistent loading text for each.

**Optional: Dynamic welcome message:**
13. Note the option for a dynamic welcome message using a custom field, Context Variable, Prompt Template, and Omni-Channel Flow. Reference: [Design Better Greetings in Agentforce Builder](https://www.salesforce.com/blog/design-better-greetings-agentforce-builder/).

#### Output

Present all generated values and ask the user to review. Character-limited fields (Builder) show the count.

Write the encoding output using the `Write` tool. Default path: `_local/generated/[agent-name]-persona-encoding.md`.

---

## Output

The skill produces up to four Markdown files:

1. **Persona document** (`_local/generated/[agent-name]-persona.md`) — follows the `templates/persona-template.md` structure. The design artifact defining who the agent is, how it sounds, and what it never does.
2. **Sample dialog** (`_local/generated/[agent-name]-sample-dialog.md`) — follows the `templates/sample-dialog-template.md` structure. Validation artifact demonstrating the persona in conversation.
3. **Scorecard** (`_local/generated/[agent-name]-persona-scorecard.md`) — 50-point rubric evaluation. Generated on request.
4. **Encoding output** (`_local/generated/[agent-name]-persona-encoding.md`) — follows the `templates/persona-encoding-template.md` structure. Platform-specific: Agent Builder field values and settings, or Agent Script YAML blocks. Generated on request via the Encode flow.
