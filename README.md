**English** · [Português (Brasil)](README.pt-BR.md)

# geo-measurement-protocol

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](LICENSE) ![Protocol version 1.0.0](https://img.shields.io/badge/protocol-v1.0.0-blue.svg)

`geo-measurement-protocol` is an open, reproducible protocol for measuring
whether a brand or a URL is mentioned, recommended, cited as a source or
linked by AI answer surfaces: Google AI Mode, Google AI Overviews, Gemini,
Perplexity, ChatGPT and Claude. It defines how to write the prompt set, how
to neutralize the session, how many rounds to run, what counts as a
citation, how to treat refusals and how to calculate each metric, with
explicit formulas. It is a text protocol with recording templates, not
software, and it does not query any AI for you.

## Contents

- [Background](#background)
- [What the protocol covers](#what-the-protocol-covers)
- [Files](#files)
- [How to use it](#how-to-use-it)
- [FAQ](#faq)
- [Limitations](#limitations)
- [Methodology](#methodology)
- [Related projects](#related-projects)
- [How to cite](#how-to-cite)
- [Contributing](#contributing)
- [Author](#author)
- [License](#license)

## Background

Answers from AI surfaces change between runs, between accounts and between
model versions. A screenshot of one answer proves nothing about how often a
brand is recommended. To compare over time, or to compare brands, the
measurement has to be repeatable: the same fixed questions, asked in clean
sessions, several times per surface, classified with the same rules and
recorded in the same format. This protocol writes those rules down so that
anyone can run the same measurement and reach a comparable result.

## What the protocol covers

1. **Scope, and what it does not measure.** It is not a ranking forecast,
   not proof of cause, not traffic, and not a promise of citation.
2. **Surfaces and channels.** Six surface ids and how to record the channel
   (browser, app, API), mode, login state and web search.
3. **Prompt set design.** Intent first, no brand in the prompt, wording
   variation, fixed and versioned prompts for time series.
4. **Session neutralization.** Clean session, no history or memory, a fixed
   and versioned preamble, declared location and language.
5. **Rounds.** Three rounds per prompt per surface, inside a short time
   window, with a 2-of-3 stability rule.
6. **Classification.** Four independent flags: mention, recommendation,
   source citation and link, plus position and the verbatim reason.
7. **Refusals.** A technical refusal is `n/d (refusal)` and leaves the
   denominator; it is never counted as "brand absent".
8. **Metrics.** Presence rate, recommendation rate, citation rate, link
   rate, share of voice, average position, stability, AI Overviews trigger
   rate and Wilson intervals, each with its formula.
9. **Threats to validity, ethics and versioning.**

## Files

| File | Content |
|---|---|
| [PROTOCOL.md](PROTOCOL.md) | The full protocol, in English |
| [PROTOCOL.pt-BR.md](PROTOCOL.pt-BR.md) | The full protocol, in Brazilian Portuguese |
| [templates/rounds-template.csv](templates/rounds-template.csv) | Round log, one row per brand per round, with fictitious example rows |
| [templates/prompt-set-template.md](templates/prompt-set-template.md) | Template for a versioned prompt set (English) |
| [templates/prompt-set-template.pt-BR.md](templates/prompt-set-template.pt-BR.md) | Same template in Brazilian Portuguese |
| [CITATION.cff](CITATION.cff) | Citation metadata |

The CSV column names are the same in both languages, so a single file
serves both. The first six columns (`data`, `ia`, `prompt`, `marca`,
`mencionada`, `posicao`) keep the field names of
[citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger),
which is why they are in Portuguese.

## How to use it

**1. Read the protocol** ([PROTOCOL.md](PROTOCOL.md)) once, end to end.

**2. Write the prompt set** with
[templates/prompt-set-template.md](templates/prompt-set-template.md):
decision prompts and informational prompts, no brand names, each with a
stable id. Declare the comparison set of brands, the surfaces, the location
and the language before the first round.

**3. Run a wave.** Three rounds per prompt per surface, each in a new clean
session with the neutralization preamble, all inside a short window.

**4. Record every round** in
[templates/rounds-template.csv](templates/rounds-template.csv) and save the
raw text of every answer.

**5. Calculate the metrics** with the formulas in section 9 of the protocol,
and publish them with the reporting line from section 12 (protocol version,
prompt count, valid rounds, refusals, window, location).

**6. Repeat** at a fixed cadence and compare waves at least 2 to 4 weeks
apart, never single rounds.

If you prefer a command line to a spreadsheet, the same rounds can be logged
with [citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger)
(by the author). Section 9 of the protocol explains how its numbers map to
the metrics defined here.

## FAQ

**Does this protocol guarantee that my brand will be cited by an AI?**
No. It measures what the surfaces answered on given dates, under declared
conditions. It does not control, predict or guarantee any answer.

**Why three rounds and not one?**
Because the same question gets different answers on the same surface. One
run is an anecdote. Three is the smallest odd number that allows a majority
reading (stable when the result repeats in at least 2 of 3 rounds).

**Why is a refusal kept out of the denominator?**
A refusal says nothing about whether the brand would have been named.
Counting it as an absence would lower the brand's rate for a reason
unrelated to the brand.

**Can I automate the rounds?**
Only with official APIs or tools that the platforms allow, under their
terms, and without bypassing logins, usage limits or CAPTCHAs. API answers
differ from the consumer interface, so keep them in a separate series.

**Is share of voice the same as presence rate?**
No. Presence rate is the fraction of valid rounds in which a brand appears.
Share of voice is the brand's fraction of all brand mentions in the
comparison set, so it changes when you add or remove a brand.

## Limitations

The protocol depends on careful manual reading and classification. It
cannot remove the variance of the models, only make it visible. Small
prompt sets give wide intervals. Results obtained in one location,
language, account setup or channel do not transfer to another. Surfaces
change their interfaces and features, and a future version of the protocol
may need new rules for them.

## Methodology

The protocol comes from the measurement routine used internally since 2026
at [lucasferrazseo.com](https://lucasferrazseo.com) to measure citation and
recommendation by AI, generalized here for public use, with no client data,
no internal measurements and no site-specific rules.

## Related projects

- [citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger), by the author: command-line log of citation rounds.
- [ai-referrer-report](https://github.com/LucasFerrazSEO/ai-referrer-report), by the author: referral traffic from AI assistants in GA4 exports.
- [awesome-generative-engine-optimization](https://github.com/LucasFerrazSEO/awesome-generative-engine-optimization), by the author: curated list on Generative Engine Optimization.
- [Método Rodada de Citação](https://lucasferrazseo.com/glossario/metodo-rodada-de-citacao/): public glossary entry, in Portuguese, at the author's agency.

## How to cite

Use the metadata in [CITATION.cff](CITATION.cff), or:

> Ferraz, L. (2026). *GEO Measurement Protocol* (Version 1.0.0). https://github.com/LucasFerrazSEO/geo-measurement-protocol

Always cite the protocol version used in your measurement.

## Contributing

Suggestions, corrections and reports of how the protocol worked in your
measurements are welcome through [GitHub Issues](https://github.com/LucasFerrazSEO/geo-measurement-protocol/issues).

## Author

[Lucas Ferraz](https://lucasferraz.com) is an SEO, website development and Generative Engine Optimization specialist and the founder of [Lucas Ferraz SEO](https://lucasferrazseo.com).

## License

Text and templates under Creative Commons Attribution 4.0 International (CC BY 4.0). See [LICENSE](LICENSE).
