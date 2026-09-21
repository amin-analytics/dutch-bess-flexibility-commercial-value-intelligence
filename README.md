# Dutch BESS Flexibility & Commercial Value Intelligence

A data-driven analysis of **Battery Energy Storage System (BESS) flexibility, electricity-market value, algorithmic trading, and commercial economics** in the Dutch electricity market.

The project evaluates a **50 MW / 100 MWh, 2-hour BESS** across multiple analytical layers:

* Dutch electricity-market intelligence
* ENTSO-E day-ahead market data
* BESS dispatch optimization
* Wholesale energy arbitrage
* Revenue stacking
* Battery degradation
* Project-level commercial valuation
* Machine-learning price forecasting
* Forecast-driven algorithmic BESS trading
* Trading performance and risk metrics
* TenneT balancing-market data integration

The objective is to connect **market fundamentals, battery constraints, forecasting, trading decisions, balancing-market signals, and investment economics** into one coherent BESS analytical workflow.

Rather than focusing only on theoretical electricity-price spreads, the project evaluates how much value a utility-scale BESS could potentially capture while considering operational constraints, forecast uncertainty, battery degradation, market signals, and project economics.

---

## Key Results

Under the base commercial case:

| Metric                                   |       Result |
| ---------------------------------------- | -----------: |
| BESS power                               |        50 MW |
| BESS energy                              |      100 MWh |
| Duration                                 |      2 hours |
| Availability                             |          97% |
| Round-trip efficiency                    |          90% |
| Historical wholesale revenue, annualized | ~€3.98M/year |
| Base stacked revenue                     | ~€4.58M/year |
| CAPEX                                    |       €20.0M |
| Fixed O&M                                |  €0.60M/year |
| NPV                                      |      ~€8.84M |
| IRR                                      |      ~15.13% |
| Simple payback                           |  ~5.54 years |
| Revenue NPV break-even                   | ~€3.39M/year |
| NPV-neutral CAPEX                        |    ~€288/kWh |

The wholesale optimization results represent a **historical perfect-foresight commercial benchmark**, rather than a real-time trading forecast.

The forecast-driven trading model provides a separate view of how a BESS could perform when decisions are based on predicted rather than known future prices.

---

# Project Architecture

The project progresses from historical market intelligence toward operational optimization, commercial valuation, machine-learning forecasting, algorithmic trading, and balancing-market integration.

```text
Dutch Electricity Market Data
          │
          ▼
01 Market Intelligence
          │
          ├───────────────┐
          ▼               │
02 BESS Dispatch          │
   Optimization           │
          │               │
          ▼               │
03 Commercial Valuation   │
   & Revenue Stacking     │
                          │
Historical Prices         │
          │               │
          ▼               │
04 Price Forecasting ◄────┘
   LightGBM / 24h
          │
          ▼
05 Algorithmic BESS
   Trading Backtest
          │
          ├── BUY / SELL / WAIT
          ├── SOC constraints
          ├── PnL
          └── Risk metrics
          
TenneT API
    │
    ▼
06 Balancing-Market
   Data Integration
```

---

# Project Structure

```text
dutch-bess-flexibility-commercial-value-intelligence/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   ├── 01_market_intelligence.ipynb
│   ├── 02_bess_dispatch_optimization.ipynb
│   ├── 03_bess_valuation_revenue_stacking.ipynb
│   ├── 04_price_forecasting_signals.ipynb
│   ├── 05_bess_algorithmic_trading_backtest.ipynb
│   └── 06_tennet_imbalance_market_data.ipynb
│
├── data/
│   ├── raw/
│   └── processed/
│
├── figures/
│   ├── capex_sensitivity_npv.png
│   ├── commercial_scenario_npv.png
│   ├── june_2026_bess_dispatch_example.png
│   ├── monthly_bess_efc.png
│   ├── monthly_wholesale_arbitrage_revenue.png
│   └── revenue_sensitivity_npv.png
│
├── results/
│   ├── capex_sensitivity.csv
│   ├── commercial_scenario_analysis.csv
│   ├── final_commercial_kpis.csv
│   ├── monthly_bess_arbitrage_full_history.csv
│   ├── monthly_bess_arbitrage_scenarios.csv
│   ├── revenue_sensitivity.csv
│   ├── price_forecast_24h_lightgbm.csv
│   ├── bess_algorithmic_trading_backtest.csv
│   ├── bess_algorithmic_trading_summary.csv
│   ├── bess_algorithmic_trading_final_kpis.csv
│   └── tennet_settlement_prices_2026-08-01.csv
│
└── src/
    ├── data/
    ├── optimization/
    └── economics/
```

