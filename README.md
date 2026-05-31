# 🚚 SwiftRoute Logistics Dashboard

> **End-to-end Power BI dashboard** analyzing 27,979 orders across 6 hubs, 55 drivers and 45 vehicles — with time intelligence, MoM KPIs, and driver profiling.


---

## 📋 Problem Statement

SwiftRoute is a logistics company operating across 6 Texas hubs (Dallas, Houston, Austin, San Antonio, Fort Worth, El Paso). The operations team lacked a centralized view to:

- Monitor **on-time delivery performance** and customer satisfaction in real time
- Identify **underperforming drivers** and hubs before issues escalate
- Track **vehicle reliability** and maintenance needs across a 45-vehicle fleet
- Compare current month performance against **previous month benchmarks**

The goal was to build a single interactive dashboard that answers these questions for any combination of year and month — without touching the raw data.

---

## 🗂️ Dataset

| File | Rows | Description |
|------|------|-------------|
| `Orders.csv` | 27,979 | All delivery orders 2023–2024 with status, delay reason, CSAT score, delivery time |
| `Drivers.csv` | 55 | Driver profiles with experience, hire date, performance rating |
| `Hubs.csv` | 6 | Hub names and processing capacities |
| `Vehicles.csv` | 45 | Vehicle codes, models, status (Active/Maintenance), breakdown count |

**Source:** Synthetic logistics dataset — designed for end-to-end BI portfolio projects.

---

## 🏗️ Data Model — Star Schema

```
                    DIM_Date
                       │
DIM_Drivers ──── FACT_Orders ──── DIM_Hubs
                       │
                  DIM_Vehicles
```

| Table | Type | Key Column | Rows |
|-------|------|-----------|------|
| `FACT_Orders` | Fact | Order ID | 27,979 |
| `DIM_Date` | Dimension | Date | 730 (2023–2024) |
| `DIM_Drivers` | Dimension | Driver ID | 55 |
| `DIM_Hubs` | Dimension | Hub Name | 6 |
| `DIM_Vehicles` | Dimension | Vehicle Code | 45 |

All relationships are **Many-to-One**, cross-filter **Single direction**.

---

## 📐 DAX Measures — Highlights

### Core KPIs
```dax
On Time Delivery Rate % =
DIVIDE(
    CALCULATE(COUNTROWS(FACT_Orders), FACT_Orders[Is On Time] = TRUE()),
    [Total Orders], 0
)

CSAT % =
DIVIDE(
    CALCULATE(COUNTROWS(FACT_Orders), FACT_Orders[Customer Satisfaction Score] >= 4),
    [Total Orders], 0
)
```

### Month-over-Month Intelligence
```dax
Total Orders MoM % =
VAR _curr = [Total Orders]
VAR _prev = CALCULATE([Total Orders], DATEADD(DIM_Date[Date], -1, MONTH))
RETURN IF(ISBLANK(_prev) || _prev = 0, BLANK(), DIVIDE(_curr - _prev, _prev, 0))
```

### Dynamic Driver Title
```dax
Drivers Title =
VAR _name   = SELECTEDVALUE(Drivers[DriverName], "All Drivers")
VAR _orders = [Total Orders]
VAR _month  = SELECTEDVALUE('Date'[MonthName_EN], "")
VAR _year   = SELECTEDVALUE('Date'[Year], "")
RETURN _name & " made " & _orders & " deliveries in " & _month & " " & _year
```

> 📄 Full list of 30 DAX measures available in DaxMesure list

---

## 📊 Dashboard Pages

### Page 1 — Overview
Main summary page with 4 KPI cards (Total Orders, On Time Rate, CSAT %, Avg Delivery Time) each showing current value, previous month, and MoM % change. Three summary panels for Hubs, Drivers, and Vehicles feed into the detail pages.



