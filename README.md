# NaijaEval

Open-source evaluation suite for **Nigerian Pidgin** and **code-switched
Nigerian English**, built as an [EleutherAI
lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)
extension.

## What it measures

| Task | What it tests |
|---|---|
| **Multi-turn chat** | Can the model hold a coherent, helpful 5-10 turn conversation in Pidgin? |
| **Code-switching fluency** | Does the model understand and respond naturally to mixed Pidgin/English and Yoruba/English? |
| **Factual grounding** | Does the model give factually correct answers about Nigeria in Pidgin? |
| **Cultural appropriateness** | Does the model show real Nigerian cultural awareness, or generic Western responses? |

## Why

Existing benchmarks (AfroBench, IrokoBench, TukaBench) cover classification,
NLI, math reasoning, and safety — but **nobody** measures multi-turn chat
quality, code-switching fluency, or general Pidgin competence. 75M+ Nigerians
speak Pidgin daily. Zero comprehensive eval tools exist for it.

## Quickstart

```bash
pip install -e .
naijaeval run --model openai/gpt-4o --tasks naijaeval:pidgin_chat
```

## Leaderboard

**https://abiolaks.github.io/naijaeval**

## Status

🚧 **Work in progress.** Spec + tickets written. Building now.
