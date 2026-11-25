---
type: analysis
engagement: fx-hedging
capability: fx-hedging
date: 2026-11-25
status: committed
---

# FX Hedging Validation Analysis — Payroll Software

## 1. Independent LLM Execution

A fresh LLM conversation was provided exactly two documents: `capabilities/fx-hedging/spec.md` and `data/2026-10-27-payroll-software-market-data-memo.md`. No workbook, prior project conversation, professor feedback, or other project files were provided. The LLM was asked to independently compute the complete hedge analysis, show supporting calculations, identify assumptions or ambiguities, and recommend a strategy.

The raw response is preserved unchanged in Appendix A.

## 2. Independent LLM vs. Workbook Comparison

The comparison uses three settlement-rate points from the required sensitivity grid: -5%, unchanged spot, and +5%.

| `S_T` | Strategy | Independent LLM | Workbook | Discrepancy | Diagnosis |
|---:|---|---:|---:|---:|---|
| 1.05925 | Unhedged proceeds | $4,554,775.00 | $4,554,775.00 | $0.00 | No discrepancy |
| 1.05925 | Forward proceeds | $5,077,010.00 | $5,077,010.00 | $0.00 | No discrepancy |
| 1.05925 | Money-market proceeds | $4,868,617.32 | $4,868,617.32 | $0.00 | No discrepancy |
| 1.05925 | Put net proceeds | $4,684,343.41 | $4,684,343.41 | $0.00 | No discrepancy |
| 1.05925 | Call protected cost | $4,756,204.20 | $4,756,204.20 | $0.00 | No discrepancy |
| 1.11500 | Unhedged proceeds | $4,794,500.00 | $4,794,500.00 | $0.00 | No discrepancy |
| 1.11500 | Forward proceeds | $5,077,010.00 | $5,077,010.00 | $0.00 | No discrepancy |
| 1.11500 | Money-market proceeds | $4,868,617.32 | $4,868,617.32 | $0.00 | No discrepancy |
| 1.11500 | Put net proceeds | $4,684,343.41 | $4,684,343.41 | $0.00 | No discrepancy |
| 1.11500 | Call protected cost | $4,995,929.20 | $4,995,929.20 | $0.00 | No discrepancy |
| 1.17075 | Unhedged proceeds | $5,034,225.00 | $5,034,225.00 | $0.00 | No discrepancy |
| 1.17075 | Forward proceeds | $5,077,010.00 | $5,077,010.00 | $0.00 | No discrepancy |
| 1.17075 | Money-market proceeds | $4,868,617.32 | $4,868,617.32 | $0.00 | No discrepancy |
| 1.17075 | Put net proceeds | $4,924,068.41 | $4,924,068.41 | $0.00 | No discrepancy |
| 1.17075 | Call protected cost | $4,995,929.20 | $4,995,929.20 | $0.00 | No discrepancy |

The call values are USD costs for the specification's reversed EUR-payable comparison, not proceeds from the company's actual EUR receivable. The fresh LLM interpreted that distinction correctly.

## 3. Hand Verification

### A. Forward Hedge

Named-range formula:

`FWD_PROCEEDS = FC_AMT × F0_in`

Arithmetic:

`= 4,300,000 × 1.1807`

`= $5,077,010.00`

**Reconciliation:** The hand calculation agrees with both the workbook and independent LLM result.

### B. Money-Market Hedge

**Step 1 — Borrow EUR today**

`MM_BORROW_FC = FC_AMT / (1 + R_FC × T_DAYS/360)`

`= 4,300,000 / (1 + 0.02930 × 365/360)`

`= 4,300,000 / 1.0297069444`

`= €4,175,945.42`

**Step 2 — Convert the EUR borrowing into USD**

`MM_USD_NOW = MM_BORROW_FC × S0_in`

`= 4,175,945.421364 × 1.1150`

`= $4,656,179.14`

**Step 3 — Invest USD to settlement**

`MM_PROCEEDS = MM_USD_NOW × (1 + R_USD × T_DAYS/360)`

`= 4,656,179.144821 × (1 + 0.0450 × 365/360)`

`= 4,656,179.144821 × 1.045625`

`= $4,868,617.32`

**Reconciliation:** All three hand-calculated money-market steps agree with the workbook and independent LLM.

### C. Put Hedge at `S_T = 1.1150`

The put premium is paid upfront and carried forward to settlement under the specification.

Upfront premium:

`PUT_PREM_TOTAL = FC_AMT × PREM_PUT`

`= 4,300,000 × 0.0245`

`= $105,350.00`

Settlement-equivalent premium:

`= PUT_PREM_TOTAL × (1 + R_USD × T_DAYS/360)`

`= 105,350 × (1 + 0.0450 × 365/360)`

`= $110,156.59`

Protected gross proceeds at the 1.1150 strike:

`= FC_AMT × K_PUT`

