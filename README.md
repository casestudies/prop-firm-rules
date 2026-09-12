# Prop-firm challenge rule corpus — public dataset showcase

A verified rule corpus for proprietary-trading-firm evaluation programs: profit
targets, daily loss limits, drawdown mechanics, consistency rules, payout terms
and pricing, each field sourced from the firm's own published documentation with
a retrieval date and a confidence label attached.

This repository (`casestudies/prop-firm-rules`) is a showcase of that corpus,
not the corpus itself — see **What's not here** below.

## What's here

| Path | Contents |
|---|---|
| `SCHEMA.md` | field definitions and enum vocabularies for every field this corpus records |
| `METHODOLOGY.md` | how a value gets sourced, disclosed when ambiguous, and re-checked |
| `firms/showcase/*.json` | five firms in full detail — values, provenance quotes, retrieval and last-checked dates, every account size tracked |
| `firms/metadata.json` (+ `firms/metadata.md`) | firm name, cited-field count, last-checked date and verification-coverage flags for all 59 tracked firms — no rule values |
| `LICENSE.md` | CC BY 4.0 license declaration (short form, links to the full legal code) |
| `ATTRIBUTION.md` | how to credit this dataset if you use it |

## The five showcase firms

Picked for name recognition and mechanic diversity — together they cover every
drawdown basis this corpus records (intraday equity, end-of-day balance, and
closed-trade balance), a consistency rule, a stated no-daily-loss-limit product,
and a two-step evaluation:

- **FTMO** (`firms/showcase/ftmo.json`) — 2-step evaluation, static drawdown. 3 account size(s), last checked 2026-07-31.
- **Apex Trader Funding** (`firms/showcase/apex.json`) — intraday-trailing drawdown, no daily loss limit. 4 account size(s), last checked 2026-07-22.
- **E8 Markets** (`firms/showcase/e8-markets.json`) — closed-balance trailing drawdown. 8 account size(s), last checked 2026-07-30.
- **Topstep** (`firms/showcase/topstep.json`) — consistency rule, end-of-day trailing drawdown. 3 account size(s), last checked 2026-07-31.
- **MyFundedFutures** (`firms/showcase/myfundedfutures.json`) — no daily loss limit (stated by the firm), end-of-day trailing drawdown. 2 account size(s), last checked 2026-07-22.

## What's not here

This repository does not contain: the full value matrix for all 59
tracked firms, per-account-size figures for any firm beyond the five above, the
complete source-quote bank, the golden-vector correctness suite, any simulation
or build tooling, or anything from the operating notes behind this project. The
metadata table lists which firms are tracked and how thoroughly — never what
their rules are.

**The complete, current dataset — every firm, every account size, every cited
field — lives inside the free simulator at https://propsurvival.com/.** Related
reading:

- Firm rules, by firm and mechanic: https://propsurvival.com/firm-rules/index.html
- Apex's trailing drawdown, in detail: https://propsurvival.com/apex-trailing-drawdown.html
- Topstep's consistency rule, in detail: https://propsurvival.com/topstep-consistency-rule.html
- This corpus's full methodology, on-site: https://propsurvival.com/research-methodology.html

## License

CC BY 4.0 — see `LICENSE.md` and `ATTRIBUTION.md`.

## Citation

PropSurvival. Prop-firm challenge rule corpus (public dataset showcase) [Dataset]. 59 tracked firms, 5 shown in full detail, 243 account-size configurations in the complete corpus. Retrieved from https://github.com/casestudies/prop-firm-rules

---

This snapshot is dated 2026-08-17. Generated from a private, verified rule corpus; the generation tooling is not part of this repository.
