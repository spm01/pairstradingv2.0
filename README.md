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
| `202X OOS COINT PXXX.png` | These are the graphs that I use to visualize the total PnL for each OOS test.
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
As such, the cointegration test evaluates the existence of a relationship moving together over time and being bound by a stable equilibrium.  

Why is that important for this exercise?  
The reason lies within the volatile nature between stocks. It's important that trades are executed within the framework of mean reversion, meaning that asset prices (and price ratio) will revert back to a specific, stable, long-term relationship.  

This statistical test relys a few key components.    

First, we need to define a time frame that the strategy can use to evaluate whether or not cointegration is present. That is done in the 'window' portion of the 'add_rolling_cointegration' function.  

Second, we need to define the p-value that ensures the cointegration relationship isn't random, but rather a pattern of continued trending in the same direction.  
We can choose the p-value we want to set depending on a number of factors including:  
How much confidence we have in our strategy  
The likelihood of the pair relationship being blown out  
How much risk we are willing to take (A higher p-value means higher risk [I chose 0.05 in the upload b/c of standard economic practice]).  

In theory, this also means we could artificially stimulate the trading strategy by lowering the p-value threshold which increases the likelihood of a trade and profit but also the likelihood of additional losses. 

For demonstration, in my results section, I attached the p-value < 0.05 AND p-value < 0.1 to compare the difference in implementation. 

---
## Results

### 2024  
### P < 0.05  
2024 was neither a success or a failure for either strategy because no trades were executed. On one hand, we could regard this as a failure because no profit was generated, BUT it also indicates that the cointegration test is working as intended by NOT TRADING when the pair is moving a non-integrated fashion.  

**Total PnL:  
Deviation: 0%      
Bollinger: 0%**  

### P < 0.1
This version of the cointegration test proved to be only marginally more successful than P < 0.05 because only one trade was executed in both strategies which generated small but meaningful PnL.  

A looser p-value does indeed perform as expected with additional trades being made (even if the total trades go from 0 trades to 1 trade).  
Crucially, the cointegration prevented the massive drawdown in June of 2024.

**Total PnL:  
Deviation: 2.66%    
Bollinger: 2.66%**  

### 2025  
### P < 0.05  
2025 proved to be a different scenario where more trades were executed starting in mid-July.  

Interestingly, the Deviation strategy again generated higher returns than the typical Bollinger band strategy.  
This lends itself to proving the Devation strategy as being a viable path for meaningful PnL.  

Both strategies encountered similar difficulties in August-September but the Deviation strategy recovered and delivered positive returns while the Bollinger strategy failed to recoup any losses.  

**Total PnL:  
Deviation: 3.73%  
Bollinger: -1.54%**  

### P < 0.1  
The major difference between P < 0.05 and P < 0.1 is a LARGER gap between the Deviation strategy and the Bollinger strategy.  

In mid-July, the Deviation strategy gained an additional ~0.5% PnL. This tracks with our expectation of P < 0.1 which allows our strategies to trade with lower confidence in cointegration leading to more/longer trades.  

Both strategies again encountered the same drawdown in mid-August but the Deviation strategy was again able to recoup losses while the Bollinger bands approach failed to deliver any meaningful gains.  

**Total PnL:  
Deviation: 4.64%  
Bollinger: -1.54%**  

---
---

## Known Constraints

**Fixed start/end windows**: Both out-of-sample tests don't begin trading until approximately June of each respective year, cutting the opportunity set roughly in half. Despite this limitation, the deviation strategy still outperformed the Bollinger approach in 2025. Still working on a fix for this issue. 

---

## Roadmap — PairsTradeV2.1

The next iteration will aim to address both constraints above:
- Rolling or expanding window approach to eliminate the hard start-date limitation (I'm not exactly sure how to integrate this).

---

## Notes

This is a second pass. It appears in both v1.0 and v2.0 that the second order volatility signal garners more value over the baseline Bollinger approach. :-)




