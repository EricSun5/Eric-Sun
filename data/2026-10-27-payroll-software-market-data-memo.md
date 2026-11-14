---
type: data
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-27
status: committed
---

# Payroll-Software FX Hedging — Market Data Memo

## Market Inputs

| Named Range | Value | Unit | Source | Observation Date | Treatment / Rationale |
|---|---:|---|---|---|---|
| `FC_AMT` | 4,300,000 | EUR | Scenario 6 | N/A | Contractual receivable; unchanged |
| `S0_in` | 1.1150 | USD/EUR | ECB euro reference exchange rate, 27 Oct 2026 — https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html | 2026-10-27 | Official same-date EUR/USD reference rate |
| `F0_in` | 1.1807 | USD/EUR | Scenario 6 bank forward quote | Scenario quote | Bank's quoted 365-day forward; retained as `F0_in` unless the bank/instructor provides a requote |
| `R_USD` | 4.50% | Annual % | U.S. Treasury Daily Par Yield Curve, 1-Year, 27 Oct 2026 — https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?field_tdr_date_value=2026&type=daily_treasury_yield_curve | 2026-10-27 | 1-year U.S. sovereign yield chosen to match the 365-day exposure |
| `R_FC` | 2.930% | Annual % | Germany 1-Year government bond yield, 27 Oct 2026 — https://www.investing.com/rates-bonds/germany-1-year-bond-yield-historical-data | 2026-10-27 | 1-year EUR sovereign yield chosen to match the 365-day exposure and the USD sovereign-rate approach |
| `K_PUT` | 1.1150 | USD/EUR | Derived from `S0_in` | Derived from S0_in observed 2026-10-27 | At-the-money strike per scenario convention |
| `K_CALL` | 1.1150 | USD/EUR | Derived from `S0_in` | Derived from S0_in observed 2026-10-27 | At-the-money strike per scenario convention |
| `PREM_PUT` | 0.0245 | USD/EUR | Scenario 6 | N/A | Scenario premium retained as instructed |
| `PREM_CALL` | 0.0448 | USD/EUR | Scenario 6 | N/A | Scenario premium retained as instructed |
| `T_DAYS` | 365 | Days | Scenario 6 | N/A | Contractual settlement horizon; unchanged |

## Rate Choices

`R_USD` uses the U.S. Treasury 1-year par yield of 4.50%, and `R_FC` uses the Germany 1-year government bond yield of 2.930%. Both are 1-year sovereign yields, so the instruments are similar in type and match the receivable's approximately one-year maturity.

The German 1-year government yield is a market proxy and is not necessarily the rate at which this company could actually borrow euros. A private company would generally borrow at a rate reflecting its own bank funding terms and credit risk, which could be higher than the German sovereign yield. If the company's actual EUR borrowing rate were higher, the amount of EUR borrowed today would be lower and the resulting money-market hedge proceeds would also be lower. Therefore, the current money-market hedge should be interpreted as a proxy-based estimate rather than a guaranteed corporate borrowing outcome.

## Bank Forward and CIP-Implied Forward Check

The model retains the bank's quoted 365-day forward as:

`F0_in = 1.1807 USD/EUR`

Covered interest parity is used separately as a diagnostic check:

`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`

`F_IMPLIED = 1.1150 × (1 + 0.0450 × 365/360) / (1 + 0.02930 × 365/360)`

`F_IMPLIED = 1.1322366 USD/EUR`

The bank's quoted forward of `1.1807` is `0.0484634 USD/EUR` above the CIP-implied forward of `1.1322366`. The bank quote remains `F0_in`; the CIP-implied forward is retained only as a diagnostic comparison and is not substituted for the bank's quote.

## Scenario Premium Assumption

The put premium remains `0.0245 USD/EUR` and the call premium remains `0.0448 USD/EUR`. These are the scenario-provided premiums and are intentionally retained because the Stage 4 instructions state that retail-accessible option quotes are not reliable enough for this assignment.

## Workbook Population and Structural Fix

The Stage 4 market refresh populated `S0_in`, `R_USD`, `R_FC`, `K_PUT`, and `K_CALL` using the sourced market observations and derived at-the-money strikes. `F0_in` remains the scenario bank forward quote of `1.1807`. `FC_AMT`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS` remain unchanged.

One structural issue surfaced when live data were loaded: the Stage 3 validation block used hard-coded placeholder check figures. Those checks would incorrectly flag a correctly populated live model. The validation formulas were revised to test the model relationships dynamically instead of comparing against the old placeholder numbers. The hedge formulas themselves were not changed.

## Live-Data Check Figures

Using the populated workbook inputs:

- Forward proceeds using the bank quote: approximately `$5,077,010.00`
- Money-market EUR borrowing: approximately `EUR 4,175,945.42`
- Money-market USD after spot conversion: approximately `$4,656,179.14`
- Money-market settlement proceeds: approximately `$4,868,617.32`
- CIP-implied forward: approximately `1.1322366 USD/EUR`
- Parity gap: approximately `-0.0484634 USD/EUR`
- Put premium paid at inception: `$105,350.00`
- Put premium carried to settlement: approximately `$110,156.59`
- Call premium paid at inception: `$192,640.00`
- Put floor: approximately `$4,684,343.41`
- Sensitivity range: `1.05925` to `1.17075 USD/EUR`

The forward and money-market proceeds do not agree within rounding because the model retains the bank's quoted `F0_in = 1.1807` rather than replacing it with the CIP-implied forward. The difference between money-market and forward proceeds is approximately `-$208,392.68`.

## FX Hedging Lab Cross-Check

The following inputs were entered into the FX Hedging Lab:

- `FC_AMT = 4,300,000`
- `S0_in = 1.1150`
- `F0_in = 1.1807`
- `R_USD = 4.50%`
- `R_FC = 2.930%`
- `K_PUT = 1.1150`
- `K_CALL = 1.1150`
- `PREM_PUT = 0.0245`
- `PREM_CALL = 0.0448`
- `T_DAYS = 365`

| Output | FX Hedging Lab | Workbook | Difference / Explanation |
|---|---:|---:|---|
| Forward proceeds | $5,077,010 | $5,077,010.00 | Matches within rounding. |
| MM EUR borrowed | 4,175,945 EUR | €4,175,945.42 | Matches within rounding. |
| MM USD after spot conversion | $4,656,179 | $4,656,179.14 | Matches within rounding. |
| MM settlement proceeds | $4,868,617 | $4,868,617.32 | Matches within rounding. |
| Implied forward | 1.1322 | 1.1322366 | Matches within rounding. |
| Quoted forward | 1.1807 | 1.1807 | Matches exactly. |
The FX Hedging Lab does not display separate output cells for the put premium or put proceeds. Those option amounts were therefore verified from the workbook rather than included in the Lab cross-check. The workbook calculates a put premium of `$105,350.00`, a settlement-equivalent premium cost of approximately `$110,156.59`, and a put floor of approximately `$4,684,343.41`.
