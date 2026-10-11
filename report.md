# Project Report: Big Data Analytics & Predictive Intelligence (Olist E-Commerce)

## 1. Objective
Build a complete analytics pipeline on e-commerce data: combine multiple tables, analyse sales and customer behaviour, predict late deliveries with machine learning, and present results in an interactive dashboard with business recommendations.

## 2. Data
Brazilian E-Commerce Public Dataset by Olist: about 100k orders placed between 2016 and 2018. Eight tables were used (customers, orders, order items, payments, reviews, products, sellers, category translation). The geolocation table was excluded because the project did not need map-level analysis.

## 3. Architecture
```
Raw CSV files (8 tables)
        |
        v
Python (Pandas): load, merge, clean, feature engineering
        |
        +--> SQL (SQLite): category and monthly revenue queries
        +--> KPI calculation
        +--> Machine learning (Random Forest): late delivery prediction
        |
        v
cleaned_for_powerbi.csv
        |
        v
Power BI dashboard (KPIs, trends, category, payment, state, delivery vs review)
        |
        v
Insights and recommendations (this report)
```

## 4. Methodology
**Merging.** Orders were joined with customers, order items, products, sellers, payments and reviews. Payments and reviews can have several rows per order, so they were aggregated to one row per order before joining. This avoids inflated revenue.

**Cleaning and features.** Date columns were parsed. Missing category names were labelled "unknown". New features: delivery days, estimated delivery days, late delivery flag, purchase month, purchase day of week, and a flag for whether customer and seller are in the same state.

**SQL.** The merged data was loaded into SQLite and queried for the top 10 categories by revenue and for monthly revenue (delivered orders only).

**Machine learning.** A Random Forest classifier predicts whether an order is delivered late. Data was split 80/20 with stratification, and class weights were balanced because late orders are only about 8% of the data.

**Dashboard.** Built in Power BI with five KPI cards, a monthly revenue trend, top categories, payment type split, delivery status vs review score, top states by revenue, and a state slicer. Months with incomplete data (before Jan 2017 and after Aug 2018) were excluded from the trend chart.

## 4a. Dashboard KPIs (order level)
| KPI | Value |
|---|---|
| Total Revenue | 16.01M BRL |
| Total Orders | 99K (99,441) |
| Average Order Value | 160.99 BRL |
| Average Review Score | 4.07 |
| Late Delivery Rate | 8.11% |

## 5. Findings
1. Revenue grew steadily through 2017 and flattened at about 1M BRL per month in 2018.
2. Health & Beauty, Watches & Gifts and Bed Bath Table lead revenue.
3. Customers in Sao Paulo generate the most revenue, far ahead of RJ and MG.
4. Credit card accounts for about 76% of orders.
5. Late deliveries are linked to much lower reviews: about 2.5 for late orders vs above 4 for on-time orders.
6. Only 3.12% of customers purchased more than once, so repeat business is very low.

## 6. Model Results
| Metric | Value |
|---|---|
| Accuracy | about 0.73 |
| ROC-AUC | about 0.73 |
| Recall (late) | about 0.60 |
| Precision (late) | about 0.16 |

The model finds about 60% of late orders but also flags many on-time orders, so it works as a rough early warning, not a precise predictor. The strongest features were estimated delivery days, freight value and price.

## 7. Recommendations
1. Improve logistics for distant states and high-freight orders.
2. Set more realistic delivery estimates.
3. Run retention campaigns, since repeat purchase is very low.
4. Prioritise inventory and marketing for top categories.

## 8. Limitations and Future Work
- The model uses only order and location features. Adding seller handling time, product weight and size, and distance between customer and seller would likely improve it.
- The dataset covers 2016 to 2018 only, so trends may not reflect current behaviour.
- Customer segmentation (for example RFM analysis) can be added to study retention in more depth.