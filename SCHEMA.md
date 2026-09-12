# Schema

One JSON record per (firm, account size) — the same shape as the five full-detail
showcase files in `firms/showcase/`. Every field below is either read directly
from a firm's own published documentation or computed from fields that are.

## Top level

| Field | Type | Meaning |
|---|---|---|
| `corpus_version` | string | internal batch tag for this record's last full rewrite |
| `firm` | string | the firm's display name |
| `firm_slug` | string | the lowercase, hyphenated identifier used as a directory name |
| `product` | string | the specific evaluation product this record describes (a firm may sell more than one) |
| `account_size` | number | the account size this record's numbers apply to, in the account's currency |
| `currency` | string | ISO currency code |
| `verification_status` | string | always `"verified"` in this export — unverified records never leave the working corpus |
| `retrieved_at` | date (`YYYY-MM-DD`) | the date this record's fields were first read from the firm's site |
| `steps` | integer | number of evaluation phases the product requires before funding |
| `phases` | array | one entry per evaluation phase — see below |
| `pricing` | object | see below |
| `payout` | object | see below |
| `ambiguities` | array | disclosed interpretive forks — see below |
| `documented_omissions` | array | fields the firm publishes that this corpus records as present but does not model as a pass/fail condition, with the reasoning stated |
| `provenance` | object | per-field citations — see below |
| `rechecks` | array | dated, independent re-verification passes — see below |

## `phases[]` — one evaluation stage

| Field | Type | Meaning |
|---|---|---|
| `name` | string | the firm's own name for this phase |
| `profit_target_pct` / `profit_target_abs` | number | the profit required to clear this phase, as a percent of the starting balance and in absolute currency |
| `min_trading_days` | integer | minimum number of days the account must be traded before this phase can be passed |
| `max_days` | integer or `null` | maximum days allowed; `null` means the firm publishes no time limit |
| `daily_loss` | object | see below |
| `max_drawdown` | object | see below |
| `consistency` | array | see below |

### `daily_loss`

| Field | Type / enum | Meaning |
|---|---|---|
| `amount_pct` / `amount_abs` | number | the maximum a day's losses may reach before breach, as a percent and in absolute currency. `0` or absent means the firm publishes no daily loss limit at this size. |
| `amount_basis` | enum: `initial`, `day_start_balance` | what the percentage is measured against — the account's starting balance, or that day's opening balance |
| `on_breach` | enum: `fail`, `lockout` | what happens the instant the limit is crossed — the evaluation ends, or trading is paused for the rest of that day |
| `includes_unrealized` | boolean | whether open (not yet closed) position P&L counts toward the daily figure |
| `reset_time_utc` | string (`HH:MM`) | the UTC clock time the daily figure resets |

### `max_drawdown`

| Field | Type / enum | Meaning |
|---|---|---|
| `type` | enum: `static`, `trailing` | whether the loss floor is fixed at account open, or moves as the account gains |
| `basis` | enum: `equity_intraday`, `balance_eod`, `balance_closed_trade` | for a trailing floor, what it tracks — live intraday equity (marks continuously through the day), the balance at each day's close (marks once per day), or the balance after each closed trade (marks only when a position is closed, never mid-trade) |
| `amount_pct` / `amount_abs` | number | the maximum drawdown allowed from the floor's anchor |
| `anchor` | enum: `initial_balance`, `highest_water_mark` | what the floor's distance is measured from |
| `includes_unrealized` | boolean | whether open position P&L counts toward the drawdown figure |
| `locks` | value or `null` | if set, the balance level at which a trailing floor stops trailing (freezes) once reached; `null` means the firm does not publish a lock point (or the floor never locks) |
| `evaluation_frequency` | enum: `intraday`, `eod` | how often the floor is actually tested for breach — continuously through the day, or once at each day's close |

### `consistency[]` — a "no single day may carry the whole result" style rule, where the firm publishes one

| Field | Type / enum | Meaning |
|---|---|---|
| `metric` | enum: `best_day_pct_of_total_profit`, `max_risk_pct_of_rrc_per_trade` | which quantity the rule constrains |
| `limit` | number | the threshold |
| `enforced_at` | enum: `pass`, `per_trade` | whether the rule is checked once, when the evaluation is passed, or continuously, on every trade |
| `effect` | enum: `block`, `raise_target`, `risk_sizing_cap` | what happens on breach — the pass is blocked outright, the profit target effectively rises until the day is diluted below the limit, or the rule caps position risk sizing directly |

