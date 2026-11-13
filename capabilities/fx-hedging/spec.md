---
type: spec
capability: fx-hedging
engagement: fx-hedging
date: 2026-10-27
status: draft
---
# FX Hedging Model Specification — Payroll Software

## 1. Problem Statement
The model will evaluate the foreign-exchange exposure created by a EUR 4,300,000 receivable due to a U.S. payroll-software company from a Spanish bank in 365 days. Because the receivable is fixed in euros while the company's reporting currency is U.S. dollars, a decline in EURUSD before settlement would reduce the dollar value of the collection. The workbook will compare the unhedged position with a forward hedge, money-market hedge, and EUR put option. It will also include a EUR call as a comparison case. The purpose of the model is to quantify the dollar proceeds, certainty, cost, and upside participation associated with each alternative. The settlement horizon is exactly `T_DAYS = 365` days from inception. The ACT/360 convention applies only to the interest-rate calculations and does not change the contractual 365-day settlement horizon.
## 2. Inputs: Named-Range Contract

All values below are model inputs. Values identified as placeholders are indicative only and will be replaced with sourced, timestamped live market data in Stage 4.

| Named Range | Description | Stage 2 Value | Unit | Stage 4 Source |
|---|---|---:|---|---|
| `FC_AMT` | Foreign-currency receivable | 4,300,000 | EUR | Scenario contractual amount; remains fixed |
| `S0_in` | Spot EURUSD rate at inception | 1.1150 | USD/EUR | ECB EUR/USD reference rate, October 27, 2026 |
| `F0_in` | 365-day EURUSD forward rate | 1.1807 | USD/EUR | Scenario bank forward quote; retain at 1.1807 unless the bank/instructor provides a requote |
| `R_USD` | USD annual interest rate | 4.50% | Annual % | Federal Reserve H.15 — 1-year Treasury constant maturity, October 27, 2026 |
| `R_FC` | EUR annual interest rate | 2.930% | Annual % | Germany 1-Year Government Bond Yield, October 27, 2026 |
| `K_PUT` | EUR put strike | 1.1150 | USD/EUR | Derived in Stage 4: set equal to live `S0_in` under the assigned at-the-money scenario |
| `K_CALL` | EUR call strike | 1.1150 | USD/EUR | Derived in Stage 4: set equal to live `S0_in` under the assigned at-the-money scenario |
| `PREM_PUT` | Put premium per EUR | 0.0245 | USD/EUR | Live option quote or scenario input as required |
| `PREM_CALL` | Call premium per EUR | 0.0448 | USD/EUR | Live option quote or scenario input as required |
| `T_DAYS` | Days to settlement | 365 | Days | Contractual maturity; remains fixed |
`S0_in`, `R_USD`, and `R_FC` are sourced market observations from October 27, 2026. `S0_in = 1.1150`, `R_USD = 4.50%`, and `R_FC = 2.930%`. The option strikes are set equal to the sourced spot rate under the assigned at-the-money convention.
## 3. Tab Architecture

The workbook will contain the following tabs in this order.

| Tab | Purpose |
|---|---|
| `Cover` | Model title, scenario description, author, date, exposure summary, data-source/provenance block, and navigation guidance |
| `Legend-Key` | Definitions of input colors, output colors, units, named ranges, and model conventions |
| `Inputs` | All ten named inputs, units, placeholder/live-data status, source, and timestamp |
| `Forward` | Forward hedge calculation and locked USD proceeds |
| `Money Market` | Three visible steps: EUR borrowing, spot conversion, and USD investment, plus parity check |
| `Options` | EUR put hedge calculations and EUR call comparison calculation |
| `Sensitivity` | Eleven ending-spot scenarios, proceeds by strategy, and one comparison chart |
| `Notes & Assumptions` | Modeling assumptions, exclusions, data-source notes, and limitations |
Input cells will be yellow, calculated output cells will be gray, and formula cells will not contain manually typed results. The Legend-Key tab will explain these conventions.
## 4. Assumptions and Constraints

