# Portfolio Stress Tester: Markowitz Analysis on NSE Stocks

A Python project that builds a Markowitz mean-variance portfolio from five NSE-listed stocks and tests how fragile the "optimal" result is under stress.

**Main finding:** the optimised portfolios beat the average stock on risk, but a correlation shock raises their volatility by 26-30%, and the portfolio with the best historical Sharpe ratio lost more than the minimum-volatility one in the COVID crash.

## Data

| Item | Choice |
|---|---|
| Stocks | HDFCBANK (banking), HINDUNILVR (FMCG), IOC (energy), M&M (auto), SUNPHARMA (pharma) |
| Source | Yahoo Finance via `yfinance`; the `Close` column is already adjusted for splits and dividends |
| Period | 1 Jan 2018 to 29 Dec 2023 (daily), covering the COVID crash, the recovery and the 2022 rate-hike cycle |
| Returns | Simple daily returns (`pct_change`), annualised with 252 trading days |
| Risk-free rate | 7.3%, approximately the 10-year Indian G-sec yield, applied to the whole sample |

One stock per sector was chosen so that diversification could actually show up. Pairwise correlations in the sample range from about 0.17 to 0.42.

## Method

1. **Inputs.** Annualised mean returns and the annualised covariance matrix, from daily returns.
2. **Monte Carlo portfolios.** 10,000 random weight sets (long-only, summing to 1). For each: return = w · mean returns, volatility = sqrt(wᵀ Σ w), Sharpe = (return − risk-free) / volatility. Seed fixed at 42 for reproducibility.
3. **Two optimal portfolios.** Minimum volatility and maximum Sharpe, taken from the random set. The upper-left edge of the plotted cloud is the efficient frontier.
4. **Stress test 1, correlation shock.** The weights stay fixed. Each pairwise correlation is moved halfway towards 1, individual volatilities are unchanged, and portfolio risk is recomputed.
5. **Stress test 2, COVID replay.** The same fixed weights are run through the actual daily returns from 20 Jan to 23 Mar 2020 (roughly the pre-crash high to the market low).

# Results

**Base case (10,000 random portfolios, seed 42)**

| | HDFCBANK | HINDUNILVR | IOC | M&M | SUNPHARMA | Return | Volatility | Sharpe |
|---|---|---|---|---|---|---|---|---|
| Min volatility | 23.9% | 36.9% | 15.4% | 2.9% | 21.0% | 15.72% | 17.15% | 0.49 |
| Max Sharpe | 7.0% | 33.8% | 2.0% | 26.1% | 31.0% | 17.78% | 18.95% | 0.55 |

**Stress test 1: correlation shock (correlations moved halfway to 1)**

| | Base vol | Stressed vol | Increase | Base Sharpe | Stressed Sharpe |
|---|---|---|---|---|---|
| Min volatility | 17.15% | 22.29% | +29.9% | 0.49 | 0.38 |
| Max Sharpe | 18.95% | 23.87% | +26.0% | 0.55 | 0.44 |

**Stress test 2: COVID replay (20 Jan to 23 Mar 2020)**

| | Cumulative return | Worst day |
|---|---|---|
| Min volatility portfolio | −25.44% | −9.88% |
| Max Sharpe portfolio | −29.07% | −9.84% |
| Average of the five stocks | about −31% | about −10.3% |

**Conclusions**
- Diversification lowers risk: both portfolios have lower volatility than the weighted average of their stocks (about 26% and 28%).
- A correlation shock raises portfolio volatility by 26-30%, but diversification does not vanish.
- The max-Sharpe portfolio lost more in the crash than the min-volatility one, because it held 26% in M&M, the worst performer (−48%).
- Limitations: historical average returns, random-draw optimum, one risk-free rate, five stocks.