`= 4,300,000 × 1.1150`

`= $4,794,500.00`

Net put proceeds:

`= $4,794,500.00 - $110,156.59`

`= $4,684,343.41`

**Reconciliation:** The hand calculation agrees with the workbook and independent LLM. The FX Hedging Lab reports $4,689,150.00 because it subtracts the $105,350 upfront premium directly rather than carrying the premium to settlement; the difference is $4,806.59.

## 4. Reconciliation and Findings

No material numerical discrepancy exists between the independent LLM and the workbook for the forward, money-market, put, call-comparison, or unhedged calculations at the three tested settlement rates.

The independent LLM also reproduced the model's parity diagnostic:

- Bank forward quote: `1.1807 USD/EUR`
- CIP-implied forward: `1.1322366 USD/EUR`
- Rate gap: `0.0484634 USD/EUR`
- USD effect on the EUR 4.3 million receivable: approximately `$208,392.68`

The quoted forward produces more USD proceeds than the money-market hedge because the bank quote is materially above the CIP-implied forward calculated from the supplied spot and sovereign-rate proxies. This is not reconciled away; the bank quote remains the required `F0_in`, and the model appropriately reports a parity `FLAG`.

## 5. Specification Retrospective

The independent run demonstrated that the specification and market-data memo are sufficiently complete for an outside model to reproduce the major hedge calculations. The LLM did not make a material arithmetic error, but it still had to make several judgments that reveal opportunities for a stronger version of the specification.

First, the largest judgment concerns the bank forward quote. The documents instruct the model to retain `F0_in = 1.1807`, while the supplied spot and interest-rate inputs imply a forward of approximately `1.1322366`. The independent LLM correctly kept the bank quote, but it had to assume that the quote would be executable when making its recommendation. A version 2 specification would state explicitly that the quoted forward should be treated as executable for model comparison while also requiring confirmation before an actual transaction.

Second, the money-market hedge uses sovereign yields as proxies for corporate borrowing and investment rates. The independent LLM correctly identified that a private company may not be able to borrow EUR at the German government yield or invest USD at the Treasury rate. A stronger specification would label these as modeling proxies in the input contract itself and distinguish modeled proceeds from executable corporate funding proceeds.

Third, the specification and market-data memo describe the 4.50% USD rate with slightly different source labels: Federal Reserve H.15 in the specification and the U.S. Treasury Daily Par Yield Curve in the memo. The numerical rate is the same, so this did not affect the model, but a version 2 document set would use one authoritative source description consistently.

Fourth, the specification does not explicitly state an intermediate rounding policy. The independent LLM retained full precision and rounded final dollar amounts to cents. A revised specification would make that convention explicit so every independent implementation follows the same rounding approach.

Finally, the call comparison is clearly identified as a reversed EUR-payable case, and the independent LLM handled it correctly. Even so, a future specification could make the output label even more explicit by stating that call figures are USD costs and must never be ranked as receivable proceeds.

These are documentation improvements rather than evidence that the core calculations failed. The independent production test reproduced the workbook successfully while still identifying where a future specification could require less judgment.

## Appendix A — Raw Independent LLM Output

# **Independent FX Hedge Analysis and CFO Recommendation**

**Documents used exclusively:**

- *FX Hedging Model Specification — Payroll Software* (`spec(1).md`)
- *Payroll-Software FX Hedging — Market Data Memo* (October 27, 2026)

All calculations below were independently performed using the ten inputs and calculation methods provided in these two documents. No external market data, existing workbook, or outside sources were used. The calculated results were subsequently compared against the documents' validation figures.

## **1. Executive summary**

The U.S. payroll-software company expects to receive €4,300,000 from a Spanish bank in 365 days. Because its reporting currency is USD, the company faces the risk that depreciation of the euro will reduce the dollar value of its receivable.

Recommended strategy — Forward hedge

# **$5,077,010.00**

Locked USD proceeds at settlement, assuming the bank honors its quoted forward rate.

Advantage over money-market hedge

## **$208,392.68**

Sensitivity scenarios won

## **11 of 11**

Advantage over put minimum

## **$392,666.59**

Exchange rate uncertainty

## **Eliminated**

The **forward hedge is the recommended strategy** because it produces the highest USD proceeds across all eleven required sensitivity scenarios, eliminates exchange rate uncertainty, and requires no option premium under the specified assumptions.

However, the bank's quoted forward rate differs materially from the covered interest parity (CIP) implied forward rate. The CFO should confirm that the bank's quote remains executable before entering into the contract.

## **2. Model inputs**

The following ten inputs are taken directly from the market data memo and model specification.

