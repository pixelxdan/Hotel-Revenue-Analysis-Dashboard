# 🏨 Hotel Revenue Analysis Dashboard | Power BI
*An end-to-end Power BI project analyzing revenue, occupancy, RevPAR, ADR, and booking realisation across 25 hotels in 4 Indian cities.*

<p align="center">
<img src="Dashboard Images/Main Dashboard.png" width="100%" alt="Hotel Performance Dashboard">
</p>

> 📄 **Project at a glance**
>
> Read the **[Executive Summary](docs/Executive_Summary.md)** for a concise overview of the business problem, key findings, and recommendations.

**Tools:** Power BI · DAX · Star Schema Modeling · Power Query

<br>

## 💼 Business Problem
A hotel group operating 25 properties across Delhi, Mumbai, Hyderabad, and Bangalore wants a single view of how its hotels are performing. Management needs to understand where revenue comes from, how well rooms are being filled and priced, and how much revenue is lost to cancellations and no-shows.

Without a consolidated dashboard, it is hard to compare cities, hotel categories, room classes, and booking platforms, or to see how performance is changing week over week.

<br>

## 🎯 Objective
This is a portfolio case study. I built a Power BI dashboard on booking data to:
- Track core hospitality KPIs: **Revenue, RevPAR, ADR, Occupancy %, DSRN, and Realisation %**.
- Show week-over-week change for every KPI.
- Compare performance by city, hotel category, room class, booking platform, and weekday vs weekend.
- Quantify revenue lost to cancellations and no-shows.

<br>

## 📂 Dataset
The model uses the **AtliQ Hotels** sample dataset, a public hospitality case-study dataset with booking-level and daily capacity data.

| Item | Value |
|---|---|
| Bookings | 134,590 |
| Hotels | 25 (16 Luxury, 9 Business) |
| Cities | 4 (Mumbai, Bangalore, Hyderabad, Delhi) |
| Room classes | 4 (Standard, Elite, Premium, Presidential) |
| Booking platforms | 7 |
| Period | 1 May 2022 - 31 Jul 2022 (check-in dates, 14 weeks) |
| Booking statuses | Checked Out, Cancelled, No Show |
| Currency | INR (₹) |

<br>

## 🧩 Data Model
A **multi-fact star schema** with 2 fact tables, 3 dimension tables, and a dedicated measures table. All relationships are many-to-one with single-direction filtering.

| Table | Type | Purpose |
|---|---|---|
| `fact_bookings` | Fact | One row per booking: status, platform, ratings, revenue generated and realised |
| `fact_aggregated_bookings` | Fact | Daily successful bookings and capacity per hotel and room category |
| `dim_hotels` | Dimension | Property name, category (Luxury / Business), city |
| `dim_rooms` | Dimension | Room class |
| `dim_date` | Dimension | Date, week number, month, weekday / weekend flag |
| `key_measures` | Measures | All 26 DAX measures in one table |

Both fact tables connect to `dim_hotels`, `dim_rooms`, and `dim_date`, so one slicer filters booking-level and capacity-level numbers together.

<br>

## 📐 Key DAX Measures
The model has **26 measures**. The core ones:

```DAX
Revenue            = SUM(fact_bookings[revenue_realized])
Total Bookings     = DISTINCTCOUNT(fact_bookings[booking_id])
Occupancy %        = DIVIDE([Total Sucessful Bookings], [Total Capacity], 0)
ADR                = DIVIDE([Revenue], [Total Bookings], 0)
RevPAR             = DIVIDE([Revenue], [Total Capacity])
Cancellation %     = DIVIDE([Total cancelled bookings], [Total Bookings])
No Show rate %     = DIVIDE([Total no show bookings], [Total Bookings])
Realisation %      = 1 - ([Cancellation %] + [No Show rate %])
```

**Week-over-week change** is built with variables and filter-context logic, and the same pattern is reused for Revenue, RevPAR, ADR, Occupancy, DSRN, and Realisation:

```DAX
Revenue WoW change % =
VAR selv   = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw  = CALCULATE([Revenue], dim_date[wn] = selv)
VAR revpw  = CALCULATE([Revenue], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
RETURN
    DIVIDE(revcw, revpw, 0) - 1
```

Other measures: Cancellation counts, Booking % by Platform, Booking % by Room Class, Average Rating, DBRN, DSRN, DURN.

> **Note:** ADR is calculated as Revenue ÷ Total Bookings across all booking statuses, and Revenue uses `revenue_realized`.

<br>

## 📊 Dashboard
The report has **1 main page and 2 tooltip pages**.

**Main page - Hotel Performance Dashboard**
- **KPI cards with week-over-week change:** Revenue, RevPAR, DSRN, Occupancy %, ADR, Realisation %.
- **Revenue Contribution** by city.
- **Trends by Key Metric:** RevPAR, ADR, and Occupancy % by month and week.
- **Platform view:** Realisation % and ADR by booking platform.
- **Day-type table:** RevPAR, Occupancy %, ADR, and Realisation % for weekdays vs weekends.
- **Property table:** each hotel's revenue, bookings, and RevPAR.
- **Slicers:** City, Room Class, Month, Week Number.

