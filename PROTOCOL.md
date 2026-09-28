**English** · [Português (Brasil)](PROTOCOL.pt-BR.md)

# GEO Measurement Protocol

Version 1.0.0, 2026-09-28. Author: Lucas Ferraz. License: CC BY 4.0.

This document defines a manual, reproducible procedure to observe whether a
brand or a URL is mentioned, recommended, cited as a source or linked by
AI answer surfaces. Every result produced with it is a dated observation of
what the surfaces answered, under declared conditions. It is not a forecast
and it does not explain why a surface answered the way it did.

## Contents

1. [Scope](#1-scope)
2. [What the protocol does not measure](#2-what-the-protocol-does-not-measure)
3. [Surfaces and channels](#3-surfaces-and-channels)
4. [Prompt set design](#4-prompt-set-design)
5. [Session neutralization](#5-session-neutralization)
6. [Rounds](#6-rounds)
7. [What counts: mention, recommendation, citation, link](#7-what-counts-mention-recommendation-citation-link)
8. [Refusals and unavailable answers](#8-refusals-and-unavailable-answers)
9. [Metrics and formulas](#9-metrics-and-formulas)
10. [Recording](#10-recording)
11. [Cadence and comparison windows](#11-cadence-and-comparison-windows)
12. [Threats to validity and how to report them](#12-threats-to-validity-and-how-to-report-them)
13. [Ethics and platform terms](#13-ethics-and-platform-terms)
14. [Using the protocol inside a benchmark](#14-using-the-protocol-inside-a-benchmark)
15. [Versioning of this protocol](#15-versioning-of-this-protocol)

## 1. Scope

The protocol measures one thing: for a fixed set of questions that a real
person would ask, which brands and which sources each AI surface returns,
and whether the brand under study is among them. The unit of observation is
the **round**: one question, asked once, on one surface, in one clean
session, with the answer read and classified.

It applies to any brand, product, person or URL, in any language and
market, as long as the prompt set, the surfaces, the location and the
language are declared before the first round.

## 2. What the protocol does not measure

- **Not a ranking forecast.** A high presence rate today says nothing about
  the position of a page in classic search results, and it does not predict
  future answers.
- **Not causality.** When a brand appears more often after a change to its
  site, the protocol records the co-occurrence. It does not prove that the
  change caused it. Model updates, index refreshes and third-party content
  change the answers at the same time.
- **Not traffic or revenue.** Being cited is not the same as receiving
  visits. Referral traffic from AI assistants is a separate measurement
  (see [ai-referrer-report](https://github.com/LucasFerrazSEO/ai-referrer-report),
  by the author).
- **Not a promise.** No result obtained with this protocol can be used to
  promise that a brand will be cited or recommended by any AI.

## 3. Surfaces and channels

The reference set has five surfaces (Google is read in two places). A study may use fewer, but it
must say which ones and why.

| Surface id | Surface | How it is read |
|---|---|---|
| `google_ai_mode` | Google AI Mode | Conversational answer in Google Search |
| `google_aio` | Google AI Overviews | Summary block on the results page, when shown |
| `gemini` | Gemini | Chat interface |
| `perplexity` | Perplexity | Answer engine with numbered sources |
| `chatgpt` | ChatGPT | Chat interface |
| `claude` | Claude | Chat interface with web search enabled |

For each round, record the **surface** and the **channel**:

- `channel`: how the answer was obtained. Suggested values: `browser`
  (manual reading in a web browser), `app` (official mobile or desktop
  app), `api` (an official API used under its terms), `serp_api` (a search
  results provider used under its terms, only for Google surfaces).
- `mode`: the model or mode shown by the interface when it is visible (for
  example a default mode, a reasoning mode, or a search mode). Keep the same
  mode across the rounds of one wave and record any change.
- `logged_in`: `yes` or `no`. If logged in, describe the account type in
  generic terms (for example "personal account, memory off"). Never record
  credentials or account identifiers in a public dataset.
- `web_search`: `yes` or `no`, for chat surfaces that can answer with or
  without searching the web. An answer produced without web search measures
  what the model recalls from training, which is a different phenomenon. Decide
  before the wave whether such answers count, and keep that rule for the
  whole series.

Answers obtained through an API are not equivalent to answers read in the
consumer interface: the API usually lacks the interface's search layer,
system instructions and personalization. Never mix channels inside one
series without flagging it.

## 4. Prompt set design

The prompt set is the instrument. A badly designed set measures the
brand's popularity with the person who wrote the prompts, not with the
surfaces.

1. **Start from intent.** Each prompt represents one intent of a real
   person. Two groups are recommended:
   - **decision prompts**: the person asks for a recommendation ("which
     company do you recommend for X in Y?"). These measure recommendation.
   - **informational prompts**: the person asks how something works or how
     to choose. These measure whether the brand's pages become sources.
2. **No brand in the prompt.** The prompt never contains the name of the
   brand under study or of any competitor. A prompt with the brand name
   measures entity recognition, which is a valid but separate test: keep it
   in its own group (for example `entity`) and never mix it into the
   recommendation rate.
3. **Real wording.** Write in the voice and language of the person, using
   wording taken from observed sources such as "People also ask" boxes,
   search autocomplete, related searches and forum questions. Record where
   each prompt came from.
4. **Wording variation.** For each intent, keep two or three phrasings
   (short, conversational, detailed). Answers change with wording, and a
   single phrasing overstates or understates presence.
5. **Location in the prompt only when the intent is local.** If the need is
   local ("in Belo Horizonte"), put the place in the prompt text. Do not
   rely on the IP location to carry intent.
6. **Fixed prompts for time series.** Once a prompt enters a series, its
   text never changes. A new question is appended with a new id and a date.
   A changed question is a new prompt, and the old series ends there
   (record `series_break: yes` if you still want to compare them).
7. **Ids and versions.** Every prompt has a stable id (`P01`, `P02`...) and
   the set has a version (`v1`, `v2`...). Every round records both. The
   verbatim text of every prompt must be recoverable from its id.
8. **Size.** There is no universal minimum. Report the number of prompts,
   rounds and valid rounds with every result (section 12).

Use [templates/prompt-set-template.md](templates/prompt-set-template.md) to
write the set.

## 5. Session neutralization

Without neutralization, the answer reflects the account of the person who
measures (memory, history, custom instructions, previous chats), and the
test measures that person, not the prospective customer. Before every
round:

1. **Clean session.** Open a new conversation. Use the temporary or
   incognito chat mode when the surface offers one. On answer engines that
   keep context inside a thread, open a new thread, never a follow-up. On
   Google surfaces, use a private browser window.
2. **No history, memory or project.** Turn memory off where the surface
   allows it, do not use projects, custom assistants or custom
   instructions, and never measure inside a working session that already
   holds context about the brand. Logging out is an option where the surface
   still answers without login; record it.
3. **Neutralization preamble.** Paste a fixed preamble before the prompt.
   Suggested text (write your own if needed, but keep it fixed and
   versioned):

   > Disregard any memory, conversation history, personalization or custom
   > instructions about me. Answer this question from scratch, as you would
   > for a new user you know nothing about.

   The preamble is the only text that may differ between surfaces (for
   example, when one surface reacts badly to it). The question itself never
   varies across surfaces. A new preamble version is tested before
   adoption (section 15) and its version is recorded in every round.
4. **Declared location.** Record the country and, when relevant, the city
   from which the rounds were run, and any search parameters used for
   Google surfaces (country and interface language). Keep them fixed for the
   series.
5. **Declared language.** Prompt language and interface language are
   recorded. Measure in the language of the market under study.

A round run without neutralization is recorded as `status:
not_neutralized` and discarded from all metrics.

## 6. Rounds

- **Three rounds per prompt per surface.** Each round is a new clean
  session. For Google AI Overviews and AI Mode, each round is a new private
  window and a new search.
- **Why three.** AI answers vary between runs of the same question on the
  same surface. One run is an anecdote. Three is the smallest odd number
  that allows a majority reading: a result is `stable` when it repeats in at
  least 2 of 3 rounds, and `oscillating` otherwise. More rounds improve
  precision and are welcome, but the protocol requires at least three.
- **Time window.** All rounds of one wave (every prompt, every surface) are
  run inside a declared window, recommended at 48 hours or less, so that a
  model update does not split the wave. Record start and end timestamps.
- **Order.** Alternate surfaces and prompts instead of running the same
  prompt three times in a row on one surface, to reduce the chance that
  short-lived caching repeats the same answer.
- **Scoreboard notation.** The per-prompt scoreboard is written `X/N`, where
  `N` is the number of valid rounds (3 per surface, times the surfaces with
  valid rounds) and `X` the rounds in which the brand met the criterion
  being reported. With four chat surfaces, the full denominator is 12. With
  AI Mode added, 15.

## 7. What counts: mention, recommendation, citation, link

Each round is classified per brand in the comparison set, with four
independent flags. A brand can have several flags in the same round.

| Flag | Definition | Test |
|---|---|---|
| `mentioned` | The brand name appears anywhere in the answer text. | Name present in the visible answer, in any context, including negative. |
| `recommended` | The answer presents the brand as an option for the person's need. | Brand in a list of suggestions, or phrased as "consider", "you could hire", "a good option is". A mention in a warning or a comparison that advises against it is not a recommendation. |
| `cited_source` | A URL of the brand's domain appears among the sources the surface attributes to the answer. | Footnote, numbered source, source card or source panel. Record the exact URL. |
| `linked` | The answer body contains a clickable link to the brand's domain. | Inline link in the text, separate from the sources panel. |

Rules:

- `recommended` implies `mentioned`. `cited_source` does not imply
  `mentioned`: a surface can use a page as a source without naming the
  brand. Record this case explicitly; it shows a page that feeds the answer
  without turning into a recommendation.
- **Position** is the ordinal of the brand's first appearance among all
  brands named in the answer (1 = first brand named). Leave it empty when
  the brand is not mentioned. When the answer has no ordering (for example
  map cards shown side by side), record the reading order and note it.
- **Verbatim reason.** When the brand is recommended or explicitly
  discarded, copy the reason the surface gave, word for word, into the
  notes. It is the most actionable part of the record.
- **Services named.** Optionally record whether the specific product or
  service related to the prompt was named, or only the brand in general.
- **Brand spelling.** Normalize every brand to one canonical spelling in
  the record, with a separate note of the variant seen.

## 8. Refusals and unavailable answers

Each round gets a `status`:

| Status | Meaning | Counts in the denominator? |
|---|---|---|
| `ok` | Answer obtained and classified. | Yes |
| `n/d (refusal)` | The surface refused or failed technically: empty answer, answer deleted, generic "I can't help with that", error message. Recorded as `nd_refusal` in the CSV. | **No** |
| `not_obtained` | The surface could not be reached in the session (login wall, usage limit, outage). | No |
| `not_shown` | Google AI Overviews did not appear for that search. | No for brand rates; yes for the trigger rate (section 9) |
| `not_neutralized` | Neutralization failed or was forgotten. | No, the round is discarded |

A refusal is never recorded as "brand absent". If all three rounds of a
surface are refusals, the surface leaves that prompt's denominator and the
scoreboard is labelled accordingly (for example `2/9, without Gemini:
refusal`). Report the count of refused rounds per surface with every result.

## 9. Metrics and formulas

Notation, for a given prompt set, wave and surface `s` (or all surfaces
together):

- `V` = number of valid rounds (`status = ok`).
- `M(b)` = valid rounds in which brand `b` is `mentioned`.
- `R(b)` = valid rounds in which `b` is `recommended`.
- `C(b)` = valid rounds in which `b` is `cited_source`.
- `L(b)` = valid rounds in which `b` is `linked`.
- `B` = the comparison set of brands, declared before the wave.

**Presence rate (mention rate)**

    presence(b) = M(b) / V

**Recommendation rate**

    recommendation(b) = R(b) / V

**Source citation rate**

    citation(b) = C(b) / V

**Link rate**

    link(b) = L(b) / V

**Share of voice** (share of all brand mentions in the comparison set)

    SoV(b) = M(b) / sum of M(k) for every k in B

Share of voice depends on the comparison set: adding or removing a brand
changes every value. Always publish `B` together with `SoV`. A recommended-
only variant uses `R` instead of `M`.

**Average position** (only over rounds where `b` was mentioned)

    avg_position(b) = sum of position(b, round) / M(b)

**Stability** (per prompt and surface): `stable` if the same outcome for
the brand (present or absent) occurs in at least 2 of the 3 valid rounds,
`oscillating` otherwise. With fewer than 2 valid rounds, `insufficient`.

**AI Overviews trigger rate**

    aio_trigger = rounds with AIO shown / (rounds with AIO shown + rounds not_shown)

**Source share by domain** (optional): for all sources listed in valid
rounds, the fraction that belongs to each domain. Report the total number of
sources and the top domains.

**Uncertainty.** With few rounds, a rate is imprecise. Report a 95% Wilson
score interval for every rate, where `p = x / n`, `z = 1.96`:

    center = (p + z^2 / (2n)) / (1 + z^2 / n)
    margin = (z / (1 + z^2 / n)) * sqrt( p(1 - p)/n + z^2 / (4n^2) )
    interval = [center - margin, center + margin]

Example: 3 of 12 valid rounds gives p = 0.25 and an interval of roughly
0.09 to 0.53. The width of the interval is the honest answer to "how sure
are we".

### Compatibility with citation-round-logger

[citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger)
(by the author) stores, per round, the surface (`ia`), the prompt, and for
each brand whether it was mentioned (`mencionada`) and its position
(`posicao`). The percentage it prints as "share of voice" per brand and per
AI is `M(b) / V` for that surface, which this protocol calls the **presence
rate**. When you use both, label the logger figure as presence rate, and
compute `SoV(b)` as defined above from the same records. Refused rounds
(`nd_refusal`) must not be logged in the logger, because the logger has no
refusal status and would count them as absences.

## 10. Recording

- **Round log.** One row per brand per round, in
  [templates/rounds-template.csv](templates/rounds-template.csv). The first
  six columns (`data`, `ia`, `prompt`, `marca`, `mencionada`, `posicao`)
  carry the same names and meaning as the fields of citation-round-logger,
  so every group of rows with the same `round_id` becomes one logger record
  (one `--marca` and `--posicao` pair per row with `mencionada = true`, or
  `--ausente` when no brand appeared). The other columns hold what this
  protocol adds.
- **Raw answers.** Save the full text of every answer in a separate file
  named by `round_id`, and store the path in `response_file`. Classification
  can be audited only if the raw text exists.
- **Prompt set.** Keep the versioned prompt set next to the log, written
  with [templates/prompt-set-template.md](templates/prompt-set-template.md).
- **Wave header.** For each wave, record protocol version, prompt set
  version, preamble version, surfaces, channels, location, language, window
  start and end, and the person or tool that read the answers.
- **Anonymization.** In a public dataset, the brands of third parties can
  be replaced by stable codes (`Brand 01`, `Brand 02`) when the study does
  not need their names. Never publish account identifiers.

## 11. Cadence and comparison windows

- Decision prompts for key pages: one wave every 30 days is a practical
  cadence. Other prompts: at publication, at each update, and every 90 days.
- **A change is declared only between windows**, comparing waves at least 2
  to 4 weeks apart, never from a single round or a single wave.
- Before a change to the measured page, run a baseline wave. After it,
  schedule the follow-up wave 2 to 4 weeks later.
- A difference smaller than the overlap of the Wilson intervals of the two
  waves is not reported as a change.

## 12. Threats to validity and how to report them

| Threat | Why it matters | Mitigation and what to report |
|---|---|---|
| Personalization | Account memory and history steer answers. | Section 5 in every round; report `logged_in`, memory setting and preamble version. |
| Model variance | The same question gets different answers. | Three rounds minimum, stability label, Wilson intervals. |
| Version and mode changes | Surfaces update models and features without notice. | Short wave window, record `mode` and dates, flag waves that straddle a visible change as not comparable. |
| Location | Local intent and search results depend on location. | Declare country, city and search parameters; keep them fixed; put local intent in the prompt text. |
| Small sample | Few prompts and rounds give wide intervals. | Publish number of prompts, rounds, valid rounds and refusals next to every rate. |
| Prompt selection bias | Prompts written to favor the brand inflate presence. | No brand in the prompt; prompts derived from observed demand; publish the full set. |
| Channel mixing | API, app and browser differ. | One channel per series or explicit flag. |
| Reader error | Manual classification can be wrong. | Keep raw answers; a second reader on a sample of rounds; record disagreements. |

Every published result should carry a line in this form:

    Protocol v1.0.0 | prompt set v1 (N prompts) | surfaces: ... | channel: ... |
    location: ... | language: ... | window: YYYY-MM-DD to YYYY-MM-DD |
    valid rounds: V of T (refusals: r) | reader: manual

## 13. Ethics and platform terms

- Measure manually in the normal interfaces, or with official APIs and
  tools that the platforms allow, always under their current terms of use.
- Do not use scrapers, bots or automation that the platform's terms forbid.
  Do not bypass login walls, usage limits or CAPTCHAs.
- Do not create fake accounts to multiply rounds.
- Do not publish personal data seen in answers.
- Do not present the results as a guarantee of citation, recommendation or
  ranking, to clients or to the public.

## 14. Using the protocol inside a benchmark

A segment benchmark can pair this protocol (part B: who the AI surfaces
recommend when a customer asks for a provider in the segment) with an audit
of public technical signals on the sites of the same businesses (part A:
for example robots.txt rules for AI crawlers, structured data, content
present in the initial HTML). The crossing of A and B is reported only as a
descriptive pattern ("the businesses recommended in the rounds tend to have
X"), with sample size and date, never as cause. If the overlap between the
two parts is too small, present them side by side without crossing.

## 15. Versioning of this protocol

- The protocol follows semantic versioning. **Major**: a change that breaks
  comparability with earlier results (for example, a new counting rule).
  **Minor**: an addition that does not change existing metrics. **Patch**:
  wording and clarifications.
- Every round records the protocol version in `protocol_version`.
- A change of channel, account setup or preamble version inside a running
  series is adopted only after a parallel test: three prompts, three rounds
  each, passing when at least 2 of 3 rounds are valid in at least 2 of the 3
  prompts. Results measured before the change are not compared as the same
  condition.

## Related

- [citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger), by the author: command-line log of rounds.
- [ai-referrer-report](https://github.com/LucasFerrazSEO/ai-referrer-report), by the author: AI assistant referral traffic from GA4 exports.
- [awesome-generative-engine-optimization](https://github.com/LucasFerrazSEO/awesome-generative-engine-optimization), by the author: curated list on Generative Engine Optimization.
- [Método Rodada de Citação](https://lucasferrazseo.com/glossario/metodo-rodada-de-citacao/), glossary entry in Portuguese at the author's agency.
- "GEO: Generative Engine Optimization", KDD 2024 ([arXiv:2311.09735](https://arxiv.org/abs/2311.09735)), for background on the field.
