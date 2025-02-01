# Spread-Trading
This repository contains a spread-reversion trading strategy simulation based on a given ETF pair. The strategy monitors the displacement between two related ETFs (based on recent returns) and trades under the assumption that the displacement will revert to the mean.
- Takes positions when the difference in M-day returns between ETFs exceeds a threshold.
- Shorts the ETF with a higher recent return and buys the one with a lower return.
- Positions are adjusted based on stop-loss.

## **Data**
Data Sources: Uses split- and dividend-adjusted closing prices for ETFs downloaded from Nasdaq using Quandl.\
⚠️ Due to GitHub size limits, the dataset is hosted locally on the Desktop/SpreadTrading/Data.

## **Strategy Sample**
- **Assets:** ETF pairs (e.g., `FTXL` and `SMH`)
- **Trading Period:** 2023-01-01 to 2024-11-15

## **Key Metrics**

**`entry_gross` (Gross Traded Cash at Entry):**
entry_gross = |position_x * px_entry_x| + |position_y * px_entry_y| 

**`N_t` (Minimum Rolling Dollar Volume):**
N_t = Median({V_t-15, V_t-14, ..., V_t-1}) 

**`cum_pnl`**: Realized PnL

**`open_pnl`**: Unrealized PnL  

## **Position Logic**

1. **No Position:**
    - If **|z| > g**, enter a position:
        - **Short `x`, Long `y`** or **Long `x`, Short `y`**.
    - Compute `position_x`, `position_y`, and deduct `trading_cost`.

2. **Has Position:**
    - If **|z| < j**, **flatten (exit position)**.
    - Else if **position side flips**, **flatten first**, then **enter new position**.

3. **Final Flatten:**
    - On the last trading day, close all positions.


## **Position Sizing**
Your strategy’s “trades” are equal-sized dollar amounts of X and Y to
the nearest integer number of shares, as close as possible to `N_t / (100 * price)` of each.
Note that Nt changes every day, so trade size will depend on which day
you open the position.


## **Stop-Loss Logic**
1. If **`open_pnl` < `s * entry_gross`**, trigger **stop-loss**:
    - **Flatten immediately**.
    - **No new positions until month-end**.
2. Resume trading on the first trading day of the next month.


## **Trading Costs**
- Assume a proportional trading cost parameter **ζ**.
- Entering loss is **ζ × gross traded entry position cash**.
- Exit cost is **ζ × gross traded exit position cash**.

---

## 🚀 **Performance Evaluation**
- Tracks mark-to-market PnL and cumulative returns.
- Analyzes strategy performance across different parameter values.
- The implementation includes parameter tuning and performance visualization to identify optimal strategy settings.

