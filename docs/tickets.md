# NaijaEval — Tickets

## Ticket 1: Project scaffolding
**Estimate:** 2-3 hours

- [ ] Create repo `abiolaks/naijaeval` on GitHub
- [ ] `pyproject.toml` with `lm-eval` dependency, Python 3.11+
- [ ] Directory structure: `naijaeval/`, `leaderboard/`, `data/`, `tests/`
- [ ] `README.md` with pitch and quickstart
- [ ] `.gitignore`, `LICENSE` (MIT)
- [ ] Installable: `pip install -e .` succeeds
- [ ] `naijaeval --help` prints usage

## Ticket 2: Prompt dataset — v1 pilot (50 per task)
**Estimate:** 1 week (heavy — writing prompts by hand)

- [ ] `data/pidgin_chat_v1.jsonl` — 50 multi-turn conversation scripts, 5+ turns each
- [ ] `data/codeswitch_v1.jsonl` — 50 code-switched prompts (Pidgin/English, Yoruba/English)
- [ ] `data/factual_v1.jsonl` — 50 factual questions about Nigeria, with ground truth + source
- [ ] `data/cultural_v1.jsonl` — 50 cultural awareness prompts
- [ ] Each prompt has: `id`, prompt/content, expected_language, metadata
- [ ] Peer review by at least one native Pidgin speaker

## Ticket 3: lm-evaluation-harness task classes
**Estimate:** 4-6 hours

- [ ] `naijaeval/tasks/pidgin_chat/task.py` — extends `lm_eval.api.task.Task`
- [ ] `naijaeval/tasks/codeswitch/task.py`
- [ ] `naijaeval/tasks/factual/task.py`
- [ ] `naijaeval/tasks/cultural/task.py`
- [ ] Each task loads its JSONL prompts, formats into lm-eval doc format
- [ ] Registered so `lm_eval --tasks naijaeval:pidgin_chat` works
- [ ] Tests: `tests/test_tasks.py` — verify doc construction, prompt count

## Ticket 4: Judge framework (GPT-4o)
**Estimate:** 4-6 hours

- [ ] `naijaeval/judges/judge.py` — `evaluate(response, rubric)` → `{scores, explanation}`
- [ ] `naijaeval/judges/rubric.py` — rubric definitions per task (dimensions + 1-5 scale)
- [ ] `naijaeval/judges/templates/` — judge prompt templates in Pidgin + English
- [ ] Configurable judge model (default GPT-4o, support Claude via env vars)
- [ ] Retry logic, rate limiting, cost tracking
- [ ] Tests: `tests/test_judge.py` — known responses, expected scores

## Ticket 5: CLI — `naijaeval run`
**Estimate:** 3-4 hours

- [ ] `naijaeval/cli.py` with `click` or `argparse`
- [ ] `naijaeval run --model MODEL --tasks TASK1,TASK2` — runs eval end-to-end
- [ ] Outputs JSON results to `results/{model}_{timestamp}.json`
- [ ] Progress bar, cost estimation
- [ ] `naijaeval aggregate` — merges results into `leaderboard/data/results.json`

## Ticket 6: Expand prompt dataset to full v1 (150-200 per task)
**Estimate:** 1 week

- [ ] Expand Pidgin chat to 150+ prompts
- [ ] Expand code-switching to 100+ prompts
- [ ] Expand factual grounding to 100+ prompts
- [ ] Expand cultural appropriateness to 100+ prompts
- [ ] Total: 450+ prompts
- [ ] Publish to HuggingFace as `abiolaks/naijaeval-prompts`

## Ticket 7: Run full evaluation on 7+ models
**Estimate:** 2-3 days (mostly API calls + judge processing)

- [ ] GPT-4o
- [ ] Claude Opus 4 or Sonnet 5
- [ ] Gemini 2.5 Pro
- [ ] N-ATLaS-LLM
- [ ] Aya Expanse
- [ ] Africlaude-7B
- [ ] DeepSeek V3
- [ ] Manual spot-check 10% of judge scores for calibration
- [ ] Publish judge agreement rate
- [ ] Generate `leaderboard/data/results.json`

## Ticket 8: Leaderboard site
**Estimate:** 1-2 days

- [ ] `leaderboard/index.html` — Tokyo Night theme, model comparison table
- [ ] `leaderboard/styles.css` — consistent with your portfolio
- [ ] `leaderboard/main.js` — renders from `data/results.json`, bar charts (Chart.js)
- [ ] Filter by task, sort by score
- [ ] Model card popups
- [ ] Deploy to GitHub Pages at `abiolaks.github.io/naijaeval`
- [ ] Mobile responsive

## Ticket 9: Documentation & distribution
**Estimate:** 1-2 days

- [ ] README: pitch, quickstart, architecture diagram, reproduction steps
- [ ] Blog post: "GPT-4o vs Claude vs Gemini on Nigerian Pidgin — Guess Who Wins?"
- [ ] X/LinkedIn comparison thread with screenshots
- [ ] HuggingFace dataset card for the prompts
- [ ] CONTRIBUTING.md (how to add prompts/languages)

## Ticket 10: Paper & publication
**Estimate:** 3-5 days

- [ ] Draft 3-4 page paper in ACL format
- [ ] Target: AfricaNLP workshop at ACL 2027 or EMNLP 2026
- [ ] ArXiv preprint as backup
- [ ] Cover: motivation, dataset, methodology, results, limitations, future work

---

## Dependency graph

```
Ticket 1 (scaffold) ──┬── Ticket 2 (pilot prompts) ── Ticket 6 (full prompts)
                      ├── Ticket 3 (lm-eval tasks)
                      └── Ticket 4 (judge) ────────── Ticket 5 (CLI)
                                                              │
                      Ticket 6 (full prompts) ─┬── Ticket 7 (run eval)
                                                └── Ticket 8 (leaderboard)
                                                              │
                                                Ticket 9 (docs + distribution)
                                                              │
                                                Ticket 10 (paper)
```

## Parallel opportunities

- Tickets 2, 3, 4 can run in parallel after Ticket 1
- Ticket 8 (leaderboard) can start as soon as results.json schema is defined
- Ticket 10 (paper) can be drafted while evaluation runs (Ticket 7)
