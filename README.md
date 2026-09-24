# Sapphire Textile Operations & Sales Analytics

A 5-page Power BI dashboard analyzing production, quality, downtime, and revenue performance for a vertically integrated textile manufacturer — modeled on the real operational structure of **Sapphire Fibres Limited** (yarn → fabric → garment production across multiple manufacturing units, exporting to international buyers).

> **Note on the data:** This project uses a synthetic dataset I generated to mirror how a composite textile manufacturer like Sapphire Fibres actually operates — three production units (each producing a different stage: yarn, fabric, or garment), international export customers, daily production logs, and order fulfillment records. It is not real company data. The goal was to demonstrate the kind of analysis I'd bring to an analyst role in this industry, using a dataset shaped around their real business model rather than a generic retail dataset.

---

## Tech Stack

- **Power BI Desktop** — data modeling, DAX measures, report design
- **Power Query (M)** — data cleaning and type transformations
- **DAX** — calculated columns, measures, time intelligence
- **Python (pandas)** — synthetic dataset generation
- **Excel** — source data delivery format

---

## Data Model

A star schema with 4 fact tables and 3 dimension tables:

**Dimensions:** `Dim_Units`, `Dim_Products`, `Dim_Customers`, `Date` (with Year/Quarter/Month/Day hierarchy)

**Facts:** `Fact_Production` (daily output & defects by unit/shift), `Fact_Orders` (customer orders with promised vs. actual delivery), `Fact_Downtime` (machine downtime by reason), `Fact_RawMaterial` (cotton consumption & stock levels)

Key modeling decisions:
- Built a dedicated **Date table** with `CALENDAR()` and marked it as the official date table to support time-intelligence functions like `DATEADD`
- Used **`DIVIDE()`-based measures** instead of calculated columns for every rate/percentage (Defect Rate, On-Time Delivery %) to avoid the common mistake of averaging pre-computed row-level ratios instead of aggregating at the correct grain
- Separated **stage-specific output measures** (Yarn in Kg, Fabric in Meters, Garment in Pieces) rather than blending them into one number, since the three production stages use different units of measure and aren't directly comparable

---

## Dashboard Pages

### 1. Executive Overview
The 10-second summary — total revenue, total output, total defects, on-time delivery %, and defect rate as headline KPIs, with revenue and output trends by month and a stage-split output view.

![Executive Overview](docs/images/Executive%20summary.jpg)

### 2. Revenue & Sales Drivers
Month-over-month revenue change broken down by customer and product, plus a decomposition tree that lets you drill from Stage → Product → Customer → Region to trace exactly where a revenue swing came from.

![Revenue and Sales Drivers](docs/images/Revenue%20and%20Sale%20Drivers.jpg)

### 3. Production & Quality
Output and defect rate trends by month, defect rate by shift and by production stage, and a scatter view of output vs. defect rate by machine line — used to spot whether higher-output lines also run higher defect rates.

![Production and Quality](docs/images/Production%20and%20Quality.jpg)

### 4. Operations & Downtime
Root-cause view of machine downtime — a Pareto-style bar chart of downtime by reason (sorted to surface the top 2–3 causes), downtime by machine line, cotton consumption vs. restocking pattern, and downtime share by manufacturing unit.

![Operations and Downtime](docs/images/operations%20and%20downtime.jpg)

### 5. Key Insights
An auto-summarized performance page pulling together the headline movements across revenue, order volume, downtime, and on-time delivery for the selected month, alongside a production-stage summary table.

![Key Insights](docs/images/key%20insghts.jpg)

---

## Key Analytical Decisions Worth Highlighting

- **Diagnosed a revenue decline using a structured root-cause approach** rather than a single chart: checked whether cancellations were inflating the "before" number, decomposed revenue into order count × average quantity × average price, and cross-referenced the timing against production output and delivery delays before drawing any conclusion.
- **Caught and fixed a unit-mixing error**: an early version of the output KPI summed yarn (Kg), fabric (Meters), and garment (Pieces) into a single blended number — a comparison that looks clean but is analytically meaningless, similar to adding liters of milk to loaves of bread. Fixed by building stage-specific measures and small-multiples visuals so each production stage is shown in its own correct unit.
- **Used `DIVIDE()` measures instead of calculated-column ratios** for every rate metric, to avoid silently averaging row-level percentages instead of aggregating at the right grain — a subtle but common DAX mistake.

---

## What I'd Build Next

- Drill-through page from the Orders table to a single-order detail view
- A proper cumulative-percentage Pareto line on the downtime chart (currently sorted-bar only, for simplicity)
- Year-over-year comparisons once a second year of data is available
