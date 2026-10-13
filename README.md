# Metro Affordability Pulse

**Can a median-income family afford the median-priced home in America's 100 largest metros?**

A Python analysis of housing affordability across the 100 largest U.S. metros for September 2026. It uses the NAR Housing Affordability Index (HAI) formula and the APPASA framework.

---

## 1. Ask
- **Problem:** First-time buyers fell to **21%** of all buyers (NAR 2025 Profile), the lowest share since 1981, while mortgage rates stayed near 7%.
- **Q1:** With **20% down**, which metros are affordable?
- **Q2:** With **10% down** (the typical first-time buyer), how many metros remain affordable?
- **Q3:** What would it take to break even? How far would price, income or the mortgage rate need to move?

## 2. Prepare
| Source | What it gives | Grain |
|---|---|---|
| [Realtor.com Inventory Core Metrics](https://www.realtor.com/research/data/) | Median listing price | Metro × month |
| [Census ACS 1-year 2024, B19113](https://data.census.gov/table/ACSDT1Y2024.B19113) | Median family income + margin of error | Metro |
| [Freddie Mac 30-yr rate (FRED MORTGAGE30US)](https://fred.stlouisfed.org/series/MORTGAGE30US) | Mortgage rate | Weekly → monthly |

## 3. Process
- Joined price to income on **CBSA code**, and to rates on **month**
- Averaged weekly rates into monthly rates
- Kept **Sep 2026** and the **100 largest metros** (by household rank)
- Asserts check row counts, unique keys and missing values after each join
- Unmatched rows are all micro areas (below the ACS 65k population threshold). All of the top 100 matched.

## 4. Analyze
**NAR HAI formula**
```
monthly payment = loan × r / (1 − (1 + r)^−360)     loan = price × (1 − down %), r = rate / 12
income needed   = payment × 12 / 0.25               (payment ≤ 25% of income)
HAI             = family income / income needed × 100   (100 = just affordable)
```
- **Formula check:** reproduces NAR's published HAI (**103.3 vs 103.2**)
- **Verdict rule:** uses the Census margin of error, so results are *Affordable*, *Not affordable* or *Too close to call*

## 5. Share
| Question | Result |
|---|---|
| Q1: 20% down | **47** affordable · **12** too close · **41** not |
| Q2: 10% down | **27** affordable · **10** too close · **63** not |
| Q2: cost of the smaller down payment | about **$262 more per month** (median) |
| Q3: break-even (median unaffordable metro) | price **−20%** *or* income **+25%** |
| Q3: rate what-if (10% down) | **19** affordable at 7.40% → **44** at 6% → **62** at 5% |

Charts are in `outputs/`. The slide deck is `Metro Affordability Pulse - Final.pptx`.

## 6. Act
- **NAR Research:** use listing-based HAI as an early monthly signal before closed-sale data comes out
- **Realtors:** watch for rate drops in metros near break-even, because that is where buyers return first
- **First-time buyers:** down-payment help matters. Going from 20% to 10% down cuts affordable metros from 47 to 27.

---

## Limitations
- Uses asking prices, not sale prices
- Income is from 2024 (the 2025 ACS release was delayed)
- One national rate; principal and interest only (no taxes or insurance)
- A single month (Sep 2026)

## Run it
```bash
python3 -m venv .venv && source .venv/bin/activate
pip install pandas numpy matplotlib
# put the 3 source files in data/, then open HAI-NAR.ipynb and Run All
```

## Structure
```
data/        raw sources (not committed if large)
HAI-NAR.ipynb  main notebook (Ask → Share)
outputs/     CSV results + charts
```

*Built with a team of Claude Code agents for drafting and QA. I made the questions, data choices and decisions, and I verified every number.* By Long Nguyen
