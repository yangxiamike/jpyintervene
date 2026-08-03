# JPY Intervention Observation System — MVP Plan

> Status: **Plan only / not started**  
> Updated: 2026-08-03  
> Execution gate: do not download data, write implementation code, run tests, or produce empirical results until the user explicitly says to start.

## 1. Objective

Build a reproducible monitoring and hypothesis-testing system for the market impact of Japanese and coordinated U.S.–Japan yen intervention.

The system should distinguish among three market regimes:

1. **Green — controlled reset:** yen-funded carry positions deleverage without materially damaging U.S. rates, credit, or equity breadth; conditions may support a later U.S. equity advance.
2. **Yellow — rhythm stabilization:** policy action prevents disorder, but liquidity, credit, and equity breadth do not yet confirm a new risk-on phase.
3. **Red — disorderly deleveraging:** yen appreciation coincides with rising Treasury term premium, widening credit spreads, deteriorating funding conditions, and broad equity weakness.

The project tests observable market relationships. It must not present statistical association as proof of U.S. policy intent or causal proof that intervention was designed to lift U.S. equities.

## 2. Causal Map

```text
Official yen intervention
  → USD/JPY level and volatility
  → yen-funded carry positioning and forced deleveraging
  → Treasury term premium, credit spreads, and dollar liquidity
  → U.S. equity returns, maximum drawdown, and market breadth
```

The decision variable is not yen appreciation alone. The system must evaluate whether yen appreciation is accompanied by improving or deteriorating U.S. financial conditions.

## 3. Research Questions and Hypotheses

### H1 — FX effectiveness and persistence

Official intervention produces statistically meaningful yen appreciation over one to five trading days. Persistence over 20 to 60 trading days depends on the U.S.–Japan rate differential and positioning.

- Null: post-intervention USD/JPY behavior is not different from comparable non-intervention periods.
- Primary outcomes: 1-, 3-, 5-, 20-, and 60-day USD/JPY returns and realized volatility.
- Confirmers: narrowing rate differential, declining yen shorts, falling post-shock volatility.
- Disconfirmers: rapid retracement, widening rate differential, repeated intervention in a short window.

### H2 — Controlled deleveraging and later U.S. equity performance

When yen appreciation is followed by declining FX volatility, stable or lower Treasury term premium, and stable high-yield spreads, subsequent 20- to 60-day U.S. equity risk-adjusted returns improve and maximum drawdown is smaller.

- Null: the controlled-reset state has no predictive relationship with forward U.S. equity outcomes.
- Primary outcomes: SPY and QQQ forward return, realized volatility, and maximum drawdown.
- Breadth outcome: RSP/SPY relative return.

### H3 — Rates/credit confirmation of a disorderly unwind

When yen appreciation coincides with rising U.S. Treasury yields or term premium, widening credit spreads, and rising rates volatility, subsequent U.S. equity maximum drawdown is larger.

- Key comparison:
  - Group A: yen appreciation + stable/falling Treasury term premium.
  - Group B: yen appreciation + rising Treasury term premium.
- Primary outcomes: forward 5-, 20-, and 60-day equity return, maximum drawdown, VIX, and high-yield OAS.

### H4 — Breadth distinguishes stabilization from a new risk-on phase

Improving RSP/SPY and risk appetite after the deleveraging phase is more consistent with a broad market reset. A stable capitalization-weighted index without breadth improvement is more consistent with rhythm stabilization only.

- Null: breadth proxies add no information beyond the capitalization-weighted index.
- Primary outcomes: RSP/SPY, QQQ/SPY, and available small-cap/large-cap relative performance.

## 4. Free Data Inventory

The MVP must use free public sources and must not require Bloomberg, Wind, FactSet, LSEG, a paid subscription, or a private API key.

