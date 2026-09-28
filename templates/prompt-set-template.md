**English** · [Português (Brasil)](prompt-set-template.pt-BR.md)

# Prompt set: {segment or page}

Template for a fixed prompt set under the [GEO Measurement Protocol](../PROTOCOL.md).
Replace everything inside braces. Delete this paragraph when the set is ready.

## Header

| Field | Value |
|---|---|
| Prompt set version | v1 |
| Created | {YYYY-MM-DD} |
| Protocol version | 1.0.0 |
| Brand under study | {canonical brand name} |
| Comparison set (B) | {Brand 01, Brand 02, ...} (declared before the first wave) |
| Surfaces | {google_ai_mode, google_aio, gemini, perplexity, chatgpt, claude} |
| Channel | {browser / app / api / serp_api} |
| Location | {country, city, search parameters} |
| Language | {prompt language, interface language} |
| Preamble version | {v1} |
| Rounds per prompt per surface | 3 |
| Answers without web search count? | {yes / no} |

## Neutralization preamble (version {v1})

> {fixed preamble text, pasted before every prompt}

## Decision prompts (measure recommendation)

No brand names in any prompt. Write in the voice of the person who buys.

| Id | Intent | Prompt (verbatim, never edited) | Wording source | Added |
|---|---|---|---|---|
| P01 | {intent} | {prompt text} | {PAA / autocomplete / related / forum / customer interview} | {YYYY-MM-DD} |
| P02 | {same intent, second phrasing} | {prompt text} | {source} | {YYYY-MM-DD} |

## Informational prompts (measure source citation)

| Id | Intent | Prompt (verbatim, never edited) | Wording source | Page that should be cited | Added |
|---|---|---|---|---|---|
| P10 | {intent} | {prompt text} | {source} | {URL} | {YYYY-MM-DD} |

## Entity prompts (optional, reported separately)

These contain the brand name and measure entity recognition only. They are
never part of the recommendation rate.

| Id | Prompt (verbatim) | Added |
|---|---|---|
| E01 | {Who is {brand}?} | {YYYY-MM-DD} |

## Rules for this set

- A prompt's text never changes. A new question gets a new id and a date.
- A retired prompt stays in the table with `retired: YYYY-MM-DD`.
- A change that breaks comparability creates a new set version (v2) and is
  recorded in the history below.

## History

| Version | Date | Change | Comparability |
|---|---|---|---|
| v1 | {YYYY-MM-DD} | First version | Baseline |
