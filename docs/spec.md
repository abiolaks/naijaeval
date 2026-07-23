# NaijaEval — Specification

## Pitch

An open-source evaluation suite for **Nigerian Pidgin** and **code-switched
Nigerian English**, built as an EleutherAI `lm-evaluation-harness` extension.
Measures what no existing benchmark covers: multi-turn chat quality,
code-switching fluency, factual grounding, and cultural appropriateness in
Pidgin. Ships with a public leaderboard comparing frontier models.

## Why this exists

| Benchmark | Pidgin? | Multi-turn chat? | Code-switch fluency? | Accessible leaderboard? |
|---|---|---|---|---|
| AfroBench | Minimal | No | No | Minimal |
| IrokoBench | No | No | No | No |
| TukaBench | No | No | Only as attack | No |
| **NaijaEval** | **Yes** | **Yes** | **Yes** | **Yes** |

## Scope

### Languages
1. **Nigerian Pidgin (primary)** — the main focus
2. **Code-switched Pidgin/English** — the way Lagos actually speaks
3. **Code-switched Yoruba/English** — second most common pattern

Yoruba, Hausa, and Igbo are explicitly out of scope for v1. They can be added
later with native-speaker collaborators.

### Tasks (4 axes)

| # | Task | Prompt count | What it measures |
|---|---|---|---|
| 1 | **Multi-turn chat** | 150-200 | Can the model hold a coherent, helpful 5-10 turn conversation in Pidgin? |
| 2 | **Code-switching fluency** | 100-150 | Does the model understand and respond naturally to mixed-language input? |
| 3 | **Factual grounding** | 100-150 | Does the model give factually correct answers about Nigeria in Pidgin? |
| 4 | **Cultural appropriateness** | 100-150 | Does the model show Nigerian cultural awareness, or does it give generic/Western answers? |

**Total: ~450-650 prompts.** Achievable for one person in 2-3 weeks.

