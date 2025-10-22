# WTI–Brent Pairs Trading Strategy (6811finalproject.py & Final_code_fre.ipynb)

This project implements a **pairs trading** strategy between **WTI** and **Brent crude oil futures** using a spread mean-reversion framework with potential extensions for volatility modeling and regime detection. Both the Python script (`6811finalproject.py`) and the Jupyter notebook (`Final_code_fre.ipynb`) contain equivalent methodologies, differing mainly in format (script vs notebook).

## Project Overview

The project aims to identify and exploit mean-reverting behavior in the **WTI–Brent spread**. It tests whether short-term deviations between the two highly correlated crude oil benchmarks can be traded profitably through a **statistical arbitrage** framework.


### Approach
1. Collect and preprocess historical price data for WTI and Brent futures.
2. Compute the **spread** and **z-score normalization**.
3. Identify trading signals when the z-score deviates significantly from zero.
4. Backtest the strategy, accounting for position changes, transaction costs, and daily returns.
5. Evaluate performance using metrics like Sharpe ratio, cumulative PnL, and portfolio value.

## Data Requirements

The project expects two input files:
| File | Required Columns | Description |
|------|------------------|--------------|
| `WTIData.csv` | `Date`, `PX_LAST`, `FUT_CUR_GEN_TICKER` | WTI front-month or active crude oil futures prices |
| `BrentData.csv` | `Date`, `PX_LAST`, `FUT_CUR_GEN_TICKER` | Brent front-month or active crude oil futures prices |

Data should include daily prices and be formatted with consistent date indexes. Missing values are forward-filled. Data is filtered to start from **2021-01-01** or later.

## Methodology

### 1. Preprocessing
- Convert dates to datetime objects and align both datasets by date.
- Forward-fill missing price data to maintain alignment.
- Compute the **price spread** (`WTI − Brent`).

### 2. Stationarity and Co-Integration
- The code may optionally perform Augmented Dickey-Fuller (ADF) tests to verify stationarity.
- A regression of WTI on Brent (`WTI ~ α + β × Brent`) provides hedge ratio insight.

### 3. Z-Score Standardization
- Compute z-score of the spread to identify deviations from the mean.
- Z-score is calculated as:
  ```
  z = (spread - mean(spread)) / std(spread)
  ```

### 4. Signal Generation
Trading signals are based on z-score thresholds:
| Condition | Signal | Position |
|------------|---------|-----------|
| z > +2 | Sell Spread | Short WTI / Long Brent |
| z < -2 | Buy Spread | Long WTI / Short Brent |
| Otherwise | Hold | No trade |

Alternative thresholds (e.g., ±1.5) can be used for more frequent trades.

## Backtesting Logic

The backtest iterates through time, opening or closing positions based on signal changes. Key components include:
- **Transaction Costs:** Deducted per trade (set as constants per leg).
- **Position Sizing:** Optionally based on volatility or fixed capital allocation.
- **Portfolio Tracking:** Tracks cash, open position value, and cumulative portfolio value.
- **Performance Metrics:** Computes daily returns, Sharpe ratio, and cumulative profit/loss (PnL).

## Performance Evaluation

The strategy computes several key performance indicators:
- **Final Portfolio Value**
- **Cumulative and Daily Returns**
- **Sharpe Ratio** (with optional 3% annual risk-free rate)
- **Max Drawdown**
- **Win Rate**
- **Trade Frequency and Average Holding Period** (if implemented)

Visualizations include:
- WTI vs Brent price chart
- Spread and z-score over time
- Cumulative PnL and portfolio value curves

## Installation & Dependencies

Install the required packages:
```
pip install numpy pandas matplotlib scipy statsmodels arch hmmlearn copulas
```
Optional packages for visualization and statistical modeling may include:
```
pip install seaborn plotly
```

## How to Run

### Running the Python Script
```
python 6811finalproject.py
```

### Running the Jupyter Notebook
1. Open `Final_code_fre.ipynb` in Jupyter or VS Code.
2. Execute cells sequentially to reproduce plots and metrics.

## Outputs

The analysis produces:
- **Spread time series** (WTI − Brent)
- **Z-score series**
- **Trading signals** (Buy/Sell/Hold)
- **PnL and Portfolio Value** over time
- **Performance summary table** with metrics

## Limitations & Future Extensions

- **Incomplete backtesting logic**: Some placeholder sections require user-defined contract sizing and transaction cost functions.
- **No volatility targeting**: Future versions could incorporate GARCH or realized volatility scaling.
- **No position scaling**: Positions are binary (in/out). Enhancements could include dynamic sizing based on z-score magnitude.
- **No leverage or margin consideration**: These can be added for realism.
- **Possible regime modeling**: GaussianHMM or Markov-switching models could be extended for adaptive thresholds.

## Disclaimer

This project is for **educational and research purposes only**. It does not constitute financial advice. Trading futures involves substantial risk of loss and is not suitable for all investors.

## License

MIT License — You are free to use, modify, and distribute this project with attribution.

<img width="432" height="644" alt="image" src="https://github.com/user-attachments/assets/cee5ab5e-0327-4af1-8cf1-c8f541e4896a" />
