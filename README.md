# Logistics Operations Performance Dashboard

**A four-page Power BI report that shows how a trucking company performs on deliveries, fleet, costs, and safety, and where it loses time and money.**

![Operations Overview](https://github.com/christianah-portfolio/Logistics-Operation-Dashboard/blob/main/dashboard-pages/01_operations_overview.png.png)

> **Note:** This is a portfolio project. It uses a practice dataset and does not represent a real company.

---

## Project Overview

**Brief Description**
A four-page Power BI dashboard analysing a trucking company's deliveries, fleet, costs, and safety, built from a multi-table dataset of about 550,000 records (portfolio project).

**Tools & Skills Used**
Power BI, Power Query, DAX, Data Modelling, Data Cleaning, KPI Design, Data Validation, Data Storytelling.

**Project Goals**
To find where a trucking company loses time and money, and to give recommendations on delivery performance, fuel costs, and safety.

**Results**
Only 44.61% of deliveries were on time, and this was the same across all routes and months, which points to a company-wide issue. Fuel took about 36% of revenue ($0.78 per mile), and 38% of safety incidents were preventable.

---

## Business Problem

A logistics company needs to know whether its trucks are earning money, delivering on time, and operating safely. The data sits in many separate tables (loads, trips, drivers, trucks, fuel, maintenance, deliveries, safety incidents), so it is hard to see the full picture. I combined the data into one model and built dashboards that answer four questions:

1. How is the business doing?
2. How are the trucks and drivers performing?
3. Where does the money go?
4. How safe and on time are the deliveries?

---

## Key Findings

| Finding | Detail |
|---|---|
| Fewer than half of deliveries are on time | **44.61%** on time, measured on delivery events only |
| Lateness is spread evenly | Every one of the 58 routes had 42% to 48% of deliveries on time, and the monthly rate stayed between about 44% and 46%. This points to a company-wide issue, not a few bad routes or drivers |
| Fuel is the biggest cost | **$95.59M**, about **36% of revenue** ($0.78 per mile) |
| Maintenance and claims are small by comparison | $5.73M (about 2% of revenue) and $2.65M (about 1%) |
| Many safety incidents are avoidable | **170 incidents**, **38% preventable**; preventable incidents made up about 34% of claims |
| Revenue is steady | About **$22M a month**, with one dip in February ($20.2M); Dedicated bookings bring in about half of revenue |

---

## Recommendations

1. **Find out why deliveries are late.** The data shows lateness is everywhere, but not the cause. Traffic, weather, and scheduling data would be the next step.
2. **Target safety training** at the incident types that cost the most (equipment damage and DOT violations) and at preventable incidents.
3. **Track fuel cost per mile** as a regular KPI, since fuel is the largest cost by far.

---

## Dashboard Pages

### 1. Logistics Operations Overview
*How is the business doing?*

Revenue, loads, trips, miles, on-time delivery, revenue trend, loads by load type, revenue by booking type, and delivery performance.

![Operations Overview](dashboard-pages/01_operations_overview.png)

### 2. Fleet & Driver Performance
*How are the trucks and drivers performing?*

Truck and driver counts, average MPG, fleet by truck status, maintenance cost by type, revenue by truck brand, and miles by month.

![Fleet and Driver Performance](dashboard-pages/02_fleet_driver_performance.png)

### 3. Revenue, Fuel & Cost Performance
*Where does the money go?*

Revenue against fuel cost, maintenance cost, claims, fuel cost per mile, revenue by origin state and load type, and claims by preventable status.

![Revenue, Fuel and Cost Performance](dashboard-pages/03_revenue_fuel_cost.png)

### 4. Safety & Delivery Performance
*How safe and on time are the deliveries?*

Total deliveries, safety and injury incidents, claims, on-time delivery trend, claims and incidents by type, preventable vs non-preventable incidents, and top states by safety incidents.

![Safety and Delivery Performance](dashboard-pages/04_safety_delivery.png)

---

## Dataset

A multi-table logistics training dataset with **549,706 records across 14 tables**.

| Table | Records | Table | Records |
|---|---|---|---|
| delivery_events | 170,820 | maintenance_records | 2,920 |
| fuel_purchases | 196,442 | customers | 200 |
| loads | 85,410 | trailers | 180 |
| trips | 85,410 | safety_incidents | 170 |
| driver_monthly_metrics | 4,464 | drivers | 150 |
| truck_utilization_metrics | 3,312 | trucks | 120 |
| routes | 58 | facilities | 50 |

Each load has two delivery events (a pickup and a delivery).

---

## Approach

1. **Explored** the 14 tables and used the data dictionary to understand each column.
2. **Cleaned and transformed** the data in Power Query:
   - Trimmed extra spaces and removed non-printing characters from text columns
   - Changed data types (for example dates, numbers, and text)
   - Replaced shortened state codes with full state names, so charts and filters are easier to read
   - Created a custom column for the month, to analyse trends over time
3. **Built a data model** by linking the tables with relationships (for example loads to routes, loads to trips, trips to drivers and trucks).
4. **Wrote DAX measures** for the main KPIs.
5. **Designed four dashboard pages** with slicers so users can filter by customer, location, and other fields.
6. **Validated** every dashboard total against the source tables before finishing.

### Key DAX measures

I wrote about 25 measures. These are the main ones:

```DAX
-- Revenue and volume
Total Revenue = SUM(loads[revenue])
Total Loads   = DISTINCTCOUNT(loads[load_id])
Total Trips   = DISTINCTCOUNT(trips[trip_id])
Total Miles   = SUM(trips[actual_distance_miles])

-- Costs
Total Fuel Cost        = SUM(fuel_purchases[total_cost])
Fuel Cost Per Mile     = DIVIDE([Total Fuel Cost], [Total Miles])
Total Maintenance Cost = SUM(maintenance_records[total_cost])
Total Claims           = SUM(safety_incidents[claim_amount])

-- Deliveries (delivery events only, pickups excluded)
Total Deliveries = CALCULATE(COUNTROWS(delivery_events),
    delivery_events[event_type] = "Delivery")

On-Time Deliveries = CALCULATE(COUNTROWS(delivery_events),
    delivery_events[event_type] = "Delivery",
    delivery_events[on_time_flag] = TRUE())

On-Time Delivery % = DIVIDE([On-Time Deliveries], [Total Deliveries])

-- Safety
Safety Incidents = COUNTROWS(safety_incidents)
Injury Incidents = CALCULATE(COUNTROWS(safety_incidents),
    safety_incidents[injury_flag] = TRUE())

-- Fleet
Total Trucks = DISTINCTCOUNT(trucks[truck_id])
Total Drivers = DISTINCTCOUNT(drivers[driver_id])
Average MPG = AVERAGE(driver_monthly_metrics[average_mpg])
```

---

## Data Validation: Issues Found and Fixed

Checking the dashboards against the source data caught several problems:

| Issue | Fix |
|---|---|
| The on-time chart counted **pickups as well as deliveries**, showing 55.67% | Filtered it to delivery events only, giving 44.61% |
| **Fuel cost per mile** showed $0.80 because the miles total came from a different source | Used total trip miles, giving $0.78 |
| Some charts showed totals far below the true values | Traced and corrected the cause |
| **Revenue by truck brand** leaves out trips with no truck assigned (about 1,700 trips) | Added a footnote to the chart |
| Average detention time mixed pickups and deliveries | Filtered to deliveries and labelled the unit (minutes) |

---

## Limitations

- This is a training dataset, so findings should not be treated as conclusions about a real business.
- The data shows that late deliveries are spread evenly, but it does not contain the causes (traffic, weather, scheduling).
- About 1,700 trips have no driver assigned and about 1,700 have no truck assigned, so driver and truck views do not cover every trip.

---

## Project Files

```
logistics-operations-dashboard/
├── README.md
├── dashboard/
│   └── Logistics_Operations_Dashboard.pbix
├── dashboard-pages/
│   ├── 01_operations_overview.png
│   ├── 02_fleet_driver_performance.png
│   ├── 03_revenue_fuel_cost.png
│   └── 04_safety_delivery.png
└── data/
    └── data_dictionary.md
```
