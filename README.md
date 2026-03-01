# pairstradingv2.0
Updated version of my pairs trading strategy that applies cointegration filter before executing trades.
This is still a WIP for the application of how to overcome the timing issues (I don't want to double dip 2023 in-sample data with 2024/2025 out-of-sample data to compensate for the ~6month burn-in period).

This is an extension of my prior work on a pairs trading implementation built around a simple mean reversion strategy, using **Coke (KO)** and **Pepsi (PEP)** as the test pair.  
I remain invested in whether or not there exists a higher performing methodology between the 'devation' approach v. traditional bollinger bands approach. 

---

## Overview

This project compares two mean reversion strategies across 2024 and 2025 out-of-sample data:

- **Deviation Strategy**: signals trades using the standard deviation *of* the standard deviation (volatility of volatility)
- **Bollinger Band Strategy**: the traditional approach, signaling trades when the spread exceeds `mean ± (σ × k)`

---

## Project Structure

| File | Description |
|---|---|
| `oos_pairstrade_202X_coint.py` | Out-of-sample test scripts with updated cointegration testing for 2024 and 2025 |
| `202X OOS graph.png` | These are the graphs that I use to visualize the total PnL for each OOS test.
All other files remain unchanges from my prior pairstradingv1.0 repository.

---

## Strategy Functions

Two functions live in `pairstrade.py`:

**`run_strategy_deviation`**
Uses the deviation of the deviation to determine when to enter a trade. Requires an initial burn-in period to calculate the second-order deviation, which pushes the trading start date to approximately mid-year.

**`run_strategy_bollinger`**
Standard Bollinger Band implementation. Signals a trade when the spread relationship between KO and PEP exceeds a k-value multiple of the rolling standard deviation.

---
## Cointegration Testing
In brief, cointegration tests for a relationship between two or more time series variables (even if the individual time series are non-stationary! [that means that the statistical properties like mean/variance/std deviation don't change over time])  
As such, the cointegration tests for moving together over time and being binded by a stable equilibrium.  

Why is that important for this exercise? The reason lies within the volatile nature between stocks. It's important that trades are executed within the framework of mean reversion, meaning that asset prices (and price ratio) will revert back to a specific, stable, long-term relationship.  


---

---

## Results

### 2024
2024 was neither a success or a failure for either strategy.  Both ended the year around **+2–3% total PnL**.

### 2025
A completely different story. The deviation strategy finished at approximately **+12% total return**. The Bollinger approach failed to capture the relationship and returned roughly **-2% PnL**. Notably, the deviation strategy outperformed *despite* starting later in the year due to its burn-in requirement.

---
---

## Known Constraints

**Fixed start/end windows**: Both out-of-sample tests don't begin trading until approximately June of each respective year, cutting the opportunity set roughly in half. Despite this limitation, the deviation strategy still outperformed the Bollinger approach in 2025.

---

## Roadmap — PairsTradeV2

The next iteration will aim to address both constraints above:
- Rolling or expanding window approach to eliminate the hard start-date limitation (I'm not exactly sure how to integrate this).
- Pre-trade cointegration testing to filter out periods where the pair relationship has broken down

---

## Notes

This is a first pass. The goal was to validate whether a second-order volatility signal could add value over a baseline Bollinger approach, and in 2025 at least, the answer was yes.




