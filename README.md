# 🏨 Hotel Analytics & Revenue Optimization Dashboard

A complete end-to-end Power BI analytics solution that transforms raw hotel booking data into actionable business insights for occupancy tracking, revenue optimization, guest segmentation, pricing strategy, and demand forecasting.

This project demonstrates strong capabilities in data modeling, business intelligence, advanced DAX calculations, and predictive analytics using Power BI and Python (Prophet).

---

# 📌 Project Overview

The objective of this project is to design a scalable and performance-optimized hotel analytics ecosystem that enables:

- Accurate Occupancy & Revenue monitoring  
- Guest behavior and loyalty analysis  
- Dynamic pricing optimization  
- Upsell opportunity identification  
- Future booking demand forecasting  

The solution is structured into multiple analytical modules built on a clean star schema data model.

---

# 🧱 Data Modeling – Star Schema Design

## Data Architecture

### Fact Table
`Raw_Fact` – Booking-level transactional dataset containing revenue, ADR, cancellations, guest details, stay duration, and booking channel information.

### Dimension Tables
- `date-dimension`
- `hotel-dimension`
- `room-dimension`
- `Customer-dimension`

## Data Preprocessing & Feature Engineering

- Created surrogate keys: `booking_id`, `hotel_id`, `room_id`, `customer_id`
- Created derived columns:
  - `arrival_date`
  - `date_key`
  - `total_nights`
  - `stay_type`
  - `Revenue`
  - `cost`
  - `discount`
  - `room_category`
- Revenue set to 0 for canceled bookings
- Meal plans mapped to cost values
- Market segments mapped to discount rates
- Room types grouped into logical categories
- Removed high-null columns (e.g., Company)
- Cleaned missing country values
- Built one-to-many relationships in Model View

The star schema ensures optimized performance, correct aggregation, and scalable analytics.

---

# 📊 Occupancy & Revenue Analysis Module

This module evaluates hotel performance using industry-standard KPIs.

## Core Measures

### Total Revenue
```dax
Total Revenue =
SUM(Raw_Fact[Revenue])
```

### Total Bookings
```dax
Total Bookings =
CALCULATE(
    COUNT(Raw_Fact[booking_id]),
    Raw_Fact[is_canceled] = 0
)
```

### ADR (Average Daily Rate)

Column ADR ≠ KPI ADR  
Column ADR = rate per booking  
KPI ADR = weighted average across occupied room nights  

```dax
ADR (Avg Daily Rate) =
DIVIDE(
    [Total Revenue],
    CALCULATE(
        SUM(Raw_Fact[total_nights]),
        Raw_Fact[is_canceled] = 0
    )
)
```

### Occupancy %
```dax
Occupied Room Nights =
CALCULATE(
    SUM(Raw_Fact[total_nights]),
    Raw_Fact[is_canceled] = 0
)

Available Room Nights =
COUNTROWS('room-dimension') *
COUNTROWS('date-dimension')

Occupancy % =
DIVIDE(
    [Occupied Room Nights],
    [Available Room Nights]
)
```

### RevPAR
```dax
RevPAR =
DIVIDE(
    [Total Revenue],
    [Available Room Nights]
)
```

## Business Value
- Identify peak and low demand periods  
- Compare Direct vs OTA performance  
- Monitor pricing efficiency  
- Evaluate cancellation impact  

---

# 👥 Guest Analysis Dashboard

Focuses on guest demographics, booking behavior, and revenue contribution.

## Guest Type Classification
```dax
Guest Type =
SWITCH(TRUE(),
    Raw_Fact[children] > 0 || Raw_Fact[babies] > 0, "Family",
    Raw_Fact[adults] = 1 && Raw_Fact[children] = 0, "Solo",
    Raw_Fact[market_segment] = "Corporate", "Business",
    Raw_Fact[adults] = 2 && Raw_Fact[children] = 0, "Couple",
    "Other"
)
```

