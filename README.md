# Spread-Trading
This repository contains a spread-reversion trading strategy simulation based on a given ETF pair. The strategy monitors the displacement between two related ETFs (based on recent returns) and trades under the assumption that the displacement will revert to the mean.
## Trading Strategy Description:
Takes positions when the difference in M-day returns between ETFs exceeds a threshold.
Shorts the ETF with a higher recent return and buys the one with a lower return.
Positions are adjusted based on stop-loss.

## Data:
Data Sources: Uses split- and dividend-adjusted closing prices for ETFs downloaded from Nasdaq using Quandl.
⚠️ Due to GitHub size limits, the dataset is hosted locally on the Desktop/SpreadTrading/Data.

---

## **Strategy Universe**
- **Assets:** ETF pairs (e.g., `FTXL` and `SMH`)
- **Trading Period:** 2023-01-01 to latest available data

---

## **Key Metrics**

### **`entry_gross` (Gross Traded Cash at Entry)**
\[
\text{entry\_gross} = |\text{position\_x} \times \text{px\_entry\_x}| + |\text{position\_y} \times \text{px\_entry\_y}|
\]

### **`N_t` (Minimum Rolling Dollar Volume)**
\[
N_t := \text{Median} \left( \{V_{t-15}, V_{t-14}, \dots, V_{t-1} \} \right)
\]
- Compute the running trailing 15-trading-day median of volume for `X` over the sample period.

### **Profit and Loss Metrics**
- **`cum_pnl`**: Realized PnL
- **`open_pnl`**: Unrealized PnL  
\[
\text{PnL} = (\text{exit price}_x - \text{entry price}_x) \times \text{position}_x + (\text{exit price}_y - \text{entry price}_y) \times \text{position}_y - \text{trading costs}
\]

---

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

---

## **Position Sizing**
\[
\text{position}_x = \frac{N_t}{100 \times \text{price}_x}
\]
\[
\text{position}_y = \frac{N_t}{100 \times \text{price}_y}
\]

---

## **Stop-Loss Logic**
1. If **`open_pnl` < `s * entry_gross`**, trigger **stop-loss**:
    - **Flatten immediately**.
    - **No new positions until month-end**.
2. Resume trading on the first trading day of the next month.

---

## **Trading Costs**
- Assume a proportional trading cost parameter **ζ**.
- Immediate loss is **ζ × gross traded entry position cash**.
- Exit cost is **ζ × gross traded exit position cash**.

---

## 🚀 **Performance Evaluation**
Tracks mark-to-market PnL and cumulative returns.
Analyzes strategy performance across different parameter values.
The implementation includes parameter tuning and performance visualization to identify optimal strategy settings.

