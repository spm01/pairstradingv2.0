# pairstradingv2.0
Updated version of my pairs trading strategy that applies cointegration filter before executing trades.

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
| `202X OOS COINT PXXX.png` | These are the graphs that I use to visualize the total PnL for each OOS test (labeled by year and p-value) |
All other files remain unchanged from my prior pairstradingv1.0 repository.

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

## A Note on Timing  
Initially, I was skeptical for using ANY 2023 data on my strategy because of the risk of 'double dipping' (information from the future influences decisions in the past).  
However, I realized that as long as my parameters remain unchanged for 2024/2025, then I don't encounter the double dip issue.  
Fundamentally, my strategies need to know the current average based on the prior 52/90 days which by definition needs a burn-in window.  

As long as I don't use future data to CHOOSE my parameters, then I'm safe.  

Moreoever, I was comforted when reading literature that discusses this EXACT problem from Marcos Lopez de Prado and Ernie Chan. Both researchers acknowledge the importance of initializing my backtesting with historical data.

Moving forward, if needed, I can remove training set observations that overlap with test set data.  

###The justification:  
To avoid an artificial blackout at the start of my OOS period, I used 2023 data to initialize the rolling state of the cointegration and Bollinger indicators. This ensure the backtest reflects real-world Day 1 state of a live trading bot.  
At no point did a decision in 2024 have access to data from that same year. Therefore, no future information was leaked into the trade execution. 

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

### COMPLETE  
This is an updated section to include the complete performance over 2024/2025 while accounting for a ~6month burn-in period during 2023 before strategy implementation. 

### P < 0.05
Throughout the entire year 2024 and through most of 2025, neither strategy executed any trades. 
This is another demonstration of cointegration being an effective tool to filter out potential trades but it does stymie additional profit generation.  

In the second half of 2025, we start to see some trading activity with rapid spikes through August and September before leveling off.  
Again, the Deviation strategy created higher returns than the traditional Bollinger band approach.  

**Total PnL:  
Deviation: 3.73%  
Bollinger: -1.54%**  

### P < 0.1  
Again, neither strategy executed any trades during the first year, until November 2024 which delivered ~2.5% profit to both strategies. Following this initial trade, both strategies followed similar trade patterns as [P < 0.05].  

The gains with [P < 0.1] seem to allow both strategies the necessary 'wiggle room' to trade enough to generate profit without being overly cautious.  
The Deviation strategy again proved to be superior compared to the traditional Bollinger band approach. The Bollinger band approach was initially on track to negative PnL but was saved only by the initial trade in Nov2024. 

**Total PnL:  
Deviation: 7.30%  
Bollinger: 1.12%**  

---

## Known Constraints

**Grid Search v Coarse-to-Fine v Ornstein-Uhlenbeck Half-Life**: Reading more literature on this topic, I've realized that there are better ways to test for the ideal window period and k-value instead of the simple grid or coarse-to-fine search. 

---

## Roadmap — PairsTradeV2.1

The next iteration will aim to address the ideal window period constraint.

---

## Notes

This is a second pass. It appears in both v1.0 and v2.0 that the second order volatility signal garners more value over the baseline Bollinger approach. :-)