| Named range | Value     | Unit        |
| ----------- | --------- | ----------- |
| `FC_AMT`    | 4,300,000 | EUR         |
| `S0_in`     | 1.1150    | USD/EUR     |
| `F0_in`     | 1.1807    | USD/EUR     |
| `R_USD`     | 4.50%     | Annual rate |
| `R_FC`      | 2.930%    | Annual rate |
| `K_PUT`     | 1.1150    | USD/EUR     |
| `K_CALL`    | 1.1150    | USD/EUR     |
| `PREM_PUT`  | 0.0245    | USD/EUR     |
| `PREM_CALL` | 0.0448    | USD/EUR     |
| `T_DAYS`    | 365       | Days        |

2026-10-27-payroll-software-market-data-memo (1)(1).md

### **Interest rate convention**

The specification requires simple annual interest using the ACT/360 day-count convention, while maintaining a contractual settlement horizon of 365 days.

**Time factor:**

\\[ t=\frac{365}{360}=1.013888889 \\]

**USD interest accumulation factor:**

\\[ 1+0.0450\times\frac{365}{360} \\]

\\[ =\boxed{1.045625} \\]

**EUR interest accumulation factor:**

\\[ 1+0.02930\times\frac{365}{360} \\]

\\[ =\boxed{1.0297069444} \\]

These factors are used consistently throughout the money-market hedge, covered interest parity calculation, and option premium calculations.

## **3. Forward hedge**

The forward hedge allows the company to lock in the exchange rate at which its €4,300,000 receivable will be converted into USD in 365 days.

**Formula:**

\\[ FWD\_{PROCEEDS}=FC\_{AMT}\times F0\_{in} \\]

**Calculation:**

\\[ =4,300,000\times1.1807 \\]

\\[ \boxed{\\$5,077,010.00} \\]

| Forward hedge component              | Result            |
| ------------------------------------ | ----------------- |
| EUR receivable                       | €4,300,000.00     |
| Contracted forward rate              | 1.1807            |
| Locked USD proceeds                  | **$5,077,010.00** |
| Upfront premium                      | $0.00             |
| Exchange rate exposure at settlement | None              |

Regardless of whether the euro appreciates or depreciates, the company receives $5,077,010 under the modeled forward contract.

The bank's quoted forward rate is intentionally retained rather than replaced by the CIP-implied forward rate, as required by both documents.&#x20;

spec(1).md



## **4. Money-market hedge**

The money-market hedge involves borrowing euros today, converting the borrowed euros into USD, and investing the USD until settlement. The future EUR receivable is then used to repay the EUR loan and accrued interest.

### **Step 1 — Borrow EUR today**

Calculate the present value of the EUR receivable.

\\[ MM\_{BORROW\\\_FC}= \frac{FC\_{AMT}}{1+R\_{FC}\times T\_{DAYS}/360} \\]

\\[ =\frac{4,300,000}{1+0.02930\times365/360} \\]

\\[ =\frac{4,300,000}{1.0297069444} \\]

\\[ \boxed{€4,175,945.42} \\]

The company borrows €4,175,945.42 at inception.

At settlement, the loan balance grows to €4,300,000, allowing the company to repay the entire loan using the contractual receivable.

### **Step 2 — Convert borrowed EUR into USD**

Convert the borrowed euros using the inception spot exchange rate.

\\[ MM\_{USD\\\_NOW}=MM\_{BORROW\\\_FC}\times S0\_{in} \\]

\\[ =4,175,945.421364\times1.1150 \\]

\\[ \boxed{\\$4,656,179.14} \\]

### **Step 3 — Invest USD until settlement**

Invest the converted USD at the specified USD annual interest rate of 4.50%.

\\[ MM\_{PROCEEDS}=MM\_{USD\\\_NOW} \times(1+R\_{USD}\times T\_{DAYS}/360) \\]

\\[ =4,656,179.144821\times1.045625 \\]

\\[ \boxed{\\$4,868,617.32} \\]

### **Money-market hedge transaction flow**

At inception

**Borrow EUR**

# **€4,175,945.42**

Annual EUR interest rate: 2.930%

**Convert EUR to USD**

# **$4,656,179.14**

Spot exchange rate: 1.1150 USD/EUR

**Invest USD for 365 days**

Annual USD interest rate: 4.50%

At settlement

USD investment matures

## **$4,868,617.32**

Final USD proceeds

EUR loan is repaid

## **€4,300,000.00**

Using the EUR receivable

The money-market hedge eliminates exchange rate uncertainty under the modeled rates but produces less USD than the quoted forward hedge.

The calculation also relies on sovereign interest rates as proxies for actual corporate borrowing and investment rates. Consequently, these proceeds should be interpreted as an estimate rather than a guaranteed corporate funding outcome.&#x20;

2026-10-27-payroll-software-market-data-memo (1)(1).md



## **5. Covered interest parity analysis**

Covered interest parity is used to determine whether the quoted forward rate is consistent with the spot exchange rate and the interest rate differential between USD and EUR.

### **Step 1 — Calculate the implied forward rate**

