# 📦 Day 8 — Logistics & Supply Chain Analysis (Power BI)

A Power BI dashboard analyzing shipment-level logistics data to track on-time delivery performance, delivery times, and cost efficiency across regions and cities.

## 🎯 Objective

To analyze delivery performance and costs across regions, identify delays, and provide actionable insights to optimize the supply chain — built as part of a Data Analyst coding challenge.

## 📁 Repository Contents

| File | Description |
|---|---|
| `day-8-logistical-shipment.pbix` | Power BI dashboard file (open with Power BI Desktop) |
| `logistics_data.csv` | Source dataset (300 shipment records) |
| `Day8_Logistics.pdf` | Original challenge brief |
| `README.md` | This file |

## 🗂️ Dataset

`logistics_data.csv` contains shipment-level details:

| Column | Description |
|---|---|
| `ShipmentID` | Unique ID for each shipment |
| `OrderDate` | Date the order was placed |
| `DeliveryDate` | Actual delivery date |
| `ExpectedDate` | Expected delivery date |
| `OriginCity` | City where the shipment originated |
| `DestinationCity` | City where the shipment was delivered |
| `Region` | Delivery region (North, South, East, West) |
| `Distance_km` | Distance between origin and destination |
| `Cost_USD` | Delivery cost in USD |
| `DeliveryStatus` | On-time / Delayed / Cancelled |
| `DeliveryTime_Days` | Time taken to deliver, in days |

## 🛠️ Tools Used

- **Power BI Desktop** — data modeling, DAX measures, visualization
- **DAX** — KPI and measure calculations

## 📊 KPIs

- **Total Shipments**
- **On-Time Delivery %**
- **Avg Delivery Time (days)**
- **Total Delivery Cost**

## 🧮 DAX Measures

```dax
Total Shipments =
COUNTROWS('logistics_data')
```

```dax
On-Time Delivery % =
DIVIDE(
    CALCULATE(
        COUNTROWS('logistics_data'),
        'logistics_data'[DeliveryStatus] = "On-time"
    ),
    CALCULATE(
        COUNTROWS('logistics_data'),
        'logistics_data'[DeliveryStatus] <> "Cancelled"
    ),
    0
)
-- Excludes Cancelled shipments from the denominator since they were
-- never actually delivered and would understate true performance.
```

```dax
Avg Delivery Time (days) =
CALCULATE(
    AVERAGE('logistics_data'[DeliveryTime_Days]),
    'logistics_data'[DeliveryStatus] <> "Cancelled"
)
-- Excludes Cancelled rows: their DeliveryTime_Days value doesn't
-- represent a real completed delivery.
```

```dax
Avg Delivery Time per Region (per days) =
CALCULATE(
    AVERAGE('logistics_data'[DeliveryTime_Days]),
    'logistics_data'[DeliveryStatus] <> "Cancelled"
)
-- Same measure as above; placed on a Region axis in the column chart
-- so Power BI auto-breaks it out per region — no per-region duplicate needed.
```

```dax
Total Delivery Cost =
SUM('logistics_data'[Cost_USD])
```

```dax
Delayed Days =
DATEDIFF('logistics_data'[ExpectedDate], 'logistics_data'[DeliveryDate], DAY)
-- Calculated column used to flag shipments delayed by more than 2 days
-- (via conditional formatting) in the pivot table.
```

## 📈 Visualizations

- **Card visuals** — Total Shipments, On-Time Delivery %, Avg Delivery Time, Total Delivery Cost
- **Column chart** — Avg Delivery Time by Region
- **Treemap** — Delivery Status by Origin City (count of shipments, used in place of a heatmap)
- **Pivot table** — Shipment-level detail with `Delayed Days`, conditionally formatted to highlight shipments delayed by more than 2 days

## 🔍 Key Insights

1. **Only ~49% of shipments arrive on time** (excluding cancellations) — on-time performance has real room to improve.
2. **North region has the slowest average delivery time (~6.0 days)** and the second-lowest on-time rate (~48.5%), making it the weakest-performing region overall.
3. **East region performs best**, with the shortest average delivery time (~5.49 days) and the highest on-time rate (~54.7%).
4. **54 shipments (18%) were delayed by more than 2 days** past their expected date — the group flagged in the pivot table's conditional formatting.
5. **12 shipments were cancelled** across the dataset, concentrated in cities like Hyderabad and Patna (3 and 2 respectively) — worth investigating as a possible origin-hub issue rather than a regional one.

## ▶️ How to Use

1. Clone this repository.
2. Open `day-8-logistical-shipment.pbix` in **Power BI Desktop**.
3. If prompted, point the data source to `logistics_data.csv` in this repo.
4. Explore the dashboard — filter by Region, City, or Delivery Status using the visuals' cross-filtering.

---
*Part of a Data Analyst daily-practice challenge series — Day 8: Logistics & Supply Chain.*
