---
type: brief
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-23
status: committed
hypothesis: "I expect the forward and money-market hedge to produce similar certainty-based dollar proceeds, while the put option preserves more upside at the cost of a $105,350 premium."
---

# Payroll-Software FX Hedging — Executive Brief
## Exposure
A U.S. payroll-software firm is scheduled to receive EUR 4,300,000 from a Spanish bank in 365 days for a multi-year software license. The euro amount is fixed, but the dollar value of that receivable is not. Because the company ultimately reports and spends in U.S. dollars, a weaker euro would reduce the cash collected in dollar terms. Every $0.01 decline in EURUSD reduces the receivable's dollar value by approximately $43,000; a $0.10 decline would reduce it by about $430,000. The indicative 365-day forward rate of 1.1807 provides a useful benchmark: locking that rate today would produce approximately $5,077,010 in one year, before any additional implementation costs.
## Hedge Alternatives
Three hedge families can address this exposure. A forward would lock the EURUSD rate today for the full EUR 4.3 million receivable. Its main advantage is complete certainty over the future dollar proceeds, while its main disadvantage is that the company gives up the benefit of a stronger euro. A money-market hedge would borrow euros today, convert them to dollars at spot, invest the dollars, and use the future euro receivable to repay the borrowing. It can create similar certainty to a forward, but it uses borrowing capacity and requires both EUR and USD interest-rate inputs. A EUR put option would create a floor under the dollar value of the receivable while allowing the company to benefit if the euro strengthens. At the stated premium of $0.0245 per euro, protecting the entire exposure would cost $105,350 upfront.
## What I am assuming
I am assuming the Spanish bank pays the full EUR 4,300,000 exactly 365 days from now and that the firm plans to convert the entire receipt into U.S. dollars. I am also assuming the quoted forward rate and option premium can be applied to the full exposure and maturity. For the money-market hedge, I will use comparable 12-month EUR and USD borrowing or investment rates from reliable market sources. Transaction costs, taxes, and credit adjustments are excluded unless later stages show that they are material.
## Hypothesis
I expect the forward and money-market hedge to produce similar certainty-based dollar proceeds, with the forward near $5.077 million, because interest-rate relationships should keep the two locked-in strategies economically close. I also expect the put option to preserve the most upside if the euro strengthens, but at the known $105,350 upfront premium.
## How I would know I was wrong
I would reject this hypothesis if the completed model shows that the money-market hedge differs from the forward by more than about 1% of the forward's locked proceeds, or if the option's premium is large enough that it does not provide meaningful net upside across plausible euro-strengthening outcomes.
## Next Steps
Stage 2 will design the workbook before it is built, including named inputs, worksheets, formulas, outputs, and validation checks. Stage 3 will use AI to build the workbook from that specification, followed by an audit of the generated formulas and logic. Stage 4 will replace placeholders with sourced and timestamped market data, including EURUSD and matched EUR and USD interest rates. Stage 5 will independently validate the results, compare the unhedged and hedged outcomes, and document the final recommendation in a decision memo.