\\[ F\_{IMPLIED}=S0\_{in}\times \frac{1+R\_{USD}\times T\_{DAYS}/360} {1+R\_{FC}\times T\_{DAYS}/360} \\]

\\[ =1.1150\times \frac{1+0.0450\times365/360} {1+0.02930\times365/360} \\]

\\[ =1.1150\times\frac{1.045625}{1.0297069444} \\]

\\[ \boxed{1.1322366\text{ USD/EUR}} \\]

### **Step 2 — Calculate the parity gap**

\\[ PARITY\_{GAP}=F\_{IMPLIED}-F0\_{in} \\]

\\[ =1.1322365857-1.1807 \\]

\\[ \boxed{-0.0484634\text{ USD/EUR}} \\]

The specification establishes an acceptable absolute parity gap of 0.00005 USD/EUR.

\\[ |{-0.0484634}|>0.00005 \\]

**Covered interest parity validation**

FLAG

Bank quoted forward

**1.1807000**

CIP-implied forward

**1.1322366**

Parity gap

**−0.0484634**

Permitted absolute gap

**0.0000500**

This flag is an expected diagnostic result under the specification, not a calculation error.&#x20;

spec(1).md



### **Step 3 — Calculate the proceeds difference**

\\[ MM\_{FWD\\\_GAP}=MM\_{PROCEEDS}-FWD\_{PROCEEDS} \\]

\\[ =4,868,617.32-5,077,010.00 \\]

\\[ \boxed{-\\$208,392.68} \\]

The quoted forward hedge produces $208,392.68 more than the money-market hedge.

The difference can also be independently reconciled through the parity gap:

\\[ FC\_{AMT}\times(F\_{IMPLIED}-F0\_{in}) \\]

\\[ =4,300,000\times(-0.04846341435) \\]

\\[ =-\\$208,392.68 \\]

The two calculation methods agree.

The bank quote should not be substituted with the implied forward rate because doing so would violate the specification. Nevertheless, the CFO should verify the quoted rate before executing the hedge.

## **6. EUR put option hedge**

A EUR put option gives the company the right, but not the obligation, to sell its euros at the strike price of 1.1150 USD/EUR.

This protects the company's receivable against euro depreciation while allowing it to benefit from euro appreciation.

### **Step 1 — Calculate the upfront put premium**

\\[ PUT\_{PREM\\\_TOTAL}=FC\_{AMT}\times PREM\_{PUT} \\]

\\[ =4,300,000\times0.0245 \\]

\\[ \boxed{\\$105,350.00} \\]

### **Step 2 — Carry the premium to settlement**

The specification requires the premium to be carried forward using the USD interest accumulation factor so that all strategies are compared on a settlement-date basis.

\\[ PUT\_{PREM\\\_FV}=105,350\times1.045625 \\]

\\[ \boxed{\\$110,156.59} \\]

The additional settlement-equivalent cost associated with paying the premium upfront is:

\\[ 110,156.59-105,350.00 \\]

\\[ =\\$4,806.59 \\]

### **Step 3 — Calculate the protected minimum proceeds**

\\[ PUT\_{FLOOR}=FC\_{AMT}\times K\_{PUT} -PUT\_{PREM\\\_FV} \\]

\\[ =(4,300,000\times1.1150)-110,156.59375 \\]

\\[ =4,794,500-110,156.59375 \\]

\\[ \boxed{\\$4,684,343.41} \\]

### **Step 4 — Calculate proceeds at different exchange rates**

The general put hedge formula is:

\\[ PUT\_{NET\\\_PROCEEDS}= FC\_{AMT}\times\max(S_T,K\_{PUT}) -PUT\_{PREM\\\_FV} \\]

Put net proceeds

Unhedged proceeds

**Scenario A — EUR depreciates by 5%**

Ending spot rate: 1.05925 USD/EUR.

The company exercises the put because the strike price is higher than the ending spot rate.

\\[ =4,300,000\times1.1150-110,156.59 \\]

\\[ =\boxed{\\$4,684,343.41} \\]

**Scenario B — EUR remains unchanged**

Ending spot rate: 1.1150 USD/EUR.

\\[ =4,300,000\times1.1150-110,156.59 \\]

\\[ =\boxed{\\$4,684,343.41} \\]

**Scenario C — EUR appreciates by 5%**

Ending spot rate: 1.17075 USD/EUR.

The company allows the option to expire and sells its euros at the more favorable market rate.

\\[ =4,300,000\times1.17075-110,156.59 \\]

\\[ =\boxed{\\$4,924,068.41} \\]

Although the put provides downside protection and upside participation, the premium reduces its net proceeds. Even at the highest ending exchange rate within the required sensitivity range, the put produces less than the forward hedge.

## **7. EUR call option comparison**

The specification explicitly states that the EUR call is intended for a reversed exposure involving a EUR payable rather than the current EUR receivable.

A EUR call protects a company that needs to purchase euros against an appreciation of the euro. Its results must therefore be interpreted as USD **costs**, not USD proceeds.

