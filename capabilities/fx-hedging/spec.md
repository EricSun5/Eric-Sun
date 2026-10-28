# FX Hedging Model Specification — Payroll Software

## 1. Problem Statement
The model will evaluate the foreign-exchange exposure created by a EUR 4,300,000 receivable due to a U.S. payroll-software company from a Spanish bank in 365 days. Because the receivable is fixed in euros while the company's reporting currency is U.S. dollars, a decline in EURUSD before settlement would reduce the dollar value of the collection. The workbook will compare the unhedged position with a forward hedge, money-market hedge, and EUR put option. It will also include a EUR call as a comparison case. The purpose of the model is to quantify the dollar proceeds, certainty, cost, and upside participation associated with each alternative.
## 2. Inputs: Named-Range Contract

All values below are model inputs. Values identified as placeholders are indicative only and will be replaced with sourced, timestamped live market data in Stage 4.

| Named Range | Description | Stage 2 Value | Unit | Stage 4 Source |
|---|---|---:|---|---|
| `FC_AMT` | Foreign-currency receivable | 4,300,000 | EUR | Scenario contractual amount; remains fixed |
| `S0_in` | Spot EURUSD rate at inception | 1.1500 | USD/EUR | Live timestamped EURUSD market quote |
| `F0_in` | 365-day EURUSD forward rate | 1.1807 | USD/EUR | Live 12-month EURUSD forward quote |
| `R_USD` | USD annual interest rate | 4.250% | Annual % | Federal Reserve H.15 or equivalent matched USD market rate |
| `R_FC` | EUR annual interest rate | 1.575% | Annual % | Comparable 12-month EUR market rate from an authoritative source |
| `K_PUT` | EUR put strike | 1.1500 | USD/EUR | At-the-money strike based on Stage 4 spot |
| `K_CALL` | EUR call strike | 1.1500 | USD/EUR | At-the-money strike based on Stage 4 spot |
| `PREM_PUT` | Put premium per EUR | 0.0245 | USD/EUR | Live option quote or scenario input as required |
| `PREM_CALL` | Call premium per EUR | 0.0448 | USD/EUR | Live option quote or scenario input as required |
| `T_DAYS` | Days to settlement | 365 | Days | Contractual maturity; remains fixed |
S0_in, R_USD, and R_FC are indicative Stage 2 placeholders rather than market observations. They are included so the model logic and check figures can be specified before Excel is built. All market-dependent placeholders will be replaced with sourced and timestamped data in Stage 4.
## 3. Tab Architecture

The workbook will contain the following tabs in this order.

| Tab | Purpose |
|---|---|
| `Cover` | Model title, scenario description, date, exposure summary, and navigation guidance |
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
- Option premiums are treated as nominal USD hedge costs. For consistency with the course model, premiums are deducted from proceeds without financing or compounding the premium cost.
- Transaction fees, bid-ask spreads, taxes, counterparty credit adjustments, collateral requirements, and margin requirements are excluded unless added in a later stage.
- Stage 2 market values are placeholders. Market-dependent inputs will be replaced with sourced and timestamped values in Stage 4.
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

Calculate the implied forward rate as:

`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`

Compare `F_IMPLIED` with `F0_in`.

Using the Stage 2 placeholders, `F_IMPLIED` should be approximately `1.180700 USD/EUR`, which should closely match `F0_in = 1.1807`.
### EUR Put Hedge

For each possible ending spot rate `S_T`, calculate:

`PUT_NET_PROCEEDS = FC_AMT × MAX(S_T, K_PUT) − FC_AMT × PREM_PUT`

Calculate the total put premium separately as:

`PUT_PREM_TOTAL = FC_AMT × PREM_PUT`

Using the Stage 2 scenario premium:

`PUT_PREM_TOTAL = 4,300,000 × 0.0245 = $105,350`
### EUR Call Comparison

Calculate total call premium as:

`CALL_PREM_TOTAL = FC_AMT × PREM_CALL`

Using the scenario premium:

`CALL_PREM_TOTAL = 4,300,000 × 0.0448 = $192,640`

The EUR call is included as a comparison case. For the opposite exposure, a EUR payable, the protected cost is:

`CALL_PROTECTED_COST = FC_AMT × MIN(S_T, K_CALL) + CALL_PREM_TOTAL`
## 6. Sensitivity Plan

The `Sensitivity` tab will evaluate eleven settlement spot rates from `0.95 × S0_in` through `1.05 × S0_in`, increasing in one-percentage-point increments.

For every `S_T`, calculate:

`UNHEDGED_PROCEEDS = FC_AMT × S_T`

`FORWARD_SENS_PROCEEDS = FWD_PROCEEDS`

`MM_SENS_PROCEEDS = MM_PROCEEDS`

`PUT_SENS_PROCEEDS = FC_AMT × MAX(S_T, K_PUT) − PUT_PREM_TOTAL`

The table will show `S_T` and the USD proceeds for the unhedged, forward, money-market, and put strategies.

One line chart will compare USD proceeds against `S_T`. The chart should allow the CFO to see that the unhedged position rises and falls directly with EURUSD, the forward and money-market hedges remain approximately flat, and the put creates a downside floor while preserving upside above the strike after the premium cost.
## 7. Validation Rules

The completed workbook must pass all of the following checks:

1. All ten required input names exist exactly as specified: `FC_AMT`, `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS`.

2. No calculation formula contains hard-coded scenario inputs that should instead reference a named range.

3. Using the Stage 2 placeholders, `FWD_PROCEEDS` must equal $5,077,010.

4. The money-market hedge must show all three visible steps. With the Stage 2 placeholders, `MM_BORROW_FC` should be approximately €4,232,413.64, `MM_USD_NOW` approximately $4,867,275.69, and `MM_PROCEEDS` approximately $5,077,007.95.

5. `F_IMPLIED` should be approximately 1.180700 and should agree with `F0_in = 1.1807` within rounding tolerance.

6. Under the Stage 2 placeholders, forward proceeds and money-market proceeds should differ by no more than rounding. A material difference should trigger a parity warning.

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

The `Sensitivity` tab must contain one Excel table named `tblSensitivity` with the columns:

`S_T`

`Unhedged Proceeds`

`Forward Proceeds`

`Money-Market Proceeds`

`Put Net Proceeds`

The workbook must also contain one comparison line chart based on `tblSensitivity`.