| Domain | Series / dataset | Source | Frequency | Main use | Known limitation |
|---|---|---|---|---|---|
| FX | `DEXJPUS` | [FRED / Federal Reserve H.10](https://fred.stlouisfed.org/series/DEXJPUS) | Daily | USD/JPY returns and realized volatility | New York noon fixing; not intraday |
| Japan intervention | Monthly and quarterly FX Intervention Operations | [Japan Ministry of Finance](https://www.mof.go.jp/english/policy/international_policy/reference/feio/monthly/index.html) | Monthly / quarterly | Confirmed dates, amounts, currencies | Monthly and quarterly publication lag |
| U.S. intervention | Treasury and Federal Reserve FX Operations | [New York Fed](https://www.newyorkfed.org/markets/international-market-operations/foreign-exchange-operations) | Quarterly / event | Confirmed U.S. participation | Sparse event sample and reporting lag |
| Positioning | CME Japanese Yen, code `097741` | [CFTC Commitments of Traders](https://www.cftc.gov/dea/futures/deacmelf.htm) | Weekly | Yen speculative positioning proxy | Futures only; publication lag |
| U.S. rates | `DGS2`, `DGS10` | FRED / Federal Reserve H.15 | Daily | Curve and rate changes | Nominal yields |
| Term premium | ACM 10-year term premium | [New York Fed](https://www.newyorkfed.org/research/data_indicators/term-premia-tabs) | Daily | Separate term premium from expected short rates | Model estimate, not observed data |
| Equity volatility | `VIXCLS` | FRED / Cboe | Daily | Equity risk state | End-of-day index |
| Credit | `BAMLH0A0HYM2` | [FRED / ICE BofA](https://fred.stlouisfed.org/series/BAMLH0A0HYM2) | Daily | U.S. high-yield OAS | Index methodology/licensing constraints |
| Fed liquidity | `WRESBAL` | [FRED / Federal Reserve H.4.1](https://fred.stlouisfed.org/series/WRESBAL) | Weekly | Bank reserve balance trend | Weekly, not intraday liquidity |
| Treasury cash | `WTREGEN` | [FRED / Federal Reserve H.4.1](https://fred.stlouisfed.org/series/WTREGEN) | Weekly | Treasury General Account drain/injection | Weekly average |
| Reverse repo | `RRPONTSYD` | FRED / New York Fed | Daily | Liquidity buffer proxy | Interpretation changes near low balances |
| Fed assets | `WALCL` | FRED / Federal Reserve H.4.1 | Weekly | Optional balance-sheet context | Broad measure, not a direct liquidity signal |
| Japan cross-border flow | International Transactions in Securities | [Japan Ministry of Finance](https://www.mof.go.jp/english/policy/international_policy/reference/itn_transactions_in_securities/index.htm) | Weekly | Resident foreign securities purchases/sales | Aggregated; sign convention must be verified |
| Japan Treasury holdings | TIC Major Foreign Holders | [U.S. Treasury](https://home.treasury.gov/data/treasury-international-capital-tic-system) | Monthly | Japan's Treasury holdings | Roughly six-week lag; valuation/custody effects |
| Equity proxies | SPY, QQQ, RSP, EWJ | Stable no-key public market source to be selected at execution | Daily | U.S. equity, breadth, and Japan equity outcomes | Provider stability and licensing must be documented |

### Paid or unavailable variables and approved MVP proxies

| Preferred variable | MVP proxy |
|---|---|
| MOVE history | 20-day realized volatility of daily 10-year Treasury yield changes |
| Historical USD/JPY implied volatility | USD/JPY realized volatility |
| Historical 25-delta risk reversal | USD/JPY trend, realized volatility, and CFTC positioning |
| Cross-currency basis | Dollar liquidity and rate-volatility proxies; omit if no reliable free history |
| Dealer gamma | Equity realized volatility and options-index proxies if available; otherwise omit |
| CTA / vol-control positioning | Trend and realized-volatility rules; label as proxy |
| EPS revision breadth | RSP/SPY and sector-relative performance; omit direct claim |
| Full constituent breadth history | RSP/SPY; do not backfill current constituents into history |

No unavailable series may be fabricated or silently replaced. Every proxy must be labeled in the data dictionary and results.

## 5. Data and Sample Design

### 5.1 Sample windows

- Preferred full sample: 1999 to the latest available date.
- Breadth sample: begins when RSP data become available.
- If source histories differ, maintain:
  1. a long core sample without RSP; and
  2. a shorter breadth sample with RSP/SPY.

### 5.2 Intervention episodes

- Use only officially confirmed intervention dates for the main event set.
- Combine intervention dates within 10 trading days into one episode.
- Store individual operations and episode identifiers separately.
- Treat reported or suspected interventions as a separate, non-primary dataset.
- U.S.–Japan coordinated events are too few for conventional statistical inference; use descriptive case comparison only.

### 5.3 Event windows

| Window | Interpretation |
|---|---|
| `[-20, -1]` | Pre-event trend and matching covariates |
| `[0, +2]` | Immediate market shock |
| `[+3, +10]` | Early stabilization or acceleration |
| `[+11, +20]` | Near-term persistence |
| `[+21, +60]` | Medium-term direction and equity outcome |

### 5.4 Training and out-of-sample discipline

- Training: 1999–2018.
- Validation: 2019–2023.
- Out-of-sample: 2024–2026.
- Adjust the split only when required by data availability and document the change before looking at results.
- Thresholds and feature weights must be fixed using training data only.

## 6. Empirical Methods

### 6.1 Event study

Estimate post-event changes in USD/JPY, realized volatility, Treasury yields, ACM term premium, VIX, high-yield OAS, SPY/QQQ returns, RSP/SPY, and maximum drawdown.

Report:

- episode count;
- mean, median, and distribution;
- effect size;
- confidence interval;
- maximum drawdown;
- results with and without unusually large episodes.

### 6.2 Matched control study

Match intervention episodes to non-intervention dates using only pre-event information:

- 20-day USD/JPY trend and volatility;
- VIX;
- SPY/QQQ prior return;
- DGS10 and yield-curve state;
- high-yield OAS;
- oil price, if a stable free series is available;
- CFTC yen positioning.

Matching choices must be specified before outcome comparison. Do not tune matching rules to maximize significance.

### 6.3 Full-sample state test

Classify each eligible day using lagged/available information into candidate states:

1. yen appreciation + stable/falling term premium;
2. yen appreciation + rising term premium;
3. yen stabilization + improving liquidity/breadth;
4. yen depreciation + rising intervention probability;
5. broad deleveraging across FX, rates, credit, and equities.

Test forward 5-, 20-, and 60-day outcomes across states. This is a predictive test and not a causal intervention estimate.

### 6.4 Statistical controls

- Use Newey–West standard errors for overlapping forward returns or block bootstrap confidence intervals.
- Cluster consecutive intervention operations into episodes.
- Report multiple-testing adjustments when many outcomes or windows are tested.
- Prefer effect sizes and confidence intervals to isolated p-values.
- Preserve an audit table showing which observations enter each test.

## 7. Regime Monitor Design

The MVP monitor should use transparent rules that can be reproduced from the data package.

### Green — controlled reset

Indicative conditions:

- yen appreciates initially, then USD/JPY realized volatility declines;
- CFTC yen shorts decline without continued acceleration in FX volatility;
- ACM term premium is stable or falling;
- high-yield OAS is stable or compressing;
- VIX and the Treasury-volatility proxy peak and decline;
- RSP/SPY improves after the initial shock;
- Fed reserve/liquidity indicators are neutral or supportive.

### Yellow — rhythm stabilization

Indicative conditions:

- USD/JPY enters a range;
- Treasury term premium and credit spreads remain stable but do not improve;
- capitalization-weighted equities hold, while RSP/SPY and broader risk appetite remain weak;
- liquidity is neutral;
- no clear evidence of forced cross-border liquidation.

### Red — disorderly deleveraging

Indicative conditions:

- yen appreciation continues to accelerate;
- ACM term premium and long Treasury yields rise;
- high-yield OAS widens;
- VIX and the Treasury-volatility proxy rise together;
- RSP/SPY deteriorates;
- Japan resident foreign-security sales or TIC holdings indicate repatriation/reserve pressure;
- repeated intervention is required.

Numerical thresholds must be estimated or fixed during the training stage. This plan does not pre-commit arbitrary production thresholds.

## 8. Minimum Delivery Package

Future execution should produce only the following MVP artifacts:

1. `README.md`
   - research question and causal map;
   - setup and one-command run instructions;
   - conclusion boundaries.
2. `data_dictionary.csv`
   - variable name, source, URL, code, frequency, release lag, transformation, missing-data rule, official/proxy flag.
3. Reproducible Python pipeline or notebook
   - download, validation, cleaning, event construction, feature construction, tests, and charts;
   - no private key;
   - one command to run.
4. `results.html` or `results.md`
   - event study;
   - matched controls;
   - full-sample state test;
   - validation and out-of-sample results;
   - effect sizes, confidence intervals, and drawdowns.
5. `limitations.md`
   - small sample;
   - intervention endogeneity;
   - proxy variables;
   - data lags and revisions;
   - predictive versus causal interpretation.
6. Minimal regime table
   - green, yellow, and red status;
   - reproducible rules;
   - current data timestamp and missing-data flags.

## 9. Acceptance Criteria

- Every input is traceable to a documented public source.
- The core pipeline uses no paid database and no private API key.
- The run is reproducible with one documented command.
- Download failure produces a clear error and an approved fallback path.
- No future data or current-constituent backfill is used.
- Intervention days are grouped into episodes before inference.
- Results distinguish confirmed events, reported events, proxies, inference, and scenarios.
- Null or statistically insignificant results are delivered honestly with power limitations.
- Predictive findings are not described as proof of policy intent or causality.
- The first implementation remains an MVP and does not expand into a production platform.

## 10. Execution Order After User Approval

1. Freeze the source list and data dictionary.
2. Validate access to each free source without downloading the full history.
3. Implement raw-data download and immutable source snapshots.
4. Implement data cleaning and release-lag alignment.
5. Build confirmed intervention operations and episode tables.
6. Produce descriptive event-study output.
7. Add matched controls and full-sample state tests.
8. Freeze thresholds on training data.
9. Run validation and out-of-sample evaluation.
10. Generate the minimum delivery package and QA all provenance and leakage checks.

## 11. Known Risks and Decision Gates

- **Small coordinated-event sample:** coordinated U.S.–Japan events remain descriptive; do not force inferential statistics.
- **Endogeneity:** intervention occurs during stress; matched controls reduce but do not eliminate selection bias.
- **Publication lags:** CFTC, TIC, intervention disclosures, and weekly flows must be aligned to their actual release dates in any real-time backtest.
- **Provider stability:** choose and document a no-key source for ETF history before implementation.
- **Series licensing:** redistribution restrictions must be respected in saved artifacts.
- **Proxy risk:** realized volatility and ETF ratios are not substitutes for options books, dealer gamma, or full market breadth; conclusions must remain narrow.
- **Execution gate:** no implementation begins until the user explicitly authorizes it.

---

**Current status:** plan archived only. No data has been downloaded, no implementation code has been written, and no hypothesis test has been run. Awaiting an explicit user instruction to begin.
