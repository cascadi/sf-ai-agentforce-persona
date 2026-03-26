---
version: "2.1.0"
date: 2026-03-24
---

# [Agent Name] — Persona Encoding

**Agent Authoring Tool:** [Agentforce Builder / Agent Script]
**Date:** [date]
**Source persona:** [agent-name]-persona.md

---

## Agent Script Encoding

*Include this section when tool = Agent Script.*

### System Block

```yaml
config:
  agent_name: "[Agent Name]"

system:
  instructions: |
    [Full persona content: identity, attribute behavioral rules,
    phrase book, chatting style, tone boundaries, never-say list.
    Use literal block scalar (|) — no character limits apply.]

  messages:
    welcome: "[Static welcome message in persona voice]"
    error: "[Static error message in persona voice]"
```

### Per-Topic Calibration

*Include when topics are provided.*

```yaml
topic [topic_name]:
  description: "[Topic description]"
  reasoning:
    instructions: |
      Brevity: [calibration for this topic]
      Tone: [tone flex encoding for this topic]
      Lexicon: [domain terms for this topic]
      Phrase book: [situational phrases for this topic]
      Humor: [guidance for this topic]
      Voice Reminder: [pointer back to global persona]
```

*Repeat for each topic.*

### Per-Action Loading Text

*Include when actions are provided.*

```yaml
  actions:
    - action: [action_name]
      progress_indicator_message: "[In-character loading text]"
      include_in_progress_indicator: True
```

*Repeat for each action.*

### Deterministic Response Examples

```yaml
  instructions: ->
    if [condition]:
      | [Deterministic response in persona voice]
    else:
      ...
```

---

## Agentforce Builder Encoding

*Include this section when tool = Agentforce Builder.*

### Agent Configuration Fields

| Field | Limit | Value | Chars |
|---|---|---|---|
| **Name** | 80 | [agent name] | [count] |
| **Role** | 255 | [functional summary — what the agent does, who it serves. No persona style.] | [count] |
| **Company** | 255 | [company context] | [count] |
| **Welcome Message** | 800 (aim ≤ 255) | [welcome in persona voice] | [count] |
| **Error Message** | — | [error in persona voice] | — |

### Agentforce Builder Settings

| Setting | Recommendation | Rationale |
|---|---|---|
| **Tone** | [Casual / Neutral / Formal] | [mapping to Register + Formality] |
| **Conversation Recs on Welcome** | [On / Off] | [rationale] |
| **Conversation Recs in Responses** | [On / Off] | [rationale] |

### Global Persona Block

*For a dedicated global instructions topic.*

```
[Full persona content: identity, attributes, phrase book, tone
boundaries, never-say list, chatting style rules. This is the
primary encoding surface in Builder.]
```

### Per-Topic Persona Instructions

*Include when topics are provided.*

**[Topic Name]:**
```
Brevity: [calibration]
Tone: [tone flex encoding]
Lexicon: [domain terms and usage notes]
Phrase book: [situational phrases]
Humor: [guidance]
Voice Reminder: [pointer back to global persona]
```

*Repeat for each topic.*

### Loading Text

| Action | Loading Text |
|---|---|
| [action name] | [in-character loading text] |
| [action name] | [in-character loading text] |
| Generic (fallback) | [in-character loading text] |
