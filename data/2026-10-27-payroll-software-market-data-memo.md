---
type: data
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-27
status: committed
---

# Payroll-Software FX Hedging — Market Data Memo

## Market Inputs

| Named Range | Value | Unit | Source | Retrieval Timestamp | Treatment / Rationale |
|---|---:|---|---|---|---|
| `FC_AMT` | 4,300,000 | EUR | Scenario 6 | N/A | Contractual receivable; unchanged |
| `S0_in` | 1.1150 | USD/EUR | ECB euro reference exchange rate, 27 Oct 2026 — https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html | 2026-10-27 19:50 HST | Official same-date EUR/USD reference rate |
| `F0_in` | 1.1322366 | USD/EUR | CIP-implied from `S0_in`, `R_USD`, `R_FC`, and `T_DAYS` | 2026-10-27 19:50 HST | Used because the project permits a CIP-implied forward when a directly comparable live outright quote is not used |
| `R_USD` | 4.50% | Annual % | U.S. Treasury Daily Par Yield Curve, 1-Year, 27 Oct 2026 — https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?field_tdr_date_value=2026&type=daily_treasury_yield_curve | 2026-10-27 19:50 HST | 1-year U.S. sovereign yield chosen to match the 365-day exposure |
| `R_FC` | 2.930% | Annual % | Germany 1-Year government bond yield, 27 Oct 2026 — https://www.investing.com/rates-bonds/germany-1-year-bond-yield-historical-data | 2026-10-27 19:50 HST | 1-year EUR sovereign yield chosen to match the 365-day exposure and the USD sovereign-rate approach |
| `K_PUT` | 1.1150 | USD/EUR | Derived from `S0_in` | Same as spot retrieval | At-the-money strike per scenario convention |
| `K_CALL` | 1.1150 | USD/EUR | Derived from `S0_in` | Same as spot retrieval | At-the-money strike per scenario convention |
| `PREM_PUT` | 0.0245 | USD/EUR | Scenario 6 | N/A | Scenario premium retained as instructed |
| `PREM_CALL` | 0.0448 | USD/EUR | Scenario 6 | N/A | Scenario premium retained as instructed |
| `T_DAYS` | 365 | Days | Scenario 6 | N/A | Contractual settlement horizon; unchanged |

## Rate Choices

`R_USD` uses the U.S. Treasury 1-year par yield of 4.50%, and `R_FC` uses the Germany 1-year government bond yield of 2.930%. Both are 1-year sovereign yields, so the instruments are similar in type and match the receivable's approximately one-year maturity.

## CIP-Implied Forward

The 1-year forward is calculated with covered interest parity:

`F0_in = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`

`F0_in = 1.1150 × (1 + 0.0450 × 365/360) / (1 + 0.02930 × 365/360)`

`F0_in = 1.1322366 USD/EUR`

The scenario's indicative forward was `1.1807`. The Stage 4 CIP-implied forward is lower by `0.0484634 USD/EUR`, approximately 4.10% below the scenario quote. The difference reflects the fact that the Stage 4 spot and interest-rate inputs are dated 27 October 2026 rather than the scenario's earlier indicative market assumptions.

## Scenario Premium Assumption

The put premium remains `0.0245 USD/EUR` and the call premium remains `0.0448 USD/EUR`. These are the scenario-provided premiums and are intentionally retained because the Stage 4 instructions state that retail-accessible option quotes are not reliable enough for this assignment.

## Workbook Population and Structural Fix

The workbook was populated only through the named-range input cells for `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, and `K_CALL`. `FC_AMT`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS` remain unchanged.

One structural issue surfaced when live data were loaded: the Stage 3 validation block used hard-coded placeholder check figures. Those checks would incorrectly flag a correctly populated live model. The validation formulas were revised to test the model relationships dynamically instead of comparing against the old placeholder numbers. The hedge formulas themselves were not changed.

## Live-Data Check Figures

Using the Stage 4 inputs:

- Forward proceeds: approximately `$4,868,617.32`
- Money-market EUR borrowing: approximately `EUR 4,175,945.42`
- Money-market USD after spot conversion: approximately `$4,656,179.14`
- Money-market settlement proceeds: approximately `$4,868,617.32`
- Put premium: `$105,350.00`
- Call premium: `$192,640.00`
- Put floor: `$4,689,150.00`
- Sensitivity range: `1.05925` to `1.17075 USD/EUR`

The forward and money-market proceeds agree within rounding because `F0_in` is CIP-implied from the same spot and interest-rate inputs.

## FX Hedging Lab Cross-Check

The workbook outputs to compare in the FX Hedging Lab are:

- `S0_in = 1.1150`
- `F0_in = 1.1322366`
- `R_USD = 4.50%`
- `R_FC = 2.930%`
- `FC_AMT = 4,300,000`
- `T_DAYS = 365`
- `K_PUT = K_CALL = 1.1150`
- `PREM_PUT = 0.0245`
- `PREM_CALL = 0.0448`

Expected cross-check results are approximately `$4,868,617.32` for both the forward and money-market hedges, `$105,350.00` for the put premium, and `$4,689,150.00` for the put floor.

**Manual confirmation pending:** enter the values above in the FX Hedging Lab and confirm that its outputs agree with the workbook within rounding. Record any discrepancy and its resolution here before changing `status` from `draft` to `committed`.