### Models to evaluate (v1)
- GPT-4o
- Claude Opus 4 / Sonnet 5
- Gemini 2.5 Pro
- N-ATLaS-LLM (Nigeria's fine-tuned Llama-3-8B)
- Aya-101 / Aya Expanse
- Africlaude-7B
- DeepSeek V3

## Architecture

```
naijaeval/
├── pyproject.toml          # Python package, depends on lm-eval
├── README.md
├── naijaeval/
│   ├── __init__.py
│   ├── tasks/              # lm-evaluation-harness task definitions
│   │   ├── __init__.py
│   │   ├── pidgin_chat/
│   │   │   ├── task.py     # Task class extending lm_eval.api.task.Task
│   │   │   └── prompts/    # JSONL prompt files
│   │   │       ├── single_turn.jsonl
│   │   │       ├── multi_turn.jsonl
│   │   │       └── judge_template.yaml
│   │   ├── codeswitch/
│   │   │   ├── task.py
│   │   │   └── prompts/
│   │   ├── factual/
│   │   │   ├── task.py
│   │   │   └── prompts/
│   │   └── cultural/
│   │       ├── task.py
│   │       └── prompts/
│   ├── judges/             # LLM-as-judge scoring
│   │   ├── __init__.py
│   │   ├── judge.py        # Unified judge interface (GPT-4o as evaluator)
│   │   ├── rubric.py       # Scoring rubrics per task
│   │   └── templates/      # Judge prompt templates
│   └── cli.py              # CLI: naijaeval run, naijaeval leaderboard
├── leaderboard/            # Static site (HTML + CSS + JS, like your portfolio)
│   ├── index.html
│   ├── styles.css
│   ├── main.js
│   ├── data/               # Eval results as JSON
│   │   └── results.json
│   └── assets/
├── data/                   # Raw prompt datasets (also published to HuggingFace)
│   ├── pidgin_chat_v1.jsonl
│   ├── codeswitch_v1.jsonl
│   ├── factual_v1.jsonl
│   └── cultural_v1.jsonl
└── tests/
    ├── test_tasks.py
    ├── test_judge.py
    └── conftest.py
```

## Task definitions

### 1. Multi-turn chat quality (Pidgin)

**Structure:** Each prompt is a conversation script. Turn 1 = user message,
turn 2 = expected model response, turn 3 = user follow-up, etc. 5 turns
minimum. The judge evaluates the FULL conversation, not individual turns.

**Example prompt:**
```jsonl
{"id":"chat_001","turns":[{"role":"user","content":"Abeg how I fit send money to my sister for PH?"},{"role":"assistant","content":"..."},{"role":"user","content":"Okay but wetin be the charges? I no wan surprise."},{"role":"assistant","content":"..."},{"role":"user","content":"And if she no get bank account, she fit collect am for agent side?"}]}
```

**Scoring dimensions (1-5):**
- Coherence: responses relate to conversation history
- Helpfulness: actually solves the user's problem
- Naturalness: reads like real Pidgin, not translated English
- Turn handling: responds to each user message appropriately

### 2. Code-switching fluency

**Structure:** Mixed-language prompts testing whether models follow the
language switch naturally. Three subtypes:
- Code-switched input → model must respond appropriately
- Follow-the-user pattern (if user switches, model should match)
- Understanding idiomatic code-switched expressions

**Example:**
```jsonl
{"id":"cs_001","prompt":"Guy, I don try all the troubleshooting steps wey you talk. The thing still dey show 'connection timed out' but my WiFi dey work for other apps o. Any other idea?","expected_language":"pidgin","code_switches":4}
```

**Scoring dimensions (1-5):**
- Language matching: response language matches the user's pattern
- Switch comprehension: understands both languages in the input
- Idiom handling: correctly interprets Nigerian English + Pidgin expressions
- Naturalness: doesn't sound like a foreigner trying to "speak local"

### 3. Factual grounding

**Structure:** Questions with verifiable answers about Nigerian facts, asked
in Pidgin. Ground truth must be sourced (Wikipedia, government sites, news).

**Example:**
```jsonl
{"id":"fact_001","prompt":"Which year Nigeria gain independence and who be the first president?","answer":"Nigeria gained independence on October 1, 1960. Nnamdi Azikiwe became the first President in 1963 (Tafawa Balewa was first PM in 1960).","source":"https://en.wikipedia.org/wiki/Nigeria","tolerance":"accepts either Azikiwe or Balewa with correct context"}
```

**Scoring:** LLM-as-judge compares model response to ground truth. Binary
(correct/incorrect) + confidence score.

### 4. Cultural appropriateness

**Structure:** Open-ended prompts testing cultural awareness. No single
"right" answer — judged on cultural attunement.

**Example:**
```jsonl
{"id":"cult_001","prompt":"Explain why Nigerians dey kneel down or prostrate to greet elders.","dimensions":["cultural_context","respect_hierarchy","regional_variation"]}
```

**Scoring dimensions (1-5):**
- Cultural accuracy: gets the cultural facts right
- Contextual nuance: distinguishes regional/ethnic variations
- Avoids stereotyping: respectful, not caricatured
- Practical grounding: reads like someone who's been to Nigeria

## Scoring framework

### LLM-as-Judge

GPT-4o serves as the primary judge. Each model response is evaluated against
the rubric for its task. BabelJudge (2026) found LLM-as-a-judge works across
languages but needs careful calibration.

**Judge prompt structure:**
```
You are evaluating an AI assistant's response to a prompt in Nigerian Pidgin
and/or code-switched Nigerian English.

Prompt: {prompt}
Model response: {response}
Reference (if applicable): {reference}

Evaluate on these dimensions (score 1-5 for each):
{dimensions}

Return your evaluation as JSON:
{"scores": {"coherence": N, ...}, "explanation": "brief justification"}
```

### Metrics per task

| Task | Primary metric | Secondary |
|---|---|---|
| Chat quality | Mean Likert score (1-5) | Turn-level coherence |
| Code-switching | Mean Likert score | Switch comprehension rate |
| Factual grounding | Accuracy % | Confidence calibration |
| Cultural | Mean Likert score | Stereotype avoidance rate |

### Composite score

A weighted **NaijaScore** (0-100) combining all four tasks:
- Chat quality: 35%
- Code-switching: 25%
- Factual grounding: 20%
- Cultural appropriateness: 20%

## Leaderboard

Static site (like your portfolio) deployed to GitHub Pages at
`abiolaks.github.io/naijaeval`. Data in `leaderboard/data/results.json`.

**Features:**
- Model comparison table (NaijaScore + per-task breakdown)
- Filter by task
- Model card popups (which model, when evaluated, provider)
- Bar chart comparison (lightweight, Chart.js or vanilla canvas)
- Tokyo Night theme (brand consistency with your portfolio)

## Implementation plan

### Phase 1: Foundation (Week 1)
1. Scaffold Python package (`pyproject.toml`, directory structure)
2. Define lm-eval task classes for all 4 tasks
3. Create 50 pilot prompts per task (200 total) to validate the pipeline
4. Build the judge framework (GPT-4o)
5. End-to-end: run one model, get scores, verify they make sense

### Phase 2: Dataset (Weeks 1-2)
6. Expand Pidgin chat prompts to 150-200
7. Create 100-150 code-switching prompts (Pidgin/English)
8. Create 100-150 factual grounding prompts
9. Create 100-150 cultural appropriateness prompts
10. Native speaker review (find 1-2 reviewers on X/LinkedIn)

### Phase 3: Evaluation (Week 2-3)
11. Run eval on GPT-4o, Claude, Gemini, N-ATLaS, Aya, Africlaude, DeepSeek
12. Manual spot-check 10% of scores for judge calibration
13. Aggregate results into `results.json`

### Phase 4: Leaderboard (Week 3)
14. Build leaderboard site (HTML + CSS + JS)
15. Deploy to GitHub Pages
16. Write README with reproduction instructions

### Phase 5: Publication (Week 3-4)
17. Write comparison blog post ("GPT-4o vs Claude vs Gemini on Nigerian Pidgin")
18. Draft short paper for AfricaNLP / ACL workshop
19. Publish prompt dataset on HuggingFace
20. Post thread on X/LinkedIn

## Risks & mitigations

| Risk | Mitigation |
|---|---|
| LLM-as-judge unreliable for Pidgin | Human spot-check 10% of scores; publish judge agreement rate |
| TukaBench expands to cover Pidgin | Move fast; ship v1 in 4 weeks; pivot to multi-turn as stronger differentiator |
| Nobody uses it | Distribution matters more than code. Blog post + X thread + AfricaNLP paper |
| Prompt quality criticized | Open-source the prompts; invite community contributions via PRs |
| GPT-4o judge is expensive | Estimate: ~$50-100 to judge all prompts once. Acceptable. Document costs. |

## Success criteria

- [ ] 450+ prompts across 4 tasks, publicly available on HuggingFace
- [ ] 7+ models evaluated with published results
- [ ] lm-evaluation-harness integration (`lm_eval --tasks naijaeval:*`)
- [ ] Public leaderboard at `abiolaks.github.io/naijaeval`
- [ ] At least one blog post or comparison thread published
- [ ] AfricaNLP workshop submission (at minimum, arXiv preprint)