---

# 1. Market Intelligence

The first notebook analyzes Dutch day-ahead electricity prices at **15-minute resolution**.

The analysis uses historical Dutch electricity-market data covering **October 2025 through August 2026**.

## Analysis includes

* Dutch bidding-zone day-ahead electricity prices
* Price distributions
* Negative-price frequency
* Daily minimum and maximum prices
* Daily price spreads
* Price volatility
* Short-term price changes
* Time-of-day patterns
* Indicative 2-hour BESS charging/discharging opportunities

## Selected market observations

* Mean day-ahead price: approximately **€99.57/MWh**
* Negative-price intervals: approximately **4.7%**
* Maximum observed price: approximately **€902/MWh**
* Minimum observed price: approximately **-€500/MWh**
* Mean daily price spread: approximately **€158/MWh**

The analysis demonstrates substantial price volatility and recurring price-spread opportunities that can create potential value for flexible storage.

---

# 2. BESS Dispatch Optimization

The second notebook models a utility-scale:

* **50 MW / 100 MWh BESS**
* 2-hour duration
* 15-minute dispatch resolution
* 97% availability
* 90% round-trip efficiency
* 10–90 MWh SOC operating range
* Initial SOC of 10 MWh
* Final SOC of 10 MWh
* Maximum daily discharged energy equivalent to **1 EFC/day**

A **mixed-integer linear programming (MILP)** formulation is used to determine economically optimal charging and discharging decisions under the defined operational constraints.

## Operational constraints

The optimization includes:

* Battery energy balance
* State-of-charge limits
* Charging power limits
* Discharging power limits
* Availability-adjusted power
* Mutually exclusive charging/discharging operation
* Initial SOC
* Final SOC
* Maximum daily discharged energy
* Round-trip efficiency

The full historical optimization is implemented as **monthly MILP problems** to keep the 15-minute model computationally manageable.

This preserves the operational logic while avoiding an impractical full-history optimization formulation.

---

# 3. Commercial Valuation & Revenue Stacking

The third notebook translates the optimized wholesale opportunity into a project-level commercial model.

## Revenue

The model considers:

* Historical wholesale energy arbitrage
* Simplified balancing-market revenue uplift
* Commercial revenue scenarios

## Costs

* CAPEX
* Fixed O&M
* Battery degradation

## Valuation

The project evaluates:

* NPV
* IRR
* Simple payback
* Revenue sensitivity
* CAPEX sensitivity
* Revenue break-even
* CAPEX break-even

---

# Commercial Scenarios

Four commercial cases are modeled:

| Scenario | Revenue Multiplier | CAPEX €/kWh | O&M €/kWh-year |
| -------- | -----------------: | ----------: | -------------: |
| Upside   |              1.20x |        €180 |             €5 |
| Base     |              1.00x |        €200 |             €6 |
| Downside |              0.80x |        €225 |             €7 |
| Stress   |              0.65x |        €250 |             €8 |

The base revenue assumption combines the historical wholesale benchmark with a simplified **15% balancing revenue uplift**.

---

# Degradation Model

A simplified throughput-based battery degradation model is used.

Assumptions include:

* 3,000 equivalent full discharge cycles
* 80% end-of-life capacity
* Capacity degradation linked to annualized EFC
* Degradation capped at the defined end-of-life level

This represents a **commercial modeling assumption**, rather than a detailed electrochemical, cell-level, or manufacturer warranty model.

---

# 4. Price Forecasting & Trading Signals

The fourth notebook introduces a machine-learning forecasting layer to move beyond perfect foresight.

A **LightGBM gradient-boosting regression model** is trained to forecast the next **24 hours of Dutch 15-minute electricity prices**, corresponding to:

**96 future 15-minute intervals.**

## Feature engineering

The forecasting model uses time-series features including:

* Quarter-hour
* Hour-of-day cyclical features
* Day-of-week cyclical features
* Month
* Multiple lagged prices
* 1-hour rolling statistics
* 6-hour rolling statistics
* 24-hour rolling statistics

Rolling features are shifted to ensure that future observations do not leak into the training features.

## Validation methodology

A **chronological train/test split** is used rather than a random split.

This reflects the temporal structure of electricity markets and avoids using future observations to train a model evaluated on earlier observations.

## Forecast performance

The held-out test results were:

| Metric                          |    LightGBM |
| ------------------------------- | ----------: |
| RMSE                            | ~€42.24/MWh |
| MAE                             | ~€31.20/MWh |
| RMSE improvement vs persistence |      ~41.8% |
| MAE improvement vs persistence  |      ~39.7% |

The model therefore provides a materially stronger short-term forecasting baseline than a simple persistence forecast over the tested period.

## Trading signal

Forecasts are converted into a simple directional signal:

* **BUY** when the expected near-term price increase exceeds the threshold
* **SELL** when the expected near-term price decrease exceeds the threshold
* **WAIT** otherwise

The signal is intentionally simple so that the relationship between the forecast and the subsequent BESS dispatch decision remains transparent.

---

# 5. Algorithmic BESS Trading Backtest

The fifth notebook connects the machine-learning forecast to a simplified algorithmic BESS trading strategy.

The workflow is:

```text
24h Price Forecast
       │
       ▼
Expected Near-Term Price Movement
       │
       ▼
BUY / SELL / WAIT
       │
       ▼
BESS SOC & Power Constraints
       │
       ▼
Charge / Discharge / Hold
       │
       ▼
Realized PnL & Risk Metrics
```

The strategy uses the same core BESS constraints as the optimization model rather than assuming unlimited battery flexibility.

## Backtest results

For the tested August 2026 period:

| Metric                     |       Result |
| -------------------------- | -----------: |
| Forecast RMSE              |   €42.24/MWh |
| Forecast MAE               |   €31.20/MWh |
| Trading decision intervals |          906 |
| Realized cash PnL          |      ~€97.8k |
| Economic PnL               |      ~€97.6k |
| Energy charged             | ~1,143.8 MWh |
| Energy discharged          |   ~953.5 MWh |
| Initial SOC                |       10 MWh |
| Final SOC                  |       90 MWh |
| Maximum cash drawdown      |      ~€12.7k |
| Profitable days            |      10 / 13 |
| Profitable-day ratio       |       ~76.9% |
| Simple daily Sharpe        |        ~0.84 |

The backtest result should **not** be interpreted as an annual expected profit.

The test period is limited and differs from the full-month perfect-foresight benchmark in both time coverage and terminal SOC.

---

# Perfect-Foresight Benchmark vs Forecast-Driven Trading

A key distinction in the project is between:

### Perfect-foresight optimization

The MILP optimizer knows the historical future price path and therefore determines an economically optimal dispatch schedule.

This provides an **upper-bound market opportunity benchmark**.

### Forecast-driven trading

The algorithmic strategy only uses information available through the forecasting workflow and makes simplified BUY / SELL / WAIT decisions.

This provides a more realistic but intentionally simplified view of forecast-based BESS trading.

The two approaches therefore serve different purposes and should not be interpreted as directly equivalent performance measures.

---

# 6. TenneT Balancing-Market Data Integration

The sixth notebook demonstrates integration with the **official TenneT API** to retrieve Dutch balancing-market settlement information.

The notebook retrieves 15-minute settlement data including:

* Settlement prices
* Dispatch UP
* Dispatch DOWN
* Shortage
* Surplus
* Regulation state
* Regulating condition

## Example API dataset

For **1 August 2026**:

| Metric                    |      Result |
| ------------------------- | ----------: |
| Intervals retrieved       |          96 |
| UP regulation intervals   |          43 |
| UP & DOWN intervals       |          38 |
| DOWN regulation intervals |          15 |
| Average dispatch UP       | €155.74/MWh |
| Average dispatch DOWN     |  €83.92/MWh |
| Average shortage price    | €136.17/MWh |
| Average surplus price     | €119.11/MWh |