### **Step 1 — Calculate the upfront call premium**

\\[ CALL\_{PREM\\\_TOTAL}=FC\_{AMT}\times PREM\_{CALL} \\]

\\[ =4,300,000\times0.0448 \\]

\\[ \boxed{\\$192,640.00} \\]

### **Step 2 — Carry the premium to settlement**

\\[ CALL\_{PREM\\\_FV}=192,640\times1.045625 \\]

\\[ \boxed{\\$201,429.20} \\]

The settlement-equivalent financing or opportunity cost of the upfront premium is $8,789.20.

### **Step 3 — Calculate the maximum protected cost**

\\[ CALL\_{CAP}=FC\_{AMT}\times K\_{CALL} +CALL\_{PREM\\\_FV} \\]

\\[ =(4,300,000\times1.1150)+201,429.20 \\]

\\[ \boxed{\\$4,995,929.20} \\]

### **Step 4 — Calculate protected costs across scenarios**

\\[ CALL\_{PROTECTED\\\_COST}= FC\_{AMT}\times\min(S_T,K\_{CALL}) +CALL\_{PREM\\\_FV} \\]

Call-protected cost

Unhedged payable cost

| Ending exchange rate | Call decision          | Total USD cost |
| -------------------- | ---------------------- | -------------- |
| 1.05925 (−5%)        | Allow option to expire | $4,756,204.20  |
| 1.11500 (0%)         | At the strike price    | $4,995,929.20  |
| 1.17075 (+5%)        | Exercise option        | $4,995,929.20  |

The call is not an appropriate primary hedge for the company's existing receivable and is therefore excluded from the receivable strategy comparison chart, consistent with the specification.&#x20;

spec(1).md



## **8. Unhedged position**

If the company chooses not to hedge, the USD value of its receivable depends entirely on the exchange rate at settlement.

**Formula:**

\\[ UNHEDGED\_{PROCEEDS}=FC\_{AMT}\times S_T \\]

| Scenario              | Calculation         | USD proceeds  |
| --------------------- | ------------------- | ------------- |
| EUR depreciates 5%    | 4,300,000 × 1.05925 | $4,554,775.00 |
| EUR remains unchanged | 4,300,000 × 1.11500 | $4,794,500.00 |
| EUR appreciates 5%    | 4,300,000 × 1.17075 | $5,034,225.00 |

The total difference between the lowest and highest unhedged outcomes within the required sensitivity range is:

\\[ 5,034,225-4,554,775 \\]

\\[ \boxed{\\$479,450.00} \\]

The unhedged strategy avoids option premium costs but leaves the company fully exposed to exchange rate movements.

Each one-percentage-point movement relative to the inception spot rate changes the dollar value of the receivable by $47,945.

## **9. Complete sensitivity analysis**

The specification requires exactly eleven ending exchange rates from −5% to +5% of the inception spot exchange rate in one-percentage-point increments.

The ending exchange rate for each scenario is calculated as:

\\[ S_T=S0\_{in}\times(1+\\%\text{ change}) \\]

### **Receivable hedge sensitivity table**

All monetary amounts are USD proceeds at the 365-day settlement date.

| Change | Ending spot | Unhedged     | Forward      | Money-market | EUR put      |
| ------ | ----------- | ------------ | ------------ | ------------ | ------------ |
| −5%    | 1.05925     | 4,554,775.00 | 5,077,010.00 | 4,868,617.32 | 4,684,343.41 |
| −4%    | 1.07040     | 4,602,720.00 | 5,077,010.00 | 4,868,617.32 | 4,684,343.41 |
| −3%    | 1.08155     | 4,650,665.00 | 5,077,010.00 | 4,868,617.32 | 4,684,343.41 |
| −2%    | 1.09270     | 4,698,610.00 | 5,077,010.00 | 4,868,617.32 | 4,684,343.41 |
| −1%    | 1.10385     | 4,746,555.00 | 5,077,010.00 | 4,868,617.32 | 4,684,343.41 |
| 0%     | 1.11500     | 4,794,500.00 | 5,077,010.00 | 4,868,617.32 | 4,684,343.41 |
| +1%    | 1.12615     | 4,842,445.00 | 5,077,010.00 | 4,868,617.32 | 4,732,288.41 |
| +2%    | 1.13730     | 4,890,390.00 | 5,077,010.00 | 4,868,617.32 | 4,780,233.41 |
| +3%    | 1.14845     | 4,938,335.00 | 5,077,010.00 | 4,868,617.32 | 4,828,178.41 |
| +4%    | 1.15960     | 4,986,280.00 | 5,077,010.00 | 4,868,617.32 | 4,876,123.41 |
| +5%    | 1.17075     | 5,034,225.00 | 5,077,010.00 | 4,868,617.32 | 4,924,068.41 |

### **Receivable hedge comparison chart**