### Page 2 — Driver Overview
Individual driver profiling with a dynamic card showing name, hire date, years of experience, and star rating for the selected driver. Includes a scatter plot (experience vs. performance rating), top 10 delayed drivers bar chart, and monthly order trend line chart.

### Page 3 — Hubs Overview
Hub capacity vs. orders processed (clustered bar), hub performance ranking by on-time rate, and a day-of-week matrix showing average processing hours per hub — useful for identifying bottlenecks on specific days.



### Page 4 — Vehicles Overview
Fleet health at a glance: Active vs. Maintenance donut, vehicle age vs. breakdown scatter plot, breakdown by vehicle code and model, orders by vehicle type distribution.


---

## 💡 Key Insights

| # | Insight | Impact |
|---|---------|--------|
| 1 | **On-time delivery rate dropped 0.6% MoM** (78.6% vs 79.1%) despite total orders growing 0.3% | Capacity pressure on hubs |
| 2 | **Austin Hub has the lowest on-time rate at 72.4%** — 10 points below San Antonio (82.9%) | Operational review needed |
| 3 | **Top 3 delayed drivers** (Rodriguez 45.5%, Williams 43.8%, Brown 42.9%) — all above 40% delay rate | Targeted coaching opportunity |
| 4 | **FT-010 (Freightliner M2) has 28 breakdowns** — highest in the fleet | Priority maintenance flag |
| 5 | **CSAT dropped 1.2% MoM** (82.6% vs 83.6%) while avg delivery time increased 4.5% | Delivery time directly impacts satisfaction |
| 6 | **73.3% of vehicles are Active** — 12 in maintenance simultaneously may explain delivery delays | Fleet rotation strategy needed |

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| **Power BI Desktop** | Data modeling, DAX, visualizations |
| **Power Query (M)** | Data cleaning, type corrections, custom columns |
| **DAX** | 30 measures including time intelligence, MoM %, SELECTEDVALUE profiling |
| **Power BI Service** | Cloud publishing, public link generation |
| **GitHub** | Version control, portfolio presentation |

---

## 🎨 Design

- **Theme:** Custom Deep Ocean dark theme (JSON file included → [`/theme/SwiftRoute_DeepOcean_Theme.json`](./theme/SwiftRoute_DeepOcean_Theme.json))
- **Color palette:** `#0A1628` background · `#2E6FD8` primary · `#00E5C3` accent · `#5BBFFF` secondary
- **Font:** Segoe UI throughout
- **Max 5 visuals per page** — clean, uncluttered layout
- **Sidebar navigation** with page buttons for seamless UX

---

## 📁 Repository Structure

```
swiftroute-logistics-dashboard/
│
├── README.md
├── SwiftRoute_Dashboard.pbix          ← Power BI file
│
├── data/
│   ├── Orders.csv
│   ├── Drivers.csv
│   ├── Hubs.csv
│   └── Vehicles.csv
│
├── dax/
│   └── measures.md                    ← All 30 DAX measures documented
│
├── theme/
│   └── SwiftRoute_DeepOcean_Theme.json
│
└── screenshots/
    ├── overview.png
    ├── drivers.png
    ├── hubs.png
    └── vehicles.png
```

---

## 🚀 How to Use

1. **Clone the repo** or download the `.pbix` file
2. Open `SwiftRoute_Dashboard.pbix` in **Power BI Desktop** (free)
3. If data doesn't load: `Transform Data → Data source settings` → update the path to the `/data` folder
4. To apply the theme: `View → Themes → Browse themes` → select `SwiftRoute_DeepOcean_Theme.json`
5. Publish to your own **Power BI Service** workspace for a shareable link

---

## 👤 Author

[Mouhamma Aboubakar]
Data Analyst · Power BI Developer

![LinkedIn]www.linkedin.com/in/mouhammad-aboubakar-b18128312
ss

---

*Built as a portfolio project to demonstrate end-to-end Power BI skills: data modeling, DAX, time intelligence, UX design, and cloud publishing.*
