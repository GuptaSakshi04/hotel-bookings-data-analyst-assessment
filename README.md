# Hotel Bookings — Data Analyst Technical Assessment

> **Submitted by:** Sakshi Gupta  
> **Date:** April 2026  
> **Dataset:** Hotel Bookings (Kaggle) — 119,390 bookings · 33 columns · 2015–2017  
> **Tools:** Python · pandas · matplotlib · seaborn · Power BI

---

##  Repository Structure

```
hotel-bookings-data-analyst-assessment/
│
├── README.md                              ← You are here
├── hotel_bookings.csv                     ← Source dataset
├── Hotel_EDA_SakshiGupta.ipynb            ← Full annotated analysis notebook
│
├── dashboard/
│   ├── hotel_bookings-dashboard.pbix         ← Power BI dashboard file
│
└── charts
```

---

##  Objective

Analyse a real-world hotel bookings dataset to identify trends, patterns, and
actionable business insights that could improve revenue management, reduce
cancellations, and optimise operations.

---

##  Dataset Overview

| Property | Detail |
|---|---|
| Source | [Kaggle — Hotel Bookings Analysis](https://www.kaggle.com/datasets/thedevastator/hotel-bookings-analysis) |
| Raw rows | 119,390 |
| Clean rows | 119,208 |
| Columns | 33 |
| Period | 2015 – 2017 |
| Hotel types | City Hotel (66.4%) · Resort Hotel (33.6%) |
| Overall cancellation rate | 37.1% |
| Average Daily Rate | €102 / night |

---

##  Process

### Step 1 — Data Cleaning

| Issue | Fix Applied |
|---|---|
| `children` — 4 missing values | Filled with 0 (no children) |
| `country` — 488 missing values | Filled with `'Unknown'` |
| `agent` — 16,340 missing values | Filled with 0 (no agent used) |
| `company` — 112,593 missing values | Filled with 0 (individual booking) |
| 180 rows with zero guests | Dropped — impossible bookings |
| 2 rows with negative or extreme ADR | Dropped — ADR < 0 or > €5,000 |
| Months stored as unordered strings | Converted to ordered categorical (Jan → Dec) |

All cleaning was applied to a copy of the raw data. The original `df_raw` was
never modified. An assertion confirmed zero missing values after cleaning.

### Step 2 — Feature Engineering

Six new columns were created from existing data:

| New Column | Formula | Purpose |
|---|---|---|
| `total_nights` | `weekend_nights + week_nights` | Actual stay length |
| `total_revenue` | `adr × total_nights` | Revenue proxy per booking |
| `total_guests` | `adults + children + babies` | Group size |
| `room_match` | `reserved_type == assigned_type` | Operational accuracy flag |
| `lead_bucket` | `pd.cut(lead_time, 6 bands)` | Categorical lead time groups |
| `guest_type` | `map(is_repeated_guest)` | Readable loyalty label |

### Step 3 — Exploratory Data Analysis

- **Univariate analysis** — histograms and box plots for 6 numeric columns;
  frequency charts for 4 categorical columns
- **Bivariate analysis** — cancellation rate across hotel type, market segment,
  deposit type, and customer type; ADR vs nights scatter
- **Correlation matrix** — 10 numeric variables ranked by relationship strength
  with `is_canceled`

---

##  Key Insights

### Insight 1 — Lead Time Predicts Cancellation

Guests who book more than 90 days in advance cancel over 55% of the time,
while last-minute bookings (≤7 days) cancel at just 11% — meaning the hotel's
earliest bookings are its least reliable revenue.

| Lead Time | Cancellation Rate |
|---|---|
| ≤ 7 days | 11.0% |
| 8 – 30 days | 27.9% |
| 31 – 90 days | 37.7% |
| 91 – 180 days | 44.7% |
| 181 – 365 days | 55.5% |
| > 365 days | 67.7% |

**Recommendation:** Introduce a tiered deposit policy — require a larger
non-refundable deposit for bookings made more than 90 days in advance.
Send re-confirmation emails at 90 days and 30 days before arrival.

---

### Insight 2 — Revenue by Market Segment

Despite Online TA dominating with 47% of all bookings, Direct and Aviation
segments match it almost exactly on revenue per booking (≈€385) while
carrying far lower cancel rates — 15.4% and 22.1% vs Online TA's 36.8% —
making them significantly more valuable per booking.

| Segment | Avg Revenue / Booking | Cancel Rate |
|---|---|---|
| Aviation | €387 | 22.1% |
| Direct | €385 | 15.4% |
| Online TA | €384 | 36.8% |
| Offline TA/TO | €356 | 34.3% |
| Groups | €243 | 61.1% |
| Corporate | €135 | 18.8% |
| Complementary | €8 | 12.2% |

**Recommendation:** Invest in direct booking incentives (loyalty discounts,
best-rate guarantees) to shift volume away from OTA channels and reduce
commission costs while maintaining revenue per booking.

---

### Insight 3 — Repeat Guest Loyalty

Repeat guests make up just 3.1% of all bookings yet cancel at only 14.7%
compared to 37.8% for new guests — if the hotel could convert even 5% more
guests into repeat visitors, it would meaningfully cut its overall cancellation
rate without acquiring a single new customer.

| Guest Type | Cancel Rate | Avg ADR | Avg Special Requests |
|---|---|---|---|
| New Guest | 37.8% | €103 | 0.57 |
| Repeat Guest | 14.7% | €65 | 0.63 |

**Recommendation:** Launch a loyalty programme targeting new-to-repeat guest
conversion. Even a small shift would deliver a measurable reduction in the
37.1% baseline cancellation rate.

---

### Insight 4 — Seasonal Pricing Patterns

Resort Hotels swing from €49/night in November to €181/night in August — a
271% seasonal gap — while City Hotels stay far more stable (€82 to €121),
suggesting Resort Hotels are heavily dependent on summer demand and are
leaving revenue on the table during shoulder months.

| Hotel | Peak Month | Peak ADR | Trough Month | Trough ADR | Swing |
|---|---|---|---|---|---|
| Resort Hotel | August | €181 | November | €49 | 271% |
| City Hotel | May | €121 | January | €82 | 48% |

**Recommendation:** Resort Hotels should run targeted shoulder-season
promotions in April–May and September–October. City Hotels should test
premium pricing aligned to local conference and event calendars.

---

### Insight 5 — Room Assignment Mismatch

1 in 8 guests (12.4%) receives a different room to what they booked, with
Complementary and Corporate segments hit hardest at 33% and 28% respectively —
an operational gap that directly drives negative reviews and is entirely within
the hotel's control to fix.

| Segment | Mismatch Rate |
|---|---|
| Complementary | 33.2% |
| Corporate | 28.1% |
| Direct | 16.8% |
| Offline TA/TO | 14.9% |
| Online TA | 9.7% |
| Groups | 9.5% |
| Aviation | 8.5% |

**Recommendation:** Audit room inventory management workflows to reduce
unplanned mismatches. Formalise a planned upgrade programme for loyalty
members — turning an operational failure into a retention tool.

---

##  Dashboard

An interactive Power BI dashboard summarises all five insights in a single
view with three KPI tiles and four charts.

- Open `dashboard/hotel_bookings_dashboard.pbix` in Power BI Desktop to interact with
  it — click any bar or segment to cross-filter the entire dashboard instantly

---

##  How to Run the Notebook

### Requirements

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

### Run locally

```bash
jupyter notebook hotel_eda_colab.ipynb
```

### Run in Google Colab

1. Go to [colab.research.google.com](https://colab.research.google.com)
2. Click **File → Upload notebook**
3. Upload `Hotel_EDA_SakshiGupta.ipynb`
4. Run Cell 2 to upload `hotel_bookings.csv` via the file picker
5. Click **Runtime → Run all**



##  Summary of Findings

- **37.1%** of all bookings are cancelled — the single biggest revenue risk
- **Lead time** is the strongest cancellation predictor (+0.29 correlation):
  bookings >90 days out cancel at 55–68% vs 11% for last-minute
- **Direct bookings** match Online TA in revenue per booking (€385 vs €384)
  but cancel at less than half the rate (15% vs 37%)
- **Repeat guests** cancel 2.6× less than new guests despite representing
  only 3.1% of all bookings — a significant untapped loyalty opportunity
- **Resort Hotels** have a 271% seasonal ADR swing — shoulder seasons
  represent a major revenue optimisation opportunity
- **12.4%** of guests receive a different room than booked — a fixable
  operational issue with direct impact on review scores

---

##  Tools Used

| Tool | Purpose |
|---|---|
| Python 3.10 | Core analysis language |
| pandas | Data cleaning, manipulation, grouping |
| numpy | Numerical operations |
| matplotlib | All custom charts and dashboard |
| seaborn | Heatmaps and distribution plots |
| Power BI Desktop | Interactive dashboard |
| Google Colab | Notebook environment |
| GitHub | Version control and submission |

---

