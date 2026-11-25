---
type: decision
engagement: fx-hedging
capability: fx-hedging
date: 2026-11-25
status: committed
---

# FX Hedging Decision Memo — Payroll Software

**To:** Chief Financial Officer  
**Subject:** Recommendation for EUR 4.3 Million Receivable

## A · Exposure Summary

The company expects to receive EUR 4.3 million from a Spanish bank in 365 days for a multi-year software license. Because the receivable is fixed in euros while the company's reporting currency is U.S. dollars, the ultimate USD value remains exposed to EURUSD movements until settlement. The sourced October 27, 2026 spot rate is 1.1150 USD/EUR. The bank's quoted 365-day forward is 1.1807, while the model's covered-interest-parity calculation using the supplied one-year USD and EUR rate proxies produces an implied forward of approximately 1.1322366.

The primary financial objective is to reduce uncertainty around the USD value of a known contractual receivable without accepting unnecessary cost or operational complexity.

## B · Hedge Outcomes

The forward hedge produces locked settlement proceeds of **$5,077,010.00**. Within the required ±5% sensitivity range, this is the highest proceeds result at every tested settlement rate.

The money-market hedge produces approximately **$4,868,617.32** at settlement. It removes exchange-rate uncertainty but generates approximately **$208,392.68 less** than the quoted forward. It also requires the company to borrow euros today, convert those euros into dollars, and invest the dollar proceeds. The model uses a 2.930% German one-year government yield and a 4.50% U.S. one-year sovereign rate as proxies, so the actual corporate funding outcome could differ from the modeled result.

The EUR put provides downside protection and retains participation in euro appreciation, but that flexibility has a meaningful cost. The upfront premium is **$105,350.00**, and its settlement-equivalent cost under the specification is approximately **$110,156.59**. This produces a protected minimum of approximately **$4,684,343.41**. At the highest required sensitivity point, `S_T = 1.17075`, put proceeds rise to approximately **$4,924,068.41**, which remains below the forward by approximately **$152,941.59**.

Remaining unhedged eliminates hedge costs but leaves the full receivable exposed to currency movements. Across the required sensitivity range, unhedged proceeds range from **$4,554,775.00** at a 5% EUR depreciation to **$5,034,225.00** at a 5% appreciation. Even at the strongest euro outcome in the required range, the unhedged position remains **$42,785.00 below** the quoted forward.

The EUR call is not a hedge for this receivable. It is included only as the specification's reversed EUR-payable comparison and therefore should not be considered a candidate strategy for the current exposure.

## C · Sensitivity Interpretation

The sensitivity analysis makes the trade-offs clear. If the euro depreciates, the unhedged position loses value immediately. At the -5% settlement rate of 1.05925, unhedged proceeds fall to **$4,554,775.00**, while the forward remains fixed at **$5,077,010.00** and the money-market hedge remains at approximately **$4,868,617.32**. The put protects the downside but only to approximately **$4,684,343.41** after carrying its premium cost to settlement.

If EURUSD remains at 1.1150, the unhedged receivable is worth **$4,794,500.00**. The forward still produces $5,077,010.00, the money-market hedge produces approximately $4,868,617.32, and the put remains at its $4,684,343.41 floor.

If the euro appreciates 5% to 1.17075, the unhedged receivable rises to **$5,034,225.00** and the put rises to **$4,924,068.41**. Those strategies benefit from appreciation, but neither exceeds the forward inside the required sensitivity range. The forward therefore provides both the strongest modeled proceeds and complete cash-flow certainty across all eleven tested scenarios.

The put would only become more attractive if management specifically valued upside participation beyond the tested range and was willing to pay the option premium for that flexibility. Based on the current model, that benefit does not compensate for the premium within the required sensitivity range.

## D · Recommendation

I recommend that the company hedge the full EUR 4.3 million receivable with the **365-day EURUSD forward at the bank's quoted rate of 1.1807**, subject to confirming that the quote remains executable.

This recommendation is supported by both the modeled proceeds and the firm's risk-management objective. The quoted forward locks **$5,077,010.00**, the highest proceeds of the modeled hedge alternatives throughout the required ±5% sensitivity range, while eliminating exchange-rate uncertainty without requiring an upfront option premium or a money-market borrowing transaction.

A significant issue must be surfaced before execution: the bank's forward quote does **not** agree with covered interest parity. The bank quote is **1.1807**, while the CIP-implied forward is approximately **1.1322366**, a difference of approximately **0.0484634 USD/EUR**. Applied to the EUR 4.3 million receivable, that rate difference represents approximately **$208,392.68** of additional USD proceeds under the quoted forward relative to the CIP-consistent money-market result.

The documents do not provide enough evidence to identify one definitive cause of the gap. The likely explanation is that the bank forward is a scenario-provided quote while the CIP calculation uses sourced sovereign yields as proxies rather than the company's executable borrowing and investment rates; timing, market conventions, bid-ask spreads, and the continued availability of the quoted bank rate could also contribute. The gap is therefore large enough that the CFO should verify the quote with the bank before relying on it.

The parity break does **not** change the recommendation as long as the 1.1807 quote is executable. In fact, the quoted rate is the reason the forward produces the strongest modeled proceeds. If the bank cannot honor 1.1807 and provides a materially different requote, the hedge alternatives should be recalculated before execution.

## E · Executive Justification

The forward provides the strongest combination of cash-flow stability, budget certainty, liquidity preservation, and modeled USD proceeds.

From a cash-flow perspective, the company can lock approximately **$5.077 million** today for receipt at settlement rather than exposing the receivable to currency movements over the next year. This is valuable for budgeting because the company can plan around a fixed USD amount instead of a range that spans nearly **$479,450** between the -5% and +5% unhedged scenarios.

The forward is also operationally simpler than the money-market hedge. The money-market strategy requires EUR borrowing, immediate conversion, and USD investment, and its modeled economics depend on the company's ability to transact at sovereign-rate proxies that may not reflect actual corporate funding terms. The forward avoids those financing transactions.

The put offers optionality, but that optionality costs approximately **$110,156.59** on a settlement-equivalent basis. The option preserves upside if the euro appreciates sufficiently, but within the entire required ±5% range it never produces more proceeds than the quoted forward. Unless management has a specific view that EURUSD will appreciate beyond the tested range and is willing to pay for that opportunity, the premium is not justified by the current analysis.

Accordingly, the company should use the quoted forward to hedge the EUR 4.3 million receivable, with one execution condition: **confirm the bank's 1.1807 quote before entering into the hedge**. If confirmed, the forward offers the clearest combination of certainty, simplicity, and financial value under the supplied assumptions.
