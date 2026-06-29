# TSA Fleet Performance & Operations Analytics

## Project Overview

This is my capstone project for the **TsAcademy Data Analytics Programme**, built under the guidance of my mentor [**Ezeikel**](https://x.com/ezekiel_aleke). It analyzes a comprehensive trucking and fleet operations dataset to surface actionable insights across revenue, driver performance, fleet health, fuel efficiency, and safety all packaged into a four-page interactive Power BI dashboard.

**The Core Question:** Where is this trucking business leaving money on the table, which operational risks demand immediate attention, and what does the data say leadership should prioritize right now?

The goal was not just to build a dashboard that looks good. The goal was to think like an analyst embedded in a real trucking operation asking uncomfortable questions, connecting numbers to decisions, and translating raw data into business intelligence that actually drives action.

---

## Dashboard Preview

| Page | Description |
|---|---|
| **Executive** | Revenue, profit, customer mix, monthly trends |
| **Drivers** | Individual driver scorecards, leaderboards, OTD rates |
| **Fleet** | Maintenance costs, incident breakdown, utilization |
| **Insights** | Synthesized findings and strategic recommendations |

---

## Dataset

- **Source:** Synthetic trucking operations dataset provided by TsAcademy
- **Scope:** Full fleet and operations data across drivers, trucks, trailers, loads, trips, routes, customers, facilities, fuel purchases, maintenance records, safety incidents, and delivery events
- **Tables:** 14 relational tables
- **Scale:** 85,000+ trips completed across 150 drivers and 120 trucks

**Key Tables:**

| Table | Description |
|---|---|
| `DRIVERS` | Driver profiles, CDL class, experience tier, home terminal |
| `TRUCKS` | Truck make, model, year, status (active/inactive) |
| `TRIPS` | Trip-level data including revenue, mileage, fuel, and delivery status |
| `LOADS` | Load details including base rate, fuel surcharge, accessorial charges |
| `CUSTOMERS` | Customer type (Contract / Spot / Dedicated), account details |
| `SAFETY_INCIDENTS` | Incident records with at-fault, injury, and preventability flags |
| `MAINTENANCE_RECORDS` | Service type, cost, and truck-level maintenance history |
| `FUEL_PURCHASES` | Fuel cost, gallons, and location-level fuel transaction data |
| `DELIVERY_EVENTS` | On-time delivery tracking, detention minutes, facility-level data |
| `SUMMARY_*` | Pre-aggregated tables for driver and fleet-level summaries |

---

## Tools & Technologies

- **Power BI Desktop** — Data modeling, DAX measures, and dashboard visualization
- **DAX** — Calculated measures, KPIs, and filter context logic
- **Power Query (M)** — Data transformation and relationship preparation

---

## Project Workflow

### 1. Data Exploration & Understanding

Before writing a single formula, I mapped out all 14 tables their columns, data types, grain, and how they connected to each other. Understanding that `TRIPS` was the central fact table and that tables like `DRIVERS`, `TRUCKS`, and `CUSTOMERS` were dimensions shaped every modeling decision that followed.

### 2. Data Modeling

- Built a **star schema** with `TRIPS` as the central fact table
- Established relationships between the fact table and all dimension tables using foreign key columns (e.g., `DRIVER_ID`, `TRUCK_ID`, `CUSTOMER_ID`)
- Connected safety and maintenance tables to trucks and drivers for cross-domain analysis
- Handled flag columns (`AT_FAULT_FLAG`, `INJURY_FLAG`, `PREVENTABLE_FLAG`) as text fields requiring conditional DAX logic rather than simple aggregation

### 3. DAX Measures

All measures stored in a dedicated `_Measures` table for clean report organization.


### 4. Dashboard Design

The dashboard uses a **dark navy and gold color theme** consistent across all four pages, with icon-based KPI cards, interactive slicers, and a top navigation bar for seamless page movement.

**Executive Page:**  KPI cards for Total Revenue ($298.62M), Total Profit ($194.66M), Profit Margin (65.2%), On-Time Delivery Rate (55.67%), Total Trips (85K), Fleet Utilization (83.04%), and Total Incidents (168). Supported by revenue vs. potential comparison, revenue by customer type, monthly trend line, and top customer leaderboard.

**Drivers Page:**  Full driver scorecard table with trips completed, incidents, revenue, MPG, driver miles, and OTD rate per driver. Top 10 leaderboards by revenue and trips completed. Filterable by driver name, experience tier, home terminal, and license state.

**Fleet Page:**  Maintenance cost by truck make, fleet maintenance by service type (inspection, repair, preventive), top 10 incident hotspot cities, incident preventability breakdown (preventable vs. non-preventable), and all fleet cost KPIs including fuel surcharge recovery.

**Insights Page:**  Fifteen synthesized findings written in plain business language, organized across revenue gaps, driver performance patterns, fleet cost efficiency, and safety risk with direct, actionable recommendations for leadership.

---

## Key Findings

**Revenue & Commercial:**
- The fleet has capacity to generate **$537.65M** annually but captures only **$298.62M** 44% of revenue potential remains unrealized
- Profit margin of **65.2%** is strong despite the capture gap the business is fundamentally healthy
- **Contract customers** lead revenue at $112.40M vs. Spot ($94.22M) and Dedicated ($92.00M); converting spot accounts to contracts would stabilize growth
- **First Group** at $10.39M is a single-point revenue risk generating $3.12M more than the next closest customer

**Driver Performance:**
- The busiest driver is not the most profitable, William Wilson completes 1,429 trips ($4.9M) while Joseph Jones does 735 trips and earns $2.63M. **Load quality outweighs trip volume**
- No driver exceeds **59% OTD rate** confirming the issue is systemic (scheduling, detention, delivery windows) not individual driver behaviour. Industry standard is above 90%
- Driver MPG is remarkably consistent (6.46–6.55 range) fuel cost differences are driven by route distance and truck age, not driver behaviour

**Fleet & Maintenance:**
- **23% of the fleet** (28 trucks) sits inactive, accumulating insurance and depreciation with zero revenue return
- **Freightliner costs 67% more to maintain** than Kenworth ($1.12M vs. $0.67M) future procurement must factor total cost of ownership
- Fuel surcharges recover only **$29.98M of $95.59M** in actual fuel costs leaving $65.61M absorbed by the company. Surcharge renegotiation is urgent

**Safety:**
- Overall incident rate is low relative to fleet size and trip volume the fleet is generally safe
- **Chicago and Miami** each recorded 11 incidents the highest in the network. Salt Lake City and Seattle follow at 10. These corridors need route-specific risk protocols
- **37.5% of incidents are preventable** (63 of 168) and drivers with more experience were more prone to at-fault incidents, suggesting overconfidence. Safety training must target veterans, not just new hires
- **Charles Hernandez** recorded 6 incidents across 677 trips, the highest rate in the entire scorecard. Requires immediate safety coaching and route review

---

## Challenges & Solutions

**Challenge: Revenue total distorted by blank rows in the TRIPS table**

Blank/null rows in the TRIPS table were pulling the total revenue figure incorrectly. Removing them directly from Power Query would have filtered those rows out of all visuals distorting trip counts and related metrics.

**Solution:** Applied the filter at the visual level using the Power BI filter pane instead of removing rows from the data model. This preserved data integrity across all other visuals while fixing the specific revenue card.

---

**Challenge: Flag columns (AT_FAULT_FLAG, INJURY_FLAG, PREVENTABLE_FLAG) stored as text, not booleans**

Standard `COUNT` or `SUM` wouldn't work for these columns. Needed conditional logic to count only the "Y" values accurately.

**Solution:** Used `COUNTROWS(FILTER(...))` pattern with explicit text conditions e.g., `FILTER(SAFETY_INCIDENTS, SAFETY_INCIDENTS[PREVENTABLE_FLAG] = "Y")` to safely count flagged records regardless of data type.

---

**Challenge: Understanding DAX filter context across related tables**

Revenue calculated at the driver level required understanding how `SUMX` iterates row by row versus how `SUM` aggregates and how `RELATED()` pulls values across table relationships into row context.

**Solution:** Built measures incrementally starting from total measures, then scoped driver-level measures to isolate and verify filter context behavior at each step before combining into more complex expressions.

---

## What I Learned

- **A 14-table relational database demands disciplined modeling.** Every relationship decision cascades through every measure.
- **Filter context is everything in DAX.** The same formula can return completely different results depending on what visual or slicer is active and that's by design.
- **Insights without context are noise.** "168 incidents" means nothing. "168 incidents across 85,000 trips, 63 of which were preventable, concentrated in two cities" is a business finding.
- **The visual-level filter is your friend.** Not every data quality fix belongs in Power Query. Sometimes preserving model integrity means filtering at the report layer.
- **Dashboard design is part of the analysis.** A well-organized, navigable, visually consistent dashboard is not decoration, it determines whether the insights get read or ignored.

---

## How to Use This Project

1. Download [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free from Microsoft)
2. Clone or download this repository
3. Open `TSA_Fleet_Operations_Dashboard.pbix`
4. Use the navigation buttons (Executive → Drivers → Fleet → Insights) to explore each page
5. Apply slicers to filter by driver name, experience tier, home terminal, or license state

---

## Future Improvements

- Add drill-through pages for individual driver and truck profiles
- Implement What-If parameters (e.g., impact of converting 20% of spot customers to contracts)
- Build a route-level profitability page using origin/destination facility data
- Integrate month-over-month and year-over-year time intelligence using a Calendar table
- Add a fuel cost forecasting model using Power BI's built-in analytics

---

## About

Built by **Abdurrahman Odusanya** as the capstone project for the [**TsAcademy Data Analytics Programme**](https://x.com/TechSphereAcad), with mentorship from [**Ezeikel**](https://x.com/ezekiel_aleke).

This project represents the full arc of a real analytics engagement from raw relational data and unclear questions, through careful modeling and formula logic, to a polished dashboard with clear, defensible business recommendations.

Every decision documented here was made deliberately. Every mistake was a learning.

---

*Connect with me on [LinkedIn](https://www.linkedin.com/in/abdurrahmanodusanya/) | [X (Twitter)](https://x.com/WaleAbdurrahman)  | Dataset provided by TsAcademy for educational purposes*
