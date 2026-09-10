# Dutch BESS Flexibility & Commercial Value Intelligence

A data-driven commercial analysis of a **50 MW / 100 MWh Battery Energy Storage System (BESS)** operating in the Dutch electricity market.

The project combines Dutch day-ahead electricity price analysis, constrained BESS dispatch optimization, revenue stacking, battery degradation, and project-level commercial valuation.

The objective is not simply to identify price spreads, but to estimate how much commercially meaningful value a BESS could capture while respecting operational constraints, degradation, and investment economics.

---

## Key Results

Under the base commercial case:

| Metric                                   |       Result |
| ---------------------------------------- | -----------: |
| BESS power                               |        50 MW |
| BESS energy                              |      100 MWh |
| Historical wholesale revenue, annualized | ~€3.98M/year |
| Base stacked revenue                     | ~€4.58M/year |
| CAPEX                                    |       €20.0M |
| Fixed O&M                                |  €0.60M/year |
| NPV                                      |      ~€8.84M |
| IRR                                      |      ~15.13% |
| Simple payback                           |  ~5.54 years |
| Revenue NPV break-even                   | ~€3.39M/year |
| NPV-neutral CAPEX                        |    ~€288/kWh |

These results should be interpreted as a **historical perfect-foresight commercial benchmark**, not as a real-time trading forecast.

---

## Project Structure

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
│   └── 03_bess_valuation_revenue_stacking.ipynb
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
│   └── revenue_sensitivity.csv
│
└── src/
    ├── data/
    ├── optimization/
    └── economics/
