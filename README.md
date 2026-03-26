---
version: "2.1.0"
date: 2026-03-24
---

# Agent Persona Design Skill

Design AI agent personas for Salesforce Agentforce using the **Identity + 12 decomposed attributes** framework.

An agent persona is a designed personality that tells an AI agent who it is and how to express itself so it comes across naturally as a consistent character suited to its context and users.

This skill provides a fast input-to-sample-dialog loop for designing consistent, intentional agent personas — from brand input through persona document to Agentforce encoding.

## Quick Start

Provide any starting input — a brand guide PDF, a URL, a prior persona document, or a text description — and the skill drafts a complete persona, shows you how the agent sounds in sample dialog, and lets you refine until it's right.

**Two-phase workflow:**

```
PHASE 1:  INPUT → CONTEXT → IDENTITY → ATTRIBUTES → SAMPLE DIALOG
PHASE 2:  HUB MENU → refine / phrase book / never-say / lexicon / score / encode
```

**Phase 1 (Essentials)** gets to sample dialog as fast as possible:
1. **Input** — Brand guide PDF, URL, prior persona.md, or text description
2. **Minimal Context** — Company, audience, modality, use case (extracts from input or asks — all skippable)
3. **Draft** — Auto-generates identity + attributes (silent)
4. **Name** — Suggests names distilled from identity
5. **Sample Dialog** — Shows how the agent sounds in conversation

**Phase 2 (Electives)** — user-driven hub menu:
- Refine identity or attributes
- Add phrase book, never-say list, tone flex, lexicon
- Score the persona (50-point rubric)
- Download the persona document
- Encode for Agentforce (Agentforce Builder or Agent Script)

## Output

Four Markdown files:
- **Persona document** (`_local/generated/[agent-name]-persona.md`) — design artifact defining who the agent is, how it sounds, what it never does
- **Sample dialog** (`_local/generated/[agent-name]-sample-dialog.md`) — validation artifact demonstrating the persona in conversation
- **Scorecard** (`_local/generated/[agent-name]-persona-scorecard.md`) — 50-point rubric evaluation (on request)
- **Encoding output** (`_local/generated/[agent-name]-persona-encoding.md`) — Agentforce Builder field values or Agent Script YAML blocks (via Encode flow)

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill definition — Design flow + Encode flow + scoring rubric |
| `resources/persona-framework.md` | Identity + 5 categories, 12 attributes — the deep reference |
| `resources/persona-encoding-guide.md` | How to encode persona into Agentforce (architecture-first) |
| `templates/persona-template.md` | Persona document output template |
| `templates/sample-dialog-template.md` | Sample dialog output template |
| `templates/persona-encoding-template.md` | Encoding output template (Agentforce Builder + Agent Script) |
| `CHANGELOG.md` | Version history |

## Framework Overview

12 attributes across 5 categories, each independently selectable:

- **Register** (1 attribute) — Subordinate / Peer / Advisor / Coach
- **Voice** (3 attributes)
  - **Formality** — Formal / Professional / Casual / Informal
  - **Warmth** — Cool / Neutral / Warm / Bright / Radiant
  - **Personality Intensity** — Reserved / Moderate / Distinctive / Bold
- **Tone** (2 attributes)
  - **Emotional Coloring** — Blunt / Clinical / Neutral / Encouraging / Enthusiastic
  - **Empathy Level** — Minimal / Understated / Moderate / High
  - *+ Tone Boundaries, Tone Flex*
- **Delivery** (2 attributes)
  - **Brevity** — Terse / Concise / Moderate / Expansive
  - **Humor** — None / Dry / Warm / Playful
- **Chatting Style** (4 attributes)
  - **Emoji** — None / Functional / Expressive
  - **Formatting** — Plain / Selective / Heavy
  - **Punctuation** — Conservative / Standard / Expressive
  - **Capitalization** — Standard / Casual

Attributes are ordered by dependency — upstream choices constrain downstream ones. Constraints are recommendations, not hard locks. The persona document also includes a **Phrase Book** (with affirmations), **Never-Say List**, **Tone Flex** rules, and optional **Lexicon** for per-topic vocabulary.

## Encoding Architecture

Persona encoding follows a three-layer model on both Agent Script and Agentforce Builder:

1. **Global instructions** — baseline identity + all attributes (Agent Script: `system.instructions`, Builder: dedicated global instructions topic)
2. **Topic calibration** — per-topic overrides for brevity, tone flex, lexicon, phrase book, humor
3. **Static messages** — welcome, error, loading text, deterministic responses

See `resources/persona-encoding-guide.md` for the full guide.

## Version

See `CHANGELOG.md` for full version history. Each file carries its own version in YAML frontmatter.

## Contributing Upstream

This skill is designed for contribution to [jaganpro/sf-skills](https://github.com/jaganpro/sf-skills). Upstream validation: `python3 skill-builder/scripts/bulk_validate.py` (run in a fork of sf-skills). AI assistance must be disclosed in PR descriptions. See `CONTRIBUTING.md` for details.

## Sources

This framework synthesizes ideas from multiple published sources into an original persona design system for AI agents:

- **Conversation Design Institute (CDI)** — Foundational principles on intentional persona design, including the pareidolia effect, register (via Leary's Interpersonal Circumplex), the "One Breath Test," tapering, and apology guidelines
- **Nielsen Norman Group (NN/g)** — Research on voice and tone in UX writing, the distinction between voice (persistent) and tone (contextual), and usability heuristics that inform dimension boundaries
- **Google Conversation Design Guidelines** — Principles for persona definition, error handling patterns, and turn-taking
- **Amazon Alexa Design Guidelines** — Voice channel parameters and voice-specific persona considerations
- **Salesforce** — Agentforce architecture, Agentforce Builder field constraints, and design patterns that shape the encoding guide

## License

MIT — see `LICENSE`.