The TenneT data is aligned with the existing Dutch day-ahead dataset using **UTC timestamps**.

Six day-ahead observations were missing from the comparison window; these were identified explicitly as missing source observations rather than being treated as a timezone or merge error.

## Market relevance

The TenneT data provides an additional market-state signal that can support future BESS flexibility analysis.

Potential future applications include:

* Balancing-market opportunity assessment
* Reserve-capacity valuation
* Multi-market BESS optimization
* Opportunity-cost analysis
* Balancing vs wholesale revenue comparison
* Market-state forecasting
* Co-optimization of energy and balancing-market participation

The current notebook intentionally does **not** treat TenneT settlement prices as directly executable trading prices.

---

# Multi-Market BESS Opportunity

The overall analytical framework can be extended toward a multi-market BESS optimization architecture:

```text
Day-Ahead Market
       │
       ▼
Price Forecast
       │
       ├──────────────┐
       │              │
       ▼              ▼
Intraday Market   Balancing Market
       │              │
       └──────┬───────┘
              ▼
      Opportunity Cost
          Analysis
              │
              ▼
      BESS Co-Optimization
              │
              ▼
      Charge / Discharge
      / Reserve Capacity
              │
              ▼
       Revenue + Risk
```

Such an architecture would require additional market-specific assumptions around bidding, activation, availability, imbalance exposure, reserve requirements, and opportunity costs.

These elements are outside the current simplified trading model.

---

# Key Commercial Insight

The analysis demonstrates that a 50 MW / 100 MWh BESS can potentially capture meaningful value from Dutch electricity-market volatility under historical conditions.

However, the commercial value of storage depends on much more than the existence of attractive price spreads.

The key commercial question is:

> **How much market value can a BESS actually capture while respecting battery constraints, forecast uncertainty, degradation, opportunity cost, market rules, and investment economics?**

The project addresses this question progressively:

1. **Market intelligence** identifies volatility and price-spread opportunities.
2. **MILP optimization** establishes a constrained perfect-foresight benchmark.
3. **Commercial valuation** translates the opportunity into NPV, IRR, and payback.
4. **Price forecasting** introduces uncertainty and removes perfect foresight.
5. **Algorithmic backtesting** converts forecasts into operational trading decisions.
6. **TenneT integration** introduces balancing-market information for potential future multi-market optimization.

---

# Important Model Limitations

The project intentionally uses simplified assumptions in several areas.

## Perfect foresight

The wholesale optimization uses historical price information to determine the optimal dispatch schedule.

Therefore, its revenue should be treated as an **upper-bound opportunity benchmark**, not a forecast of real-world trading revenue.

## Forecasting

The LightGBM model is evaluated over a limited historical period and is not intended to represent a production-grade forecasting system.

Further development could include:

* Probabilistic forecasting
* Prediction intervals
* More extensive rolling-origin validation
* Additional fundamental features
* Weather and renewable-generation forecasts
* Cross-border market information
* Intraday price data

## Algorithmic trading

The trading backtest is deliberately simplified.

A production BESS trading engine would require additional considerations such as:

* Market bidding rules
* Order-book liquidity
* Transaction costs
* Bid/ask spreads
* Market gate closure times
* Forecast uncertainty
* Position management
* Intraday re-optimization
* Imbalance exposure
* Reserve obligations
* Battery degradation cost
* Opportunity cost of stored energy
* Availability constraints
* Market-specific settlement rules

## Balancing markets

TenneT settlement prices provide useful historical balancing-market information, but settlement prices are not assumed here to represent directly executable trading prices.

---

# Technology Stack

* **Python**
* **Pandas**
* **NumPy**
* **Matplotlib**
* **SciPy**
* **scikit-learn**
* **LightGBM**
* **HiGHS MILP solver**
* **Jupyter Notebook**
* **ENTSO-E Transparency Platform API**
* **TenneT API**
* **python-dotenv**

---

# Data Sources

The project uses market data from:

### ENTSO-E

Dutch day-ahead electricity-market data for the Netherlands bidding zone:

`10YNL----------L`

The data is processed at **15-minute resolution** for the historical analysis period.

### TenneT

Official TenneT API data is used for balancing-market settlement information, including regulation conditions and settlement-related price signals.