```

---

## 1. Market Intelligence

The first notebook analyzes Dutch day-ahead electricity prices at 15-minute resolution.

### Analysis includes

* Dutch bidding-zone day-ahead prices
* Negative-price frequency
* Price distribution
* Daily minimum and maximum prices
* Daily price spreads
* Price volatility
* Short-term price changes
* Indicative 2-hour charging/discharging spread opportunities

The analysis uses historical Dutch market data covering **October 2025 through August 2026**.

### Selected market observations

* Mean day-ahead price: approximately **€99.57/MWh**
* Negative-price intervals: approximately **4.7%**
* Maximum observed price: approximately **€902/MWh**
* Minimum observed price: approximately **-€500/MWh**
* Mean daily price spread: approximately **€158/MWh**

The results demonstrate substantial intraday price volatility that can create potential value for flexible storage.

---

## 2. BESS Dispatch Optimization

The second notebook models a:

* **50 MW / 100 MWh** BESS
* 2-hour duration
* 15-minute dispatch resolution
* 97% availability
* 90% round-trip efficiency
* 10–90 MWh SOC operating range
* Initial SOC of 10 MWh
* Final SOC returned to 10 MWh
* Maximum daily discharged energy equivalent to **1 EFC/day**

A mixed-integer linear programming (MILP) formulation is used to determine charging and discharging decisions.

### Operational constraints

The optimization includes:

* Battery energy balance
* SOC minimum and maximum
* Charging power limit
* Discharging power limit
* Mutually exclusive charging/discharging operation
* Availability-adjusted power
* Initial and final SOC
* Maximum daily discharged energy

The full-year optimization was intentionally implemented as **monthly MILP problems** because the full-year 15-minute formulation created a memory requirement that was impractical for the available computing environment.

This preserves the operational logic while keeping the model computationally manageable.

---

## 3. Commercial Valuation & Revenue Stacking

The third notebook translates the optimized wholesale opportunity into a simplified project-level commercial model.

The model considers:

### Revenue

* Historical wholesale arbitrage revenue
* A simplified balancing-market revenue uplift
* Revenue multipliers for commercial scenarios

### Costs

* CAPEX
* Fixed O&M
* Battery degradation

### Valuation

* NPV
* IRR
* Simple payback
* Revenue sensitivity
* CAPEX sensitivity

---

## Commercial Scenarios

Four commercial cases are modeled:

| Scenario | Revenue Multiplier | CAPEX €/kWh | O&M €/kWh-year |
| -------- | -----------------: | ----------: | -------------: |
| Upside   |              1.20x |        €180 |             €5 |
| Base     |              1.00x |        €200 |             €6 |
| Downside |              0.80x |        €225 |             €7 |
| Stress   |              0.65x |        €250 |             €8 |

The base revenue assumption combines the annualized wholesale benchmark with a simplified **15% balancing revenue uplift**.

---

## Degradation Model

A simplified throughput-based degradation model is used.

Assumptions:

* 3,000 equivalent full discharge cycles
* End-of-life capacity: 80%
* Capacity degradation driven by annualized EFC
* Capacity degradation capped at the 80% end-of-life level

This is intended as a **commercial modeling assumption**, not a detailed cell-level warranty or electrochemical degradation model.

---

## Key Commercial Insight

The analysis indicates that a 50 MW / 100 MWh BESS can capture meaningful value from Dutch electricity-market volatility under historical perfect-foresight conditions.

However, the commercial case is highly sensitive to achievable revenue capture and investment cost.

The key question is therefore not simply:

> Do attractive electricity-price spreads exist?

It is:

> Can sufficient value be captured across available markets while respecting battery constraints, degradation, opportunity cost, and investment economics?

The sensitivity analysis demonstrates that revenue capture has a material impact on project value, while CAPEX also strongly influences NPV and IRR.

---

## Important Model Limitation

The dispatch optimization uses **historical perfect foresight**.

This means the optimizer knows the historical price path when determining the economically optimal charging and discharging schedule.

Therefore, the optimized wholesale revenue should be interpreted as an:

**upper-bound market opportunity benchmark**

rather than a forecast of what a real-time BESS trading strategy would achieve.

A production trading strategy would require additional elements such as:

* Price forecasting
* Forecast uncertainty
* Real-time market signals
* Intraday trading
* Balancing-market participation
* Bid/offer constraints
* Opportunity-cost management
* More detailed degradation economics

These are intentionally outside the scope of this portfolio project.

---

## Technology Stack

* Python
* Pandas
* NumPy
* Matplotlib
* SciPy
* HiGHS MILP solver through `scipy.optimize.milp`
* Jupyter Notebook
* ENTSO-E market data

---

## Reproducibility

Install the required Python packages:

```bash
pip install -r requirements.txt
```

The market-data notebook requires an ENTSO-E API token.

Store the token locally in a `.env` file rather than committing it to GitHub.

Example:

```text
ENTSOE_API_TOKEN=your_token_here
```

The `.env` file should remain excluded through `.gitignore`.

---

## Notebooks

### `01_market_intelligence.ipynb`

Analyzes Dutch electricity-price volatility and identifies potential storage arbitrage opportunities.

### `02_bess_dispatch_optimization.ipynb`

Optimizes constrained BESS charging and discharging using MILP.

### `03_bess_valuation_revenue_stacking.ipynb`

Converts the operational opportunity into commercial scenarios and project-level valuation.

---

## Outputs

The project produces:

### Figures

* Monthly wholesale arbitrage revenue
* Monthly equivalent full cycles
* June 2026 example BESS dispatch
* Commercial scenario NPV
* CAPEX sensitivity
* Revenue sensitivity

### Results

* Monthly BESS optimization results
* Commercial scenario analysis
* Final commercial KPIs
* CAPEX sensitivity results
* Revenue sensitivity results

---

## Conclusion

This project connects **energy-market intelligence, BESS optimization, and commercial valuation** into one analytical workflow.

The main conclusion is that Dutch electricity-price volatility can create substantial theoretical flexibility value for a utility-scale BESS, but the investment case depends critically on:

1. Achievable revenue capture
2. Market participation strategy
3. Battery operating constraints
4. Degradation
5. CAPEX
6. Access to multiple revenue streams

The model therefore focuses on the commercial question behind BESS deployment rather than treating optimization as an end in itself.

---

## Disclaimer

This is an analytical portfolio project based on historical market data and simplified commercial assumptions. It is not investment advice, a trading recommendation, or a guarantee of future BESS revenues.
