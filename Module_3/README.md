# Hotel Guest Analysis Dashboard

## Project Overview
This project is a **Power BI Dashboard** developed to analyze guest demographics, booking behaviors, and revenue patterns using the `Raw_Fact` dataset. The module provides a deep dive into guest types, loyalty, and spending habits to help hotel management make data-driven decisions.

## Key Features
* **Guest Analysis Module**: Analyzes guest types including Business, Family, and Solo travelers.
* **Interactive Visuals**: Includes geographic distribution by nationality, booking source analysis, and stay duration.
* **Customer Clustering**: Groups customers into First-timers, Loyal guests, and High spenders.
* **Performance KPIs**: Tracks Total Bookings, Average Daily Rate (ADR), Total Revenue, and Cancellation Rates.

## Data Source
The analysis is built on the `Raw_Fact` table, utilizing key columns such as:
* **Demographics**: `country`, `adults`, `children`, `babies`.
* **Behavior**: `is_canceled`, `is_repeated_guest`, `previous_bookings_not_canceled`, `total_of_special_requests`.
* **Financials**: `Revenue`, `adr`, `discount`.
* **Logistics**: `total_nights`, `arrival_date`, `distribution_channel`, `market_segment`.

## DAX Logic Implemented

### 1. Guest Type Classification
Categorizes bookings based on party composition and market segments.

```dax
Guest Type = 
SWITCH( TRUE(),
    'Raw_Fact'[children] > 0 || 'Raw_Fact'[babies] > 0, "Family",
    'Raw_Fact'[adults] = 1 && 'Raw_Fact'[children] = 0, "Solo",
    'Raw_Fact'[market_segment] = "Corporate", "Business",
    'Raw_Fact'[adults] = 2 && 'Raw_Fact'[children] = 0, "Couple",
    "Other"
)
```

### 2. Loyalty Clustering
Distinguishes between new customers and returning loyal guests.

```dax
Loyalty Segment = 
IF(
    'Raw_Fact'[is_repeated_guest] = 1 || 'Raw_Fact'[previous_bookings_not_canceled] > 0, 
    "Loyal Guest", 
    "First-timer"
)
```

### 3. High Spender Identification
A dynamic measure to flag the top 10% of guests by revenue contribution.

```dax
High Spender Status = 
VAR RevenueThreshold = PERCENTILEX.INC(ALL('Raw_Fact'), 'Raw_Fact'[Revenue], 0.9)
RETURN
IF(MAX('Raw_Fact'[Revenue]) >= RevenueThreshold, "High Spender", "Standard")
```

## Dashboard Layout

- **Top KPI Row**: Displays Total Bookings (575), ADR (120.47), Total Revenue ($271K), and Cancel Rate (0.35).
- **Left Pane**: World map showing Revenue by country and a Donut Chart for `distribution_channel`.
- **Bottom Pane**: Analysis of `total_nights` by Guest Type and a VIP Customer table.
- **Right Filter Panel**: Slicers for Month, Market Segment, Hotel Type, Deposit Type, and Guest Type.

## Author
**Somalanka Pavan Kumar**  
B.Tech Computer Science and Engineering  
SRM University AP