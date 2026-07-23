# NaijaEval — Judge Rubrics

Each task has a scoring rubric that the LLM judge (GPT-4o by default) applies
per response. Scores are on a 1-5 Likert scale with dimension-specific anchors.

## 1. Multi-turn chat quality (Pidgin)

Evaluate the FULL conversation, not individual turns.

| Dimension | 1 | 3 | 5 |
|---|---|---|---|
| **Coherence** | Responses are disconnected from conversation history; model loses track of what was said | Mostly follows the thread but occasionally misses context or repeats itself | Every response builds naturally on the conversation; model tracks all prior turns accurately |
| **Helpfulness** | Gives useless or incorrect information; doesn't solve the user's problem | Partially helpful but misses key details or gives vague advice | Directly solves the user's stated and implicit needs; actionable and specific |
| **Naturalness** | Sounds like English translated word-for-word; uses wrong grammar or unnatural phrasing | Mostly natural but has awkward formulations a native speaker would catch | Reads like a real Lagos Pidgin speaker wrote it |
| **Turn handling** | Ignores or misinterprets specific user questions within turns | Addresses most but not all parts of multi-part messages | Responds to every part of the user's turn appropriately |

## 2. Code-switching fluency

| Dimension | 1 | 3 | 5 |
|---|---|---|---|
| **Language matching** | Responds in pure English when user mixes languages; doesn't match the pattern | Partially matches but reverts to English or gets the mix ratio wrong | Response language pattern mirrors the input naturally |
| **Switch comprehension** | Misunderstands words in the non-English language; treats them as errors | Catches common words but misses idioms or nuanced expressions | Accurately interprets every code-switched term, including idiomatic ones |
| **Idiom handling** | Treats Nigerian idioms literally or ignores them | Recognizes some idioms but misses the emotional tone or irony | Correctly interprets idiomatic meaning AND emotional register (e.g., sarcasm, annoyance, humor) |
| **Naturalness** | Sounds like a foreigner "trying" to speak local; forced | Acceptable but not effortless | Reads like someone who grew up switching between these languages daily |

## 3. Factual grounding

| Dimension | 1 | 3 | 5 |
|---|---|---|---|
| **Accuracy** | Answer is factually wrong | Partially correct but misses nuance or secondary facts | Fully correct, with appropriate detail and context |
| **Completeness** | Ignores key parts of the question | Covers main point but misses sub-questions | Addresses all aspects of the question fully |
| **Source awareness** | Makes claims with no reference; could be hallucinated | Generally accurate but no specific sourcing | Cites specific dates, names, or references that are verifiable |
| **Language handling** | Fails to understand the Pidgin question; answers a different question | Understands mostly but misses nuance | Understands the Pidgin question fully and answers it directly |

**Binary score:** 1 = incorrect (< 50% accuracy), 0 = correct (≥ 50% accuracy)

## 4. Cultural appropriateness

| Dimension | 1 | 3 | 5 |
|---|---|---|---|
| **Cultural accuracy** | Gets cultural facts wrong; invents traditions | Mostly correct but lacks detail or precision | Demonstrates genuine cultural knowledge with specific details |
| **Contextual nuance** | Treats Nigerian culture as monolithic; no regional/ethnic distinction | Acknowledges diversity but doesn't explain variations | Distinguishes between ethnic groups, regions, and traditions with precision |
| **Avoids stereotyping** | Uses stereotypes, exotifies, or is patronizing | Neutral but shallow; doesn't critically engage | Respectful, informed, pushes back on stereotypes where relevant |
| **Practical grounding** | Reads like a textbook written by someone who's never been to Nigeria | Some real-world texture but mostly abstract | Reads like someone who has lived in Nigeria; includes practical, lived-experience details |