## Loyalty Segmentation
```dax
Loyalty Segment =
IF(
    Raw_Fact[is_repeated_guest] = 1 ||
    Raw_Fact[previous_bookings_not_canceled] > 0,
    "Loyal Guest",
    "First-timer"
)
```

## High Spender Identification
```dax
High Spender Status =
VAR RevenueThreshold =
    PERCENTILEX.INC(
        ALL(Raw_Fact),
        Raw_Fact[Revenue],
        0.9
    )
RETURN
IF(
    MAX(Raw_Fact[Revenue]) >= RevenueThreshold,
    "High Spender",
    "Standard"
)
```

## Insights Delivered
- Revenue by country  
- Booking channel distribution  
- Stay duration by guest type  
- VIP customer identification  
- Cancellation behavior patterns  

Enables targeted marketing and loyalty optimization.

---

# 📈 Forecasting & Advanced Analytics

Demand forecasting implemented using Python (Prophet) integrated with Power BI.

## Forecasting Workflow

1. Created Month-End Date column:
```dax
Month_End_Date =
EOMONTH(Raw_Fact[arrival_date], 0)
```

2. Aggregated monthly bookings:
```dax
Monthly_Bookings =
SUMMARIZE(
    Raw_Fact,
    Raw_Fact[Month_End_Date],
    "Total_Bookings",
    COUNT(Raw_Fact[booking_id])
)
```

3. Exported aggregated dataset  
4. Trained Prophet model (24-month forecast horizon)  
5. Reintegrated forecast results into Power BI  

## Outputs
- Actual vs Forecast comparison  
- Confidence intervals  
- Peak demand identification  

## Business Impact
- Capacity planning  
- Staffing optimization  
- Promotional scheduling  
- Long-term pricing strategy  

---

# 💰 Revenue Strategy Dashboard

Focused on dynamic pricing and upsell revenue optimization.

## Estimated Upsell Revenue
```dax
Estimated Upsell Revenue =
VAR MealUpsell = [Meal Upsell Revenue]
VAR SpecialRequestsUpsell =
    SUMX(
        Raw_Fact,
        Raw_Fact[total_of_special_requests] * 15
    )
RETURN
MealUpsell + SpecialRequestsUpsell
```

## Dynamic Pricing Model
```dax
Optimal Price Point =
VAR CurrentOccupancy = [Occupancy %]
VAR CurrentADR = [ADR(Avg Daily Rate)]
VAR PriceAdjustment =
    SWITCH(TRUE(),
        CurrentOccupancy >= 85, 1.15,
        CurrentOccupancy >= 70, 1.05,
        CurrentOccupancy >= 50, 1.00,
        CurrentOccupancy >= 40, 0.95,
        0.85
    )
RETURN
CurrentADR * PriceAdjustment
```

## Strategic Capabilities
- Seasonal pricing recommendations  
- Segment profitability comparison  
- Discount effectiveness tracking  
- Upsell opportunity analysis  
- Year-over-Year revenue monitoring  

---

# 🛠 Technology Stack

- Power BI Desktop  
- Power Query Editor  
- DAX (Data Analysis Expressions)  
- Python  
- Prophet (Time-Series Forecasting Library)  
- CSV Integration  

---

# 🚀 How to Use

1. Open the `.pbix` file in Power BI Desktop  
2. Click Refresh  
3. Navigate via Home Page bookmarks  
4. Apply filters (Hotel, Date, Market Segment, Season, Room Category)  
5. Use drilldowns and slicers for detailed insights  

---

# 🎯 Business Outcomes

This solution enables:

- Data-driven revenue optimization  
- Accurate KPI tracking  
- Advanced guest segmentation  
- Forecast-based strategic planning  
- Improved operational efficiency  

---

⭐ A complete hotel analytics ecosystem integrating data engineering, business intelligence, revenue strategy, and predictive forecasting within Power BI.