API credentials are stored locally and are **not committed to the repository**.

---

# Reproducibility

Install the required Python packages:

```bash
pip install -r requirements.txt
```

The market-data workflows require API credentials.

Store credentials locally in a `.env` file rather than committing them to GitHub.

Example:

```text
ENTSOE_API_TOKEN=your_entsoe_token_here
TENNET_API_KEY=your_tennet_api_key_here
```

The `.env` file should remain excluded through `.gitignore`.

---

# Notebooks

### `01_market_intelligence.ipynb`

Analyzes Dutch day-ahead electricity prices, market volatility, negative-price events, daily spreads, and indicative BESS arbitrage opportunities.

### `02_bess_dispatch_optimization.ipynb`

Optimizes constrained BESS charging and discharging using mixed-integer linear programming.

### `03_bess_valuation_revenue_stacking.ipynb`

Converts the historical wholesale opportunity into commercial scenarios using revenue stacking, degradation, CAPEX, O&M, NPV, IRR, and payback analysis.

### `04_price_forecasting_signals.ipynb`

Builds a LightGBM-based 24-hour, 15-minute electricity-price forecasting model and converts forecasts into transparent directional trading signals.

### `05_bess_algorithmic_trading_backtest.ipynb`

Backtests a forecast-driven BUY / SELL / WAIT BESS strategy under battery operating constraints and evaluates PnL, drawdown, profitable days, and Sharpe ratio.

### `06_tennet_imbalance_market_data.ipynb`

Demonstrates TenneT API integration and analyzes Dutch balancing-market settlement prices and regulation conditions alongside day-ahead market data.

---

# Outputs

## Figures

The project produces analytical figures including:

* Monthly wholesale arbitrage revenue
* Monthly equivalent full cycles
* BESS dispatch example
* Commercial scenario NPV
* CAPEX sensitivity
* Revenue sensitivity
* TenneT regulation-condition profile
* Day-ahead vs balancing-market price comparison

## Results

Key generated datasets include:

* Monthly BESS optimization results
* Full-history wholesale arbitrage results
* Commercial scenario analysis
* Final commercial KPIs
* CAPEX sensitivity
* Revenue sensitivity
* 24-hour LightGBM price forecasts
* Algorithmic BESS trading backtest
* Algorithmic trading summary and KPIs
* TenneT settlement-price data

---

# Overall Analytical Framework

The project combines five major BESS analytical disciplines:

| Area                 | Analytical Approach                              |
| -------------------- | ------------------------------------------------ |
| Market Intelligence  | Dutch day-ahead price analysis                   |
| Asset Optimization   | MILP-based BESS dispatch                         |
| Commercial Economics | Revenue stacking, NPV, IRR, CAPEX sensitivity    |
| Algorithmic Trading  | LightGBM forecasting and constrained backtesting |
| Balancing Markets    | TenneT settlement-price API integration          |

This creates an integrated framework spanning:

**Energy Markets → Asset Optimization → Forecasting → Trading → Revenue → Risk → Investment**

---

# Conclusion

This project demonstrates an end-to-end analytical approach to utility-scale BESS flexibility in the Dutch electricity market.

The analysis connects **ENTSO-E market data, BESS optimization, machine-learning forecasting, algorithmic trading, TenneT balancing-market data, revenue stacking, battery degradation, and project finance** within a single workflow.

The results indicate that Dutch electricity-market volatility can create substantial theoretical flexibility value for a 50 MW / 100 MWh BESS.

At the same time, realized commercial value depends critically on:

1. Achievable market revenue capture
2. Forecast accuracy and uncertainty
3. BESS operating constraints
4. Battery degradation
5. CAPEX and O&M
6. Market access and liquidity
7. Intraday and balancing-market opportunities
8. Opportunity cost of stored energy
9. Trading and dispatch strategy
10. Risk management

The project therefore treats BESS optimization as part of a broader **energy-market, asset-management, trading, and investment problem**, rather than as an optimization exercise in isolation.

---

## Author

**Amin**

BESS | Energy Markets | Flexibility | Energy Storage | Algorithmic Trading | Commercial Analytics

**Email:** [amin@bdcerts.com](mailto:amin@bdcerts.com)