ForwardMoney-marketEUR putUnhedged

$4.5M$4.7M$4.8M$5M$5.2M1.059251.070401.081551.092701.103851.115001.126151.137301.148451.159601.17075

The sensitivity analysis demonstrates that:

- The forward hedge generates the highest proceeds in all eleven scenarios.
- The forward and money-market hedges remain constant regardless of the ending spot rate.
- The EUR put maintains a protected minimum of $4,684,343.41 when the ending exchange rate is at or below the strike.
- The EUR put participates in appreciation above the strike, less the settlement-equivalent premium.
- The unhedged position changes linearly with the ending spot exchange rate.

These results satisfy the sensitivity relationships required by Section 6 of the specification.

### **EUR call sensitivity table — reversed payable exposure**

The call is presented separately because it measures the cost of purchasing euros rather than proceeds from selling euros.

| Change | Ending spot | Unhedged payable cost | Call-protected cost |
| ------ | ----------- | --------------------- | ------------------- |
| −5%    | 1.05925     | $4,554,775.00         | $4,756,204.20       |
| −4%    | 1.07040     | $4,602,720.00         | $4,804,149.20       |
| −3%    | 1.08155     | $4,650,665.00         | $4,852,094.20       |
| −2%    | 1.09270     | $4,698,610.00         | $4,900,039.20       |
| −1%    | 1.10385     | $4,746,555.00         | $4,947,984.20       |
| 0%     | 1.11500     | $4,794,500.00         | $4,995,929.20       |
| +1%    | 1.12615     | $4,842,445.00         | $4,995,929.20       |
| +2%    | 1.13730     | $4,890,390.00         | $4,995,929.20       |
| +3%    | 1.14845     | $4,938,335.00         | $4,995,929.20       |
| +4%    | 1.15960     | $4,986,280.00         | $4,995,929.20       |
| +5%    | 1.17075     | $5,034,225.00         | $4,995,929.20       |

## **10. Interactive hedge comparison**

The following calculator uses the independently derived formulas and the ten specified model inputs. Its default sensitivity range is limited to the required ±5%.

EUR exchange rate movement

# **0%**

−5%

0%

+5%

Ending spot rate (USD/EUR)

# **1.11500**

USD receivable proceeds

Forward

$5,077,010.00

Money-market

$4,868,617.32

EUR put

$4,684,343.41

Unhedged

$4,794,500.00

Bars are measured from $0 to $5.2 million.

**Forward advantage over unhedged**

At selected settlement rate

## **$282,510.00**

Separate payable comparison

EUR call-protected cost

**$4,995,929.20**

Reset to inception spot rate

## **11. Break-even analysis**

Although the forward hedge dominates within the required sensitivity range, calculating break-even exchange rates helps explain when alternative strategies could become more attractive.

### **Unhedged position versus forward hedge**

Set the unhedged proceeds equal to the forward proceeds:

\\[ 4,300,000\times S_T=5,077,010 \\]

\\[ S_T=\frac{5,077,010}{4,300,000} \\]

\\[ \boxed{S_T=1.1807} \\]

The unhedged position would only outperform the forward hedge if the euro appreciated above 1.1807 USD/EUR, approximately 5.89% above the inception spot rate.

### **EUR put versus forward hedge**

Above the put strike price, set the net put proceeds equal to the forward proceeds.

\\[ 4,300,000\times S_T-110,156.59375 =5,077,010 \\]

\\[ S_T=\frac{5,077,010+110,156.59375} {4,300,000} \\]

\\[ \boxed{S_T=1.2063178} \\]

The EUR put would only outperform the forward hedge if the euro appreciated above approximately 1.20632 USD/EUR, representing appreciation of approximately 8.19% from the inception spot rate.

### **Break-even rates relative to the sensitivity range**

Both break-even points occur outside the specified ±5% sensitivity range.

These break-even calculations are supplementary analyses derived entirely from the provided inputs. They are not exchange rate forecasts.

## **12. Comparison of hedging alternatives**

**Minimum USD proceeds within the sensitivity range**

Scale: $0 to $5.2 million

Forward

$5,077,010.00

Money-market

$4,868,617.32

EUR put

$4,684,343.41

Unhedged

$4,554,775.00

| Comparison                 | Forward    | Money-market | EUR put                        | Unhedged   |
| -------------------------- | ---------- | ------------ | ------------------------------ | ---------- |
| Minimum modeled proceeds   | $5,077,010 | $4,868,617   | $4,684,343                     | $4,554,775 |
| Maximum modeled proceeds   | $5,077,010 | $4,868,617   | $4,924,068                     | $5,034,225 |
| Proceeds at unchanged spot | $5,077,010 | $4,868,617   | $4,684,343                     | $4,794,500 |
| Downside protection        | Full       | Full         | Protected floor                | None       |
| Upside participation       | No         | No           | Yes                            | Yes        |
| Upfront option premium     | None       | None         | $105,350                       | None       |
| Requires EUR borrowing     | No         | Yes          | No                             | No         |
| Exchange rate certainty    | Full       | Full         | Minimum guaranteed under model | None       |

