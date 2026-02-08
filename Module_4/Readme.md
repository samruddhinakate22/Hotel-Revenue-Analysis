##Hotel Revenue Analysis – Milestone 3
 Project Overview

This project analyzes hotel booking, occupancy, and revenue patterns using Power BI.
Milestone 3 focuses on forecasting future bookings, understanding cancellation behavior, and enabling data-driven business decisions through interactive dashboards.

### Home Page

The Home Page acts as a central navigation hub for the entire report.

Key Features

Interactive menu using Bookmarks

Navigation to all major sections:

Info & Discount Deals

Occupancy & Revenue Analysis

Guest Analysis

Forecast

About Us

Clean, executive-friendly layout with hotel branding

 Home Page Preview

![Home Page](Screenshot/Home Page.png)

###Info & Discount Deals Page

This page highlights promotional offers based on guest type, improving targeted marketing strategies.

Key Features

Guest-specific offers:

Business

Couple

Family

Solo

Image switching using Bookmarks + Selection Pane

Discounts dynamically displayed based on user selection

Info & Discount Deals Preview

![Info & Discount Page](Screenshot/Info & Discount page.png)

###About Us Page

The About Us page provides a business overview of the hotel and its data-driven vision.

About the Hotel

The hotel delivers comfortable stays and premium service for both business and leisure travelers.
By leveraging data-driven insights, the hotel aims to optimize operations, enhance guest experience, and achieve sustainable revenue growth.

Additional Insights

Global revenue distribution by country

Built-in Q&A visual for quick executive queries

Feedback interaction button

About Us Page Preview

![About Us Page](Screenshot/About us page.png)

### Forecasting Methodology

To forecast future hotel bookings, a structured time-series forecasting approach was implemented using Power BI, DAX, and Python (Prophet). The methodology followed these steps:

1  Creation of Month-End Date Column

A new calculated column was created to standardize all bookings at a monthly granularity, which is required for time-series forecasting.

Month_End_Date = EOMONTH ( Raw_Fact[arrival_date], 0 )


This ensures all bookings are aligned to the end of each month, improving consistency and trend detection.

2 Monthly Aggregation of Bookings

A new summarized table was created to aggregate total bookings per month.

Monthly_Bookings =
SUMMARIZE (
    Raw_Fact,
    Raw_Fact[Month_End_Date],
    "Total_Bookings",
    COUNT ( Raw_Fact[booking_id] )
)


This table serves as the forecast input dataset, containing:

Month_End_Date → Time dimension

Total_Bookings → Target variable

3 Export of Aggregated Data

The Monthly_Bookings table was exported from Power BI as a CSV file to enable advanced forecasting using Python.

This step separates data preparation (Power BI) from modeling (Python) for better control and transparency.

4 Forecasting Using Prophet (Python)

The exported dataset was loaded into Power BI using a Python script, and the Prophet forecasting model was applied.

Key steps included:

Renaming columns to Prophet format (ds, y)

Data type validation

Removal of null values

Forecasting 24 future months (2 years)

import pandas as pd
from prophet import Prophet

df = dataset.copy()

df = df.rename(columns={
    'Month_End_Date': 'ds',
    'Sum of Total_Bookings': 'y'
})

df['ds'] = pd.to_datetime(df['ds'])
df['y'] = pd.to_numeric(df['y'])
df = df.dropna()

model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False
)

model.fit(df)

future = model.make_future_dataframe(
    periods=24,
    freq='ME'
)

forecast = model.predict(future)

result = forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']]

5 Integration Back into Power BI

The forecast output (yhat, yhat_lower, yhat_upper) was loaded back into Power BI and used to create:

Forecast KPIs

Actual vs Forecast trend analysis

Confidence interval visuals

Peak demand identification

✔ Outcome

This forecasting approach enables:

Accurate prediction of future booking demand

Identification of peak booking periods

Improved planning for pricing, staffing, and promotions

Data-driven revenue optimization decisions
