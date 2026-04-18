# 📈 Impact of FOMC Announcements on SPY Returns & Volatility

> An econometric study of how Federal Reserve monetary policy decisions shape U.S. equity market behavior — combining ARX and GARCH modeling on 15+ years of daily data.

---

## Overview

Financial markets watch the Federal Open Market Committee (FOMC) closely. But does the *direction* of a rate decision really move stock prices — or is the market already priced in?

This project investigates exactly that question, separating the impact of FOMC announcements on **returns** from their impact on **volatility**, using the SPY ETF (S&P 500 proxy) as the target asset over the period **2008–2025**.

The core hypothesis: in efficient markets, anticipated policy decisions don't move prices — but they *do* move uncertainty.

---

## Methodology

### 1. ARX Model — Abnormal Returns
An AutoRegressive model with eXogenous variables tests whether FOMC announcement days generate statistically abnormal equity returns:

$$r_t = \alpha + \phi r_{t-1} + \delta_0 D_t^{\text{meet}} + \delta_1 D_{t+1}^{\text{meet}} + \delta_2 D_{t+2}^{\text{meet}} + \theta(d\_rate_t \cdot D_t^{\text{meet}}) + \varepsilon_t$$

- HAC-robust standard errors (Newey-West, max 5 lags)
- 3-day event window: announcement day + 2 post-meeting trading days
- Interaction term to isolate the *size* of the rate shock

### 2. GARCH(1,1) — Time-Varying Volatility
A standard GARCH(1,1) model captures volatility clustering in daily returns:

$$h_t = \omega + \alpha\varepsilon_{t-1}^2 + \beta h_{t-1}$$

Model selection between GARCH(1,1), (2,1) and (2,2) based on AIC/BIC — GARCH(1,1) wins.

### 3. Volatility Regression — FOMC Event Study
GARCH-estimated conditional variance is regressed on FOMC event dummies and rate change magnitude:

$$\log(h_t) = \alpha + \delta_0 D_t^{\text{meet}} + \delta_1 D_{t+1}^{\text{meet}} + \delta_2 D_{t+2}^{\text{meet}} + \theta|d\_rate_t|$$

---

## Key Results

| Hypothesis | Finding |
|---|---|
| H1 — FOMC days increase volatility | ✅ Confirmed — rate magnitude strongly significant (p < 0.001) |
| H2 — Volatility spills over post-announcement | ✅ Confirmed — elevated at t+1 and t+2 |
| H3 — Larger moves → larger volatility response | ✅ Confirmed — coefficient on \|Δrate\| = 3.88 |

**Bonus finding:** Volatility is actually *lower* on the meeting day itself (coefficient = −1.20, p < 0.001) — explained by market microstructure: the cash market closes at 16:00, while the FOMC decision lands at 14:00–14:30, leaving only 90 minutes of reaction time.

> **Takeaway:** Markets efficiently price in anticipated policy moves (no return effect), but rate *surprises* generate persistent, measurable spikes in conditional variance.

---

## Data Sources

| Data | Source | Series |
|---|---|---|
| SPY daily prices | Yahoo Finance (`yfinance`) | Adjusted Close, 2008–2025 |
| Fed Funds Target Rate (upper) | FRED API | `DFEDTARU` |
| Fed Funds Target Rate (lower) | FRED API | `DFEDTARL` |

> ⚠️ **Note:** A FRED API key is required to download the rate data. Insert your key in the `API_KEY` variable in the data ingestion cell.  
> Get a free key at: https://fred.stlouisfed.org/docs/api/api_key.html

---

## 🛠️ Tech Stack

```
Python 3.x
├── pandas / numpy          — data wrangling
├── yfinance                — market data ingestion
├── statsmodels             — OLS with HAC errors, ACF plots
├── arch                    — GARCH estimation
└── matplotlib              — visualization
```

---

## 🚀 Getting Started

```bash
# 1. Clone the repository
git clone https://github.com/hous087S/fomc-volatility-study.git
cd fomc-volatility-study

# 2. Install dependencies
pip install pandas numpy matplotlib yfinance statsmodels arch

# 3. Add your FRED API key
# Open the notebook and set: API_KEY = "your_key_here"

# 4. Run the notebook
jupyter notebook ML_Trading_PortMan.ipynb
```

---

## 📁 Repository Structure

```
.
├── ML_Trading_PortMan.ipynb   # Main analysis notebook
└── README.md
```

---

## 📌 Limitations & Extensions

- **Intraday granularity:** Daily data blurs the 14:00–16:00 reaction window. Tick-level data would sharpen the event study.
- **International spillovers:** Extending to European or Asian indices would reveal cross-market transmission channels.
- **Time-varying sensitivity:** Rolling-window estimation could test whether markets have become better at anticipating the Fed over time.
- **Asymmetry:** An EGARCH or GJR-GARCH specification could test whether rate hikes and cuts have asymmetric volatility effects.
