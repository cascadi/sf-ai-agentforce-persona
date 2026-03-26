---
version: "2.2.0"
date: 2026-03-25
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

---

## Voice Encoding

*Include this section when modality includes telephony/voice. See `resources/persona-encoding-guide-voice.md` for full guidance.*

### Voice Selection Criteria

| Criteria | Target |
|---|---|
| **Language** | [primary language] |
| **Gender** | [inferred or specified] |
| **Voice Qualities** | [description of what to look for based on persona — e.g., warm, professional, measured pace] |

Select from available voices in your org's voice library. Listen to previews before deciding.

### Voice Fine-Tuning (starting points)

| Parameter | Starting Point | Persona Rationale |
|---|---|---|
| **Speed** | [value] | [match to Brevity] |
| **Stability** | [value] | [match to Emotional Coloring + Personality Intensity] |
| **Similarity** | [value] | [default or adjusted] |

### Key-Term Prompting

| Term |
|---|
| [brand name] |
| [product name] |
| [domain term] |

### Pronunciation Dictionary *(optional)*

| Word | Pronunciation (IPA) | Verified? |
|---|---|---|
| [term] | [approximate IPA] | ⚠️ Verify in voice preview |

### Voice Welcome Message

```
[Shorter than text welcome. Includes AI disclosure. In persona voice.]
```

### Voice Instruction Adjustments

- Brevity: [text default] → [voice adjustment, one position shorter]
- Formatting: suppressed (no emoji, bullets → ordinals)
- [Any pausing guidance for structured data]