The forward hedge offers the strongest combination of modeled proceeds and certainty. The EUR put offers greater flexibility, but its premium and lower protected floor make it less attractive under the specified market inputs.

## **13. Independent validation and reconciliation**

The independently calculated results were compared against the check figures contained in the specification and market data memo.

| Named output                    | Independent result | Document check figure | Status                |
| ------------------------------- | ------------------ | --------------------- | --------------------- |
| `FWD_PROCEEDS`                  | $5,077,010.00      | $5,077,010.00         | Match                 |
| `MM_BORROW_FC`                  | €4,175,945.42      | €4,175,945.42         | Match                 |
| `MM_USD_NOW`                    | $4,656,179.14      | $4,656,179.14         | Match                 |
| `MM_PROCEEDS`                   | $4,868,617.32      | $4,868,617.32         | Match                 |
| `F_IMPLIED`                     | 1.1322366          | 1.1322366             | Match                 |
| `PARITY_GAP`                    | −0.0484634         | −0.0484634            | Match                 |
| `PUT_PREM_TOTAL`                | $105,350.00        | $105,350.00           | Match                 |
| `CALL_PREM_TOTAL`               | $192,640.00        | $192,640.00           | Match                 |
| Put premium at settlement       | $110,156.59        | $110,156.59           | Match                 |
| Call premium at settlement      | $201,429.20        | Not stated            | Independently derived |
| `PUT_FLOOR`                     | $4,684,343.41      | $4,684,343.41         | Match                 |
| `CALL_PROTECTED_COST` at strike | $4,995,929.20      | Not stated            | Independently derived |
| `MM_FWD_GAP`                    | −$208,392.68       | −$208,392.68          | Match                 |
| `PARITY_STATUS`                 | FLAG               | FLAG                  | Expected result       |

2026-10-27-payroll-software-market-data-memo (1)(1).md

### **Specification validation checklist**

| Rule | Validation requirement                       | Result                       |
| ---- | -------------------------------------------- | ---------------------------- |
| 1    | All ten input names included                 | Satisfied in analysis        |
| 2    | Workbook formulas reference named ranges     | Not independently verifiable |
| 3    | Forward proceeds equal $5,077,010            | Pass                         |
| 4    | Three money-market steps and correct results | Pass                         |
| 5    | CIP calculation and expected diagnostic flag | Pass — expected FLAG         |
| 6    | Correct money-market and forward difference  | Pass                         |
| 7    | Correct put and call premiums                | Pass                         |
| 8    | Correct put floor and upside behavior        | Pass                         |
| 9    | Exactly eleven sensitivity scenarios         | Pass                         |
| 10   | Flat forward/MM and linear unhedged results  | Pass                         |
| 11   | No Excel formula errors                      | Not independently verifiable |
| 12   | Every gray output cell contains a formula    | Not independently verifiable |

**Validation conclusion:** All independently verifiable numerical results and sensitivity relationships agree with the supplied documents.

Because this analysis was restricted to the specification and market data memo, the underlying Excel workbook was not inspected. Therefore, workbook-specific requirements involving named ranges, cell formulas, formatting, table naming, and Excel errors cannot be independently certified.

### **Reconciliation of the FX Hedging Lab put result**

The market data memo reports a difference between the FX Hedging Lab's put proceeds and the workbook's put proceeds.

| Component                | FX Hedging Lab, as reported | Independent calculation |
| ------------------------ | --------------------------- | ----------------------- |
| Gross protected proceeds | $4,794,500.00               | $4,794,500.00           |
| Premium deducted         | $105,350.00                 | $110,156.59             |
| Net protected proceeds   | $4,689,150.00               | $4,684,343.41           |
| Difference               |                             | $4,806.59               |

The difference is entirely attributable to the treatment of the option premium. The Lab deducts the upfront premium directly, while the specification requires carrying the premium forward to the settlement date.

The independent calculation follows the specification. The Lab itself was not accessed; its reported results were obtained solely from the market data memo.&#x20;

2026-10-27-payroll-software-market-data-memo (1)(1).md



## **14. Assumptions, ambiguities, and limitations**

### **Assumptions explicitly stated in the documents**

| Assumption                                | Treatment in this analysis                     |
| ----------------------------------------- | ---------------------------------------------- |
| Settlement horizon                        | Exactly 365 days                               |
| Interest calculation                      | Simple interest using ACT/360                  |
| Receivable collection                     | Entire €4,300,000 is collected on time         |
| Hedge coverage                            | 100% of the receivable                         |
| Currency conversion                       | Entire EUR receipt is converted into USD       |
| Option strikes                            | Both equal inception spot rate                 |
| Option premiums                           | Paid upfront and carried forward to settlement |
| Transaction fees and bid-ask spreads      | Excluded                                       |
| Taxes and counterparty credit adjustments | Excluded                                       |
| Collateral and margin requirements        | Excluded                                       |

