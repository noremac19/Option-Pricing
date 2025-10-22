# Option Pricing App (European, Asian, Barrier) — Streamlit

This repository contains a **Streamlit** application and Python library for pricing options and computing **Greeks** using **Monte Carlo simulation** under a **Geometric Brownian Motion (GBM)** model. The app currently supports:

- **European options** (calls & puts)
- **Asian options** (arithmetic-average, calls & puts)
- **Barrier options**:
  - **Up-and-Out Call** (knock-out when price breaches barrier from below)
  - **Down-and-In Put** (knock-in when price breaches barrier from above)

It also provides **finite-difference Greeks** (Δ, Γ, Vega, Theta, Rho) and a simple **backtesting/portfolio** workflow.

> Primary code reference: `6811finalproject.py` (Streamlit app with an `Option` class and pricing methods).  
> The notebook `Final_code_fre.ipynb` includes environment/deployment helpers (e.g., launching Streamlit in Colab).

---

## Features

### Pricing Methods
- **Monte Carlo GBM** path simulation with risk-neutral discounting `exp(-r * T / day_counts)`.
- **European payoff** at maturity \( T \):  
  - Call: \( \max(S_T - K, 0) \)  
  - Put: \( \max(K - S_T, 0) \)
- **Asian payoff** using the **arithmetic average** over the simulated path.  
- **Barrier options** (*path-dependent*):  
  - **Up-and-Out Call**: option is knocked **out** if the simulated path **touches/exceeds** the barrier before expiry.  
  - **Down-and-In Put**: option is **activated** only if the path **drops to/below** the barrier before expiry.

### Greeks (finite differences)
- `calculate_delta()` — bump spot \( S_0 \) by \( \,\varepsilon \)
- `calculate_gamma()` — second-order central difference in \( S_0 \)
- `calculate_vega()` — bump volatility \( \sigma \)
- `calculate_theta()` — bump time-to-maturity \( T \)
- `calculate_rho()` — bump risk-free rate \( r \)

Each Greek re-prices with the same Monte Carlo routine to estimate the sensitivity.

### Streamlit UI
- **Home** page for single-option pricing & Greeks  
- **Portfolio Management** (maintains a list of options in session state)  
- **Backtesting** (basic structure; sample hooks for historical runs)  
- Inputs include: ticker (used for fetching spot or context), spot, strike, implied vol, time to maturity (trading days), rates, dividend yield, option style (call/put), and (for barrier types) barrier level.

---

## Model Overview

### GBM Dynamics
Simulated under risk-neutral measure with continuous dividend yield \( q \):
\[
S_t = S_0\,\exp\Big((r - q - \tfrac{1}{2}\sigma^2)\,t + \sigma\,W_t\Big)
\]

Paths are generated over \( n \) steps with step size \( \Delta t = T / (\text{day\_counts}\cdot n) \) and payoffs discounted at \( e^{-r T / \text{day\_counts}} \).

> **Note**: Pricing accuracy is sensitive to the number of simulations and the time discretization used for path-dependent options.

---

## Installation

```bash
# (recommended) create a fresh venv
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate

pip install -U pip
pip install streamlit yfinance numpy pandas scipy matplotlib
```

> The app uses only widely available packages: `numpy`, `pandas`, `scipy`, `matplotlib`, `streamlit`, and `yfinance`.

---

## How to Run

```bash
streamlit run 6811finalproject.py
```

This launches the Streamlit UI in your browser (default: http://localhost:8501).

---

## Usage Notes

- **Time to Maturity (`T`)**: The script treats `T` as **trading days** and uses `day_counts=252` by default. Adjust as needed.  
- **Implied Volatility** is assumed to be annualized (e.g., `0.25` for 25%).  
- **Dividend Yield (`q`)** is continuous-compounded.  
- **Number of Simulations**: Increase for more stable estimates (e.g., `10,000+`) especially for barrier/Asian options.  
- **Barriers**: Ensure you pass a valid `barrier` level for *Up-and-Out* and *Down-and-In* types.  
- **Portfolio page**: Uses Streamlit session state to store multiple options and display aggregate results.

---

## File Map

```
.
├── 6811finalproject.py      # Streamlit app + Option class (pricing + Greeks)
├── Final_code_fre.ipynb     # Colab/launch helpers for Streamlit environment
└── README.md                # This file
```

---

## API (Option class)

```python
opt = Option(
    option_type="european",     # "european" | "asian" | "up-and-out" | "down-and-in"
    ticker="AAPL",
    spot=175.0,
    strike=180.0,
    implied_vol=0.25,
    T=63,                       # trading days to expiry
    r=0.05,                     # annualized risk-free
    q=0.00,                     # annualized dividend yield
    day_counts=252,
    call_option=True,           # True=Call, False=Put
    position=1,                 # number of contracts/shares equivalent
    barrier=None                # e.g., 210.0 (required for barrier types)
)

price = opt.price_option(num_simulations=10000)
delta = opt.calculate_delta(num_simulations=10000)
gamma = opt.calculate_gamma(num_simulations=10000)
vega  = opt.calculate_vega(num_simulations=10000)
theta = opt.calculate_theta(num_simulations=10000)
rho   = opt.calculate_rho(num_simulations=10000)
```

---

## Limitations & Extensions

- **Discretization bias** for path-dependent options (Asian/Barrier) — consider finer time steps or exact formulas where available.  
- **Variance reduction** techniques (Antithetic variates, Control variates) can materially reduce MC error.  
- **Smile/Skew** not modeled — the current engine assumes a single constant volatility.  
- **Alternative models**: GBM could be replaced or extended with local/stochastic volatility, jump diffusions, etc.  
- **Calibration**: If you intend to calibrate \( \sigma \) or \( r \) from market data, add routines for fitting to vanilla surfaces.

---

## License

MIT License — free to use, modify, and distribute with attribution.
