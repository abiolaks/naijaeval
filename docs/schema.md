# NaijaEval — Prompt Schema

Each task has a JSONL file with one prompt per line. Fields marked `*` are
required.

## 1. Multi-turn chat (`pidgin_chat_seed.jsonl`)

Tests whether a model can hold a coherent, helpful 5+ turn conversation in
Nigerian Pidgin.

```json
{
  "id*": "chat_001",              // unique, lowercase, task prefix
  "domain*": "banking",           // one of: banking, health, transport, food,
                                  //   tech, education, entertainment, legal,
                                  //   housing, utilities, shopping, travel,
                                  //   religion, family, sports, government
  "difficulty*": "easy|medium|hard",
  "source": "real-world-lagos",  // where the scenario came from
  "turns*": [
    {
      "role*": "user|assistant",
      "content*": "string"       // Pidgin text
    }
  ]
}
```

**Validation rules:**
- Minimum 5 turns
- Turns must alternate user/assistant/user/assistant/...
- Must start with `user` and end with `user` (the last assistant turn is what
  the model generates; the last user turn is the final question to respond to)
- `domain` must be one of the enumerated values

**What the model receives:**
All turns EXCEPT the final assistant turn are included in the prompt. The
model's task is to generate the final assistant response. The judge evaluates
the entire conversation together.

## 2. Code-switching fluency (`codeswitch_seed.jsonl`)

Tests whether models understand and match mixed-language input.

```json
{
  "id*": "cs_001",
  "domain*": "tech|social|work|daily-life|education|commerce|health",
  "type*": "pidgin-english-mix|yoruba-english-mix|hausa-english-mix|deep-pidgin-with-english-tech|yoruba-expressions-in-english|mixed-triple",
  "prompt*": "string",           // the code-switched input
  "expected_behavior*": "string", // what correct handling looks like
  "code_switch_count": 4         // number of language switch points
}
```

**Validation rules:**
- `prompt` must contain at least 2 identifiable code-switches
- `expected_behavior` must be descriptive enough for the judge to evaluate
- `type` must be one of the enumerated values

## 3. Factual grounding (`factual_seed.jsonl`)

Tests whether models give verifiable factual answers about Nigeria in Pidgin.

```json
{
  "id*": "fact_001",
  "domain*": "history|government|geography|economy|science|education|health|sports|culture|law",
  "prompt*": "string",           // question in Pidgin
  "answer*": "string",           // ground truth, detailed enough for judging
  "source*": "string",           // URL or citation for verification
  "tolerance*": "string"         // what counts as correct (partial credit rules)
}
```

**Validation rules:**
- `answer` must cite verifiable facts (dates, numbers, names)
- `source` must be a real URL or authoritative reference
- `tolerance` must include partial credit rules

## 4. Cultural appropriateness (`cultural_seed.jsonl`)

Tests whether models demonstrate genuine Nigerian cultural awareness.

```json
{
  "id*": "cult_001",
  "domain*": "greetings|ceremonies|food|social_norms|religion|family|language|dress|music|politics|history",
  "prompt*": "string",           // open-ended question in Pidgin or code-switched
  "dimensions*": [               // which rubric dimensions to score
    "cultural_accuracy",
    "contextual_nuance",
    "avoid_stereotyping",
    "practical_grounding"
  ],
  "context*": "string"           // what a good answer should cover (for judge calibration)
}
```

**Validation rules:**
- `dimensions` must be a subset of the four standard dimensions
- `context` must guide the judge without being a full answer key

---

## General rules across all tasks

1. **Language:** All prompts should be in natural Nigerian Pidgin or
   code-switched English/Yoruba as appropriate. No "academic" or sanitized
   versions.
2. **Cultural authenticity:** Scenarios should reflect real Nigerian
   experiences (Lagos traffic, PH reference, naija tech stack, etc.).
3. **No trick questions:** We're testing model capability, not trying to
   confuse it. Difficulty should come from real linguistic complexity,
   not from ambiguity.
4. **ID convention:** `{task_prefix}_{3-digit}` — `chat_001`, `cs_042`,
   `fact_150`, `cult_088`.
5. **Line format:** One JSON object per line, no trailing commas, UTF-8.
