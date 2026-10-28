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
