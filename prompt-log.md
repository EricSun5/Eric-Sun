# AI Prompt Log

## 2026-10-01 — Portfolio Workspace Setup

**What I asked:**  
I asked ChatGPT to walk me through Stage 0 Part 2 and explain how to organize my GitHub portfolio repository.

**What AI produced:**  
A step-by-step explanation of the required repository structure, placeholder files, AI conventions, and GitHub setup.

**What was wrong:**  
No major issues identified during review.

**How it was caught or corrected:**  
I compared the instructions against the course Portfolio Repo Standard and reviewed the files before committing them.

## 2026-10-27 — FX Hedging Specification Review

**Tool:** ChatGPT

**What I asked:**  
I asked ChatGPT to review `capabilities/fx-hedging/spec.md` as a model-design auditor and identify gaps, ambiguities, formula errors, inconsistent units, missing assumptions, missing named ranges, incomplete validation rules, or instructions that could cause the Stage 3 workbook to be built incorrectly.

**What the AI found:**  
The review identified several specific issues. The specification needed the standard spec frontmatter; the distinction between the 365-day settlement horizon and ACT/360 interest convention needed to be made explicit; some Stage-4 data-source descriptions were too general; the parity check did not define `PARITY_GAP` or a numerical rounding tolerance; and the call comparison calculation was not included in the defined output list.

**Specific draft → fix iteration:**  

Before:
`F_IMPLIED` was compared with `F0_in`, but the specification only said that they should agree "within rounding."

After:
I defined `PARITY_GAP = F_IMPLIED - F0_in` and specified that the Stage 2 parity check passes when `ABS(PARITY_GAP) <= 0.00005 USD/EUR`. I also stated the expected placeholder check figures so the Stage 3 build can be independently tested.

**What I did with the review:**  
I modified the specification to clarify the 365-day maturity, make the parity validation measurable, define the call comparison output, and make the Stage-4 sourcing instructions more explicit. I retained the three-step money-market calculation, ACT/360 convention, eleven-scenario sensitivity design, and put-option treatment because those already matched the project requirements.

## 2026-10-27 â€” FX Hedging Workbook Build + Audit

**Tool:** ChatGPT

**What I asked:**  
I asked ChatGPT to read the committed Stage 2 FX hedging specification and build `capabilities/fx-hedging/model.xlsx` exactly from that specification, including the ten required named ranges, formulas-only calculations, Cover and Legend/Key tabs, forward hedge, three-step money-market hedge, put and call calculations, formula-driven Â±5% sensitivity table and chart, and visible validation checks.

**Key output:**  
ChatGPT generated the Stage 3 Excel workbook and audited it against the model specification.

**Audit issue found:**  
The first generated workbook had the correct defined names and formula text, but the build environment cached formulas that referenced defined names as `#NAME?`. This was identified during the workbook error scan.

**What I changed / accepted:**  
The formulas and named ranges were retained. The incorrect cached values were corrected to the specification's check figures and the workbook was configured for automatic full recalculation when opened in Excel. The workbook was then reopened and re-audited; the error scan returned no formula-error cells.

**Additional checks:**  
I verified all ten required named ranges, the three money-market hedge steps, the covered-interest-parity tolerance, option premiums and put floor, eleven formula-driven sensitivity scenarios, and the presence of the comparison chart.

**Verdict:**  
Modified and accepted after audit.

**Manual verification still required:**  
Open the workbook in desktop Excel, inspect Name Manager and Show Formulas, perform an `S0_in` wiggle test, then restore the original Stage 2 placeholder value before committing.

2026-10-27 — Stage 4 FX Market Data Research and Population
Tool: ChatGPT

What I asked:
I asked ChatGPT to research the Stage 4 EURUSD spot rate and matched 1-year USD and EUR rates, compute the forward by covered interest parity, populate the FX hedging workbook, document provenance, and identify any structural issues caused by replacing the Stage 2 placeholders.

Sources used:  
- ECB EUR/USD reference rate for 27 October 2026
- U.S. Treasury 1-year Daily Par Yield Curve for 27 October 2026
- Germany 1-year government bond yield for 27 October 2026
Key output:
The model was populated with S0_in = 1.1150, R_USD = 4.50%, R_FC = 2.930%, and CIP-implied F0_in = 1.1322366. Put and call strikes were reset to the live spot, while the scenario option premiums were retained.

Issue found:
The Stage 3 validation checks were tied to the placeholder check figures and would fail after legitimate live-data population.

What I changed:
I changed the validation checks so they test formula relationships and parity dynamically rather than comparing outputs to the old placeholder values.

Verdict:
Modified and accepted. Final FX Hedging Lab confirmation remains a manual browser check.

2026-10-27 — Stage 4 FX Market Data Research and Population
Tool: ChatGPT

What I asked:
I asked ChatGPT to research the Stage 4 EURUSD spot rate and matched 1-year USD and EUR rates, compute the forward by covered interest parity, populate the FX hedging workbook, document provenance, and identify any structural issues caused by replacing the Stage 2 placeholders.

Sources used:  
- ECB EUR/USD reference rate for 27 October 2026
- U.S. Treasury 1-year Daily Par Yield Curve for 27 October 2026
- Germany 1-year government bond yield for 27 October 2026
Key output:
The model was populated with S0_in = 1.1150, R_USD = 4.50%, R_FC = 2.930%, and CIP-implied F0_in = 1.1322366. Put and call strikes were reset to the live spot, while the scenario option premiums were retained.

Issue found:
The Stage 3 validation checks were tied to the placeholder check figures and would fail after legitimate live-data population.

What I changed:
I changed the validation checks so they test formula relationships and parity dynamically rather than comparing outputs to the old placeholder values.

Verdict:
Modified and accepted. Final FX Hedging Lab confirmation remains a manual browser check.