## `pricing`

| Field | Type / enum | Meaning |
|---|---|---|
| `model` | enum: `one_time`, `one_time_refundable`, `subscription_monthly` | how the evaluation is paid for |
| `initial_fee` | number or `null` | the entry price |
| `reset_price_alive` / `reset_price_after_breach` | number or `null` | the price to restart a still-running or already-breached attempt, where the firm sells resets |
| `fee_refunded_at_first_payout` | boolean | whether the entry fee is returned once the trader reaches their first payout |
| `refund_amount` | number or `null` | the refunded amount, where it differs from the entry fee |

## `payout`

| Field | Type | Meaning |
|---|---|---|
| `first_payout_after_days` | integer or `null` | minimum days of funded trading before the first payout is allowed. `0` is a real, published value (no waiting period) and is distinct from `null` (not sourced). |
| `cycle_days` | integer or `null` | how often payouts recur after the first one |
| `profit_split_pct` | number or `null` | the trader's share of profit at payout |

## `ambiguities[]` — where a firm's own documentation does not settle a field

| Field | Type | Meaning |
|---|---|---|
| `field` | string | the field path this ambiguity applies to |
| `candidates` | array | the readings the firm's own text supports |
| `conservative` | value | which candidate this corpus models — always the reading that makes passing harder, never the more generous one |
| `basis` | string | the reasoning for the conservative pick, in full |
| `sources` | array of URLs | where the ambiguity was found |

## `provenance` — one entry per cited field, keyed by field path

| Field | Type / enum | Meaning |
|---|---|---|
| `source_url` | URL | the firm's own page the value was read from |
| `retrieved_at` | date | when it was read |
| `quote` | string | the supporting sentence, verbatim, from the source |
| `confidence` | enum: `sourced`, `derived`, `reconciled`, `ambiguous` | `sourced` — the firm states this value directly; `derived` — computed from a sourced value by exact arithmetic or logical necessity; `reconciled` — cross-checked against a worked example the firm itself published; `ambiguous` — the firm's own text supports more than one reading (see `ambiguities[]`) |
| `reasoning` | string, optional | present when a confidence label needed a stated justification (e.g. a downgrade after a later pass found no reconciling example) |

## `rechecks[]` — dated, independent re-verification passes

| Field | Type / enum | Meaning |
|---|---|---|
| `checked_at` | date | when this pass ran |
| `by` | string | which process ran it |
| `method` | enum: `two-pass-blind-independent`, `direct-primary-source-fetch`, `modeled-from-prior-run-2-finding` | how the recheck was performed — two independent re-derivations compared against each other and against the stored value; a direct re-fetch of the primary source; or a conversion of an earlier pass's finding into a formal record |
| `passes` | integer | how many independent re-derivations this pass ran |
| `outcome` | string | the pass's overall verdict for this recheck (e.g. `confirmed`, `confirmed_with_mismatch`, `confirmed_with_disagreement`, `changed`) |
| `fields` | object | per-field status strings for exactly the fields this pass covered — not a fixed enum; common values include `confirmed`, `mismatch`, `disagree`, and dated resolution notes for how a disagreement was closed |
| `sources` | array of URLs | sources consulted for this pass |
| `note` | string | the pass's own written summary |

## Metadata table fields (`firms/metadata.json`, all 59 tracked firms)

Coverage facts about the verification process for a firm — never a rule value.
None of the fields below can answer "what is this firm's drawdown / daily-loss /
target / payout number"; that is only ever answered inside `firms/showcase/`, and
only for the five showcase firms.

| Field | Type | Meaning |
|---|---|---|
| `firm`, `slug` | string | identify the firm |
| `multiPhase` | boolean | whether this firm's product requires more than one evaluation phase before funding |
| `citedFieldCount` | integer | how many distinct field paths carry a provenance citation for this firm, across every account size tracked |
| `lastChecked` | date | the newest evidence date on record for this firm — the later of its most recent independent recheck and its most recent field retrieval |
| `hasGoldenVector` | boolean | whether this firm has at least one worked example, taken from the firm's own materials, that the rule engine is checked against exactly |
| `hasDisclosedAmbiguity` | boolean | whether this firm has at least one field where its own documentation supports more than one reading |
| `hasIndependentRecheck` | boolean | whether this firm has been re-verified at least once, independently of its original read |
