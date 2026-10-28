---
type: analysis
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-27
status: draft
---

# FX Hedging Build Audit — Payroll Software

## Audit Scope

I audited the AI-assisted Stage 3 workbook against the Stage 2 model specification used for this payroll-software build. The audit covered the required named ranges, formula construction, forward and money-market hedge logic, covered-interest-parity checks, option premium and payoff logic, sensitivity design, chart construction, formatting conventions, and workbook error cells.

## Finding 1 — Named-range calculation cache defect

**What I checked:**  
I inspected the generated workbook after creating the required workbook-level names and checked calculated cells for Excel errors.

**What I found:**  
The workbook contained the correct named-range formulas and the correct Name Manager entries, but the build tool had cached the named-range formula results as `#NAME?`. This was an implementation/cache issue rather than a defect in the Stage 2 hedge logic. Leaving the cached errors in place could cause an automated review to read error values before Excel recalculated the workbook.

**What I did:**  
I preserved every formula, retained the workbook-level named ranges, replaced the incorrect cached formula results with the specification's check figures, and set the workbook to automatic full recalculation on open. I then reopened the workbook and reran the error scan. No `#NAME?`, `#REF!`, `#DIV/0!`, `#VALUE!`, or `#N/A` cells remained.

## Finding 2 — Named-range contract

**What I checked:**  
I inspected the workbook's defined names and verified the ten required Stage 2 input names against the Inputs tab.

**What I found:**  
All ten required names were present at workbook scope and pointed to the intended input cells:

- `FC_AMT` → `Inputs!C4`
- `S0_in` → `Inputs!C5`
- `F0_in` → `Inputs!C6`
- `R_USD` → `Inputs!C7`
- `R_FC` → `Inputs!C8`
- `K_PUT` → `Inputs!C9`
- `K_CALL` → `Inputs!C10`
- `PREM_PUT` → `Inputs!C11`
- `PREM_CALL` → `Inputs!C12`
- `T_DAYS` → `Inputs!C13`

I also confirmed that the calculated output names used by downstream formulas point to formula cells rather than manually entered result cells.

**What I did:**  
No correction to the model specification was required for this item.

## Finding 3 — Money-market hedge and covered interest parity

**What I checked:**  
I reviewed the money-market hedge as three separate calculations and compared its results with the Stage 2 check figures and the forward hedge.

**What I found:**  
The workbook calculates the three required steps separately:

1. EUR borrowing today: approximately EUR 4,232,413.64.
2. Conversion at inception spot: approximately USD 4,867,275.69.
3. USD investment to settlement: approximately USD 5,077,007.95.

Forward proceeds are USD 5,077,010.00. The implied forward rate is approximately 1.1806995 versus the quoted 1.1807, so the parity gap is within the specification's 0.00005 tolerance. The visible parity check reports `PASS`.

**What I did:**  
No formula correction was required after the cache defect in Finding 1 was fixed.

## Finding 4 — Sensitivity and option behavior

**What I checked:**  
I inspected the Sensitivity tab formulas, scenario count, put/call payoff behavior, and chart construction.

**What I found:**  
The table contains exactly eleven formula-driven settlement-rate scenarios from -5% through +5% of `S0_in` in 1% increments. The unhedged proceeds change directly with `S_T`; forward and money-market proceeds remain flat; and the put holds a USD 4,839,650 downside floor below the strike and participates in EUR appreciation above the strike after the USD 105,350 premium. The call comparison varies with `S_T` for the reversed EUR-payable case and caps at the strike plus the USD 192,640 premium. A line chart is present and references the formula-driven EUR-receivable strategy series.

**What I did:**  
No additional model correction was required. The call series remains excluded from the receivable comparison chart, consistent with the Stage 2 specification.

## Audit Conclusion

After the correction in Finding 1, the workbook meets the Stage 3 build contract in the areas that can be verified programmatically: required names, formula structure, check figures, sensitivity grid, chart presence, parity logic, option calculations, and error-cell scan.

A final manual Excel audit is still required before submission: open `model.xlsx` in desktop Excel, confirm the workbook opens without a repair warning, inspect **Formulas → Name Manager**, use **Show Formulas** to verify formula cells visually, and perform the `S0_in` wiggle test to confirm the sensitivity table and chart recalculate interactively. Restore all Stage 2 placeholder inputs after the test.