- Interest calculations use simple annual interest and an ACT/360 day-count convention.
- The settlement horizon is `T_DAYS = 365`.
- The full EUR 4,300,000 receivable is hedged under each strategy for comparison purposes.
- The receivable is assumed to be collected in full on the settlement date.
- The entire EUR receipt is assumed to be converted to USD.
- `K_PUT` and `K_CALL` are initially set equal to `S0_in`, representing at-the-money strikes.
- The forward and money-market hedge should be economically close when covered interest parity approximately holds.
- Option premiums are paid upfront at inception. Because hedge outcomes are compared at the 365-day settlement date, the premium cost is carried forward to settlement using `R_USD` under the model's simple-interest ACT/360 convention.
- Transaction fees, bid-ask spreads, taxes, counterparty credit adjustments, collateral requirements, and margin requirements are excluded unless added in a later stage.
- `S0_in`, `R_USD`, and `R_FC` use sourced market observations dated October 27, 2026. `F0_in` remains the scenario bank forward quote of 1.1807 unless the bank/instructor provides a requote.
- The EUR put is the protective option for this EUR receivable. The EUR call is retained as a comparison case and is not treated as the primary receivable hedge.
## 5. Calculation Flow

### Forward Hedge

Calculate locked USD proceeds as:

`FWD_PROCEEDS = FC_AMT × F0_in`

Using the Stage 2 placeholders:

`FWD_PROCEEDS = 4,300,000 × 1.1807 = $5,077,010`
### Money-Market Hedge

The money-market hedge must be displayed as three separate calculation steps rather than one nested formula.

**Step 1 — Borrow EUR today**

`MM_BORROW_FC = FC_AMT / (1 + R_FC × T_DAYS/360)`

This determines the amount of EUR that must be borrowed today so the EUR 4,300,000 receivable exactly repays principal and interest at settlement.

**Step 2 — Convert borrowed EUR into USD at spot**

`MM_USD_NOW = MM_BORROW_FC × S0_in`

**Step 3 — Invest the USD until settlement**

`MM_PROCEEDS = MM_USD_NOW × (1 + R_USD × T_DAYS/360)`
### Covered Interest Parity
Calculate the quoted-versus-implied forward difference as:

`PARITY_GAP = F_IMPLIED - F0_in`

Because `F0_in` is specified to four decimal places in the Stage 2 scenario, the parity check will pass when:

`ABS(PARITY_GAP) <= 0.00005 USD/EUR`

Also calculate the proceeds difference as:

`MM_FWD_GAP = MM_PROCEEDS - FWD_PROCEEDS`

Using the sourced market inputs, `F_IMPLIED` is approximately `1.1322366 USD/EUR`. Therefore:

`PARITY_GAP = 1.1322366 - 1.1807 = -0.0484634 USD/EUR`

and:

`MM_FWD_GAP = $4,868,617.32 - $5,077,010.00 = -$208,392.68`

The large difference is retained because `F0_in = 1.1807` is the bank's quoted forward and must not be replaced by the CIP-implied forward.

Calculate the implied forward rate as:

`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`

Compare `F_IMPLIED` with `F0_in`.

Using the sourced market inputs, `F_IMPLIED` should be approximately `1.1322366 USD/EUR`. This does not closely match the bank's quoted `F0_in = 1.1807`; the difference is intentionally retained and reported by the parity check rather than replacing the bank quote.
### EUR Put Hedge

For each possible ending spot rate `S_T`, calculate:

`PUT_NET_PROCEEDS = FC_AMT × MAX(S_T, K_PUT) − [FC_AMT × PREM_PUT × (1 + R_USD × T_DAYS/360)]`
The put premium is paid at inception. To compare the premium with settlement-date hedge proceeds, its USD cost is carried forward to settlement using `R_USD` and the ACT/360 convention.

Calculate the total put premium separately as:

`PUT_PREM_TOTAL = FC_AMT × PREM_PUT`

Using the Stage 2 scenario premium:

`PUT_PREM_TOTAL = 4,300,000 × 0.0245 = $105,350`
Using the sourced market inputs, carrying the put premium to settlement increases its comparable cost from $105,350.00 to approximately $110,156.59. The forward remains ahead of the put at all eleven sensitivity points from -5% through +5%.
### EUR Call Comparison

Calculate total call premium as:

`CALL_PREM_TOTAL = FC_AMT × PREM_CALL`

Using the scenario premium:

`CALL_PREM_TOTAL = 4,300,000 × 0.0448 = $192,640`

For the reversed exposure of a EUR payable, calculate:

`CALL_PROTECTED_COST = FC_AMT × MIN(S_T, K_CALL) + [FC_AMT × PREM_CALL × (1 + R_USD × T_DAYS/360)]`
The EUR call is a comparison case only. It is not the protective hedge for the current EUR receivable and therefore will not be included as a strategy in the receivable sensitivity chart.

## 6. Sensitivity Plan

The `Sensitivity` tab will evaluate eleven settlement spot rates from `0.95 × S0_in` through `1.05 × S0_in`, increasing in one-percentage-point increments.

For every `S_T`, calculate:

`UNHEDGED_PROCEEDS = FC_AMT × S_T`

`FORWARD_SENS_PROCEEDS = FWD_PROCEEDS`

`MM_SENS_PROCEEDS = MM_PROCEEDS`

`PUT_SENS_PROCEEDS = FC_AMT × MAX(S_T, K_PUT) − [PUT_PREM_TOTAL × (1 + R_USD × T_DAYS/360)]`

The table will show `S_T` and the USD proceeds for the unhedged, forward, money-market, and put strategies.

One line chart will compare USD proceeds against `S_T`. The chart should allow the CFO to see that the unhedged position rises and falls directly with EURUSD, the forward and money-market hedges remain approximately flat, and the put creates a downside floor while preserving upside above the strike after the premium cost.
## 7. Validation Rules

The completed workbook must pass all of the following checks:

1. All ten required input names exist exactly as specified: `FC_AMT`, `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS`.

2. No calculation formula contains hard-coded scenario inputs that should instead reference a named range.

3. `FWD_PROCEEDS` must equal $5,077,010.

4. The money-market hedge must show all three visible steps. `MM_BORROW_FC` should be approximately €4,175,945.42, `MM_USD_NOW` approximately $4,656,179.14, and `MM_PROCEEDS` approximately $4,868,617.32.

5. `F_IMPLIED` should be approximately `1.1322366`. With the bank's quoted `F0_in = 1.1807`, `PARITY_GAP` should be approximately `-0.0484634 USD/EUR`. The parity diagnostic uses `ABS(PARITY_GAP) <= 0.00005 USD/EUR`; with these sourced inputs and the retained bank quote, the expected status is `FLAG`.

6. Using the sourced market inputs, `MM_PROCEEDS` should be approximately `$4,868,617.32` and `FWD_PROCEEDS` should equal `$5,077,010.00`. Therefore, `MM_FWD_GAP = MM_PROCEEDS - FWD_PROCEEDS` should be approximately `-$208,392.68`. This difference is expected because `F0_in` is the retained bank quote rather than the CIP-implied forward.

7. `PUT_PREM_TOTAL` must equal $105,350 and `CALL_PREM_TOTAL` must equal $192,640 using the scenario premiums.

8. When `S_T < K_PUT`, put net proceeds must remain at the protected floor. When `S_T > K_PUT`, put net proceeds must increase with `S_T`, less the premium.

9. The sensitivity table must contain exactly eleven settlement-rate scenarios from 95% through 105% of `S0_in` in 1% increments.

10. The forward and money-market series must remain flat across all sensitivity scenarios; the unhedged series must move linearly with `S_T`.

11. The workbook must contain no `#DIV/0!`, `#VALUE!`, `#N/A`, `#REF!`, or other Excel errors.

12. Every gray output cell must contain a formula rather than a manually typed result.
## 8. Outputs
The workbook must produce the following named summary outputs:

| Output Name | Description | Unit |
|---|---|---|
| `FWD_PROCEEDS` | Locked USD proceeds from forward hedge | USD |
| `MM_BORROW_FC` | EUR borrowed today | EUR |
| `MM_USD_NOW` | USD received after spot conversion | USD |
| `MM_PROCEEDS` | USD proceeds at settlement from money-market hedge | USD |
| `F_IMPLIED` | Forward rate implied by covered interest parity | USD/EUR |
| `PARITY_GAP` | Difference between implied and quoted forward rate | USD/EUR |
| `PUT_PREM_TOTAL` | Total put premium | USD |
| `CALL_PREM_TOTAL` | Total call premium | USD |
| `PUT_FLOOR` | Minimum net USD proceeds under the put hedge | USD |
| `CALL_PROTECTED_COST` | Protected USD cost for the reversed EUR-payable comparison case | USD |
| `MM_FWD_GAP` | Difference between money-market and forward USD proceeds | USD |
| `PARITY_STATUS` | PASS/FLAG result for the covered-interest-parity validation | Text |

### Output Formula — Put Floor

`PUT_FLOOR = FC_AMT × K_PUT − [FC_AMT × PREM_PUT × (1 + R_USD × T_DAYS/360)]`
`PARITY_STATUS = PASS` when `ABS(PARITY_GAP) <= 0.00005`; otherwise `FLAG`.
The `Sensitivity` tab must contain one Excel table named `tblSensitivity` with the columns:

`S_T`

`Unhedged Proceeds`

`Forward Proceeds`

`Money-Market Proceeds`

`Put Net Proceeds`

The workbook must also contain one comparison line chart based on `tblSensitivity`.