**Tooltip pages**
- *Tooltip - Revenue:* weekly revenue trend by hotel category.
- *Tooltip - RevPAR:* RevPAR by week number and hotel category.

<br>

## 🔍 Key Findings

### 💰 Overall performance
- **Revenue ₹1.71 billion** from 134,590 bookings, with **ADR ₹12,696**, **RevPAR ₹7,347**, and **occupancy 57.9%**.
- **Realisation is 70.1%.** 24.8% of bookings were cancelled and 5.0% were no-shows. Realised revenue is ₹1.71B against ₹2.01B generated, so about **₹299M (14.9%) was not realised**.
- Performance was stable across the three months (occupancy 58.5% in May, 57.6% in June, 57.5% in July).

### 🌆 City performance
| City | Revenue | Share | ADR | Occupancy | RevPAR |
|---|---|---|---|---|---|
| Mumbai | ₹668.6M | 39.1% | ₹15,387 | 57.9% | ₹8,907 |
| Bangalore | ₹420.4M | 24.6% | ₹13,131 | 55.8% | ₹7,323 |
| Hyderabad | ₹325.2M | 19.0% | ₹9,322 | 58.1% | ₹5,414 |
| Delhi | ₹294.5M | 17.2% | ₹12,154 | 60.5% | ₹7,359 |

- **Mumbai** leads on revenue, ADR, and RevPAR.
- **Delhi** has the highest occupancy (60.5%) but the smallest revenue share, because it has fewer bookings.
- **Hyderabad** has the lowest ADR (₹9,322), so it fills rooms at lower prices.

### 🏷️ Category and room class
- **Luxury hotels generate 61.6% of revenue**, but they are 16 of the 25 hotels. Per available room, **Business hotels earn slightly more** (RevPAR ₹7,498 vs ₹7,256).
- **Presidential rooms** are 11.9% of bookings but **22.0% of revenue** (ADR ₹23,440).

### 📅 Weekday vs weekend
| | Occupancy | ADR | RevPAR |
|---|---|---|---|
| Weekday | 51.3% | ₹12,679 | ₹6,510 |
| Weekend | **73.6%** | ₹12,724 | **₹9,363** |

- Weekend occupancy is **22.3 percentage points higher**, but **ADR is almost the same**. Weekend demand is not being priced higher.

### 🌐 Booking platforms
- Platforms outside the named channels ("others") account for 40.9% of revenue, followed by MakeYourTrip (19.9%) and Logtrip (11.0%). Direct online and offline together are 15.0%.
- **Realisation is nearly the same everywhere** (69.8%-70.6% across platforms, 69.9%-70.3% across cities). Cancellations look like a hotel-wide issue, not a channel-specific one.

<br>

## 💡 Recommendations
1. **Raise weekend rates.** Weekend occupancy is 73.6% but ADR is flat versus weekdays, so there is room to price weekends higher.
2. **Drive weekday demand.** Weekday occupancy is 51.3%, so use weekday packages and corporate offers.
3. **Reduce cancellations at the policy level.** Since cancellation rates are about 25% in every city, category, and platform, use deposits, non-refundable rates, or tighter cancellation windows.
4. **Review Hyderabad pricing.** It has good occupancy but the lowest ADR.
5. **Grow Presidential and Premium room sales.** They earn the highest ADR.
6. **Increase direct bookings.** Direct channels are only 15.0% of revenue.

<br>

## ⚠️ Limitations
- The data covers only **3 months (May-Jul 2022)**, so there is no year-over-year comparison or seasonality view.
- There is **no cost or profit data**, so revenue metrics (RevPAR, ADR) are measured but profitability (e.g. GOPPAR) is not.
- Only **42.1% of bookings have a rating** (average 3.62), so rating analysis is limited.
- 40.9% of revenue comes from the "others" platform group, which hides the detail behind that channel.

<br>

## 📁 Repository Structure
```
Hotel-Revenue-Analysis-Dashboard
│
├── Dashboard
│   └── Hotel_Project.pbix
│
├── Dashboard Images
│   └── Hotel Dashboard.png
│
├── docs
│   └── Executive_Summary.md
│
└── README.md
```

To explore the report, open `Hotel_Project.pbix` in **Power BI Desktop**.

<br>

## 🧠 Skills Demonstrated
| Skill | Where Used |
|---|---|
| Multi-fact star schema modeling | 2 fact + 3 dimension tables |
| DAX with variables and filter context | Week-over-week change measures |
| Hospitality KPIs | RevPAR, ADR, Occupancy, DSRN, Realisation % |
| Interactive report design | Slicers, KPI cards, trend lines, combo chart |
| Custom tooltip pages | Revenue and RevPAR by hotel category |
| Business insight and recommendations | Executive Summary |

<br>

## 👤 About Me
**Chandan Singh** – aspiring Data Analyst

- 💼 LinkedIn: [linkedin.com/in/chandansingh](https://linkedin.com/in/chandansingh)
- 📧 Email: chandansingh.data@gmail.com