spec(1).md

### **Ambiguities and independent modeling decisions**

**1. Bank forward quote versus market-implied forward**

The bank's forward quote of 1.1807 is significantly higher than the independently calculated CIP-implied rate of 1.1322366.

I retained the quoted rate as explicitly instructed. I assumed it would be available for execution when calculating the forward proceeds, but the two documents do not independently establish that the bank will honor that rate at execution.

This is the most significant limitation affecting the recommendation.

**2. Actual corporate borrowing and investment rates**

The interest rates provided are sovereign-market yields rather than confirmed corporate borrowing and investment rates.

I assumed the company could borrow EUR at 2.930% and invest USD at 4.50% for modeling purposes. Actual money-market proceeds may differ if the company's available rates are different.

**3. USD interest rate source discrepancy**

The specification identifies Federal Reserve H.15 one-year Treasury constant maturity data as its intended USD rate source, while the market data memo identifies the U.S. Treasury Daily Par Yield Curve.

Both documents provide the same numerical rate of 4.50%. I used the value and final source identified in the market data memo. This discrepancy does not affect the numerical calculations.

**4. Option premium availability**

The put and call premiums are scenario-provided values, not verified executable market quotes.

I retained both premiums as instructed and assumed they applied to the full €4,300,000 amount.

**5. EUR call exposure**

The actual exposure is a EUR receivable, whereas the EUR call calculation assumes a EUR payable.

I interpreted the EUR call as a hypothetical reversed exposure of the same notional amount, in accordance with the specification. Its protected costs were calculated separately rather than incorrectly treating them as receivable proceeds.

**6. Rounding convention**

The documents do not explicitly establish an intermediate rounding policy.

I retained full calculation precision throughout the analysis and rounded final monetary amounts to two decimal places. Exchange rates are displayed to the precision appropriate for each calculation.

**7. Sensitivity range and probability**

The required ±5% sensitivity range is a scenario analysis rather than a statistical forecast. No probabilities are provided for the individual scenarios.

I did not assume that the scenarios were equally likely or calculate probability-weighted expected proceeds. The forward's superiority is established within the tested range, not across every theoretically possible future exchange rate.

## **15. Final recommendation to the CFO**

**To:** Chief Financial Officer\
**Subject:** FX Hedging Recommendation for €4,300,000 Receivable

I recommend that the company enter into a 365-day forward contract to sell the entire €4,300,000 receivable at the quoted exchange rate of 1.1807 USD/EUR, subject to confirmation that the bank's quoted rate remains available and executable.

The forward hedge locks in USD proceeds of $5,077,010.00, eliminating uncertainty associated with changes in the EUR/USD exchange rate. It produces the highest proceeds across all eleven sensitivity scenarios, outperforming the money-market hedge by $208,392.68 and the EUR put's protected minimum proceeds by $392,666.59.

Even under the most favorable exchange rate scenario within the required sensitivity range, representing a 5% appreciation of the euro, the forward produces $42,785.00 more than the unhedged position and $152,941.59 more than the EUR put.

The money-market hedge provides an alternative means of eliminating exchange rate exposure but generates lower proceeds and depends on the company's ability to borrow and invest at the modeled sovereign-market rates. The EUR put provides downside protection while preserving upside participation; however, its settlement-equivalent premium of $110,156.59 reduces its attractiveness. The EUR call is not recommended because it is designed to protect a EUR payable rather than the company's existing EUR receivable.

The primary concern is the significant difference between the bank's quoted forward rate of 1.1807 and the covered interest parity implied rate of 1.1322366. This discrepancy produces a parity diagnostic flag and should be investigated before the company commits to the forward contract. If the bank cannot honor the quoted rate, the hedge alternatives should be recalculated using the revised executable quote.

Subject to this verification, the forward hedge provides the strongest combination of financial certainty, downside protection, and USD proceeds under the specified assumptions.

## **16. Final conclusion**

### **Final strategy comparison**

**Forward hedge**

Locked proceeds

### **$5,077,010.00**

**Money-market hedge**

Locked modeled proceeds

### **$4,868,617.32**

**EUR put hedge**

Protected minimum

### **$4,684,343.41**

**Unhedged**

Minimum tested proceeds

### **$4,554,775.00**

Numerical reconciliation

All supplied check figures match

CIP diagnostic

Expected FLAG

Recommended strategy

**Forward hedge**

The independent analysis confirms that the numerical check figures contained in the two provided documents are consistent with their stated inputs and formulas.

**The forward hedge is the preferred strategy, delivering locked settlement proceeds of $5,077,010.00, provided that the quoted forward rate is confirmed by the bank.**
