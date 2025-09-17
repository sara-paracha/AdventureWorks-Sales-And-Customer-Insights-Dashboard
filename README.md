# **AdventureWorks Sales Navigator**

## **Overview**

This interactive Power BI dashboard provides a comprehensive view of retail sales performance for AdventureWorks from January 2020 to December 2023. It enables users to explore sales trends, product performance, customer behavior, and regional insights through an intuitive and visually engaging interface.

## **Key Features**

- High-Level Metrics: Track revenue, profit, orders, and return rates at a glance.
- Time Series Analysis: View monthly and quarterly trends in sales, orders, and returns.
- Product Performance: Compare top-selling products and categories, including bikes, accessories, and clothing.
- Customer Insights: Analyze customer segments by income level, occupation, and spending behavior.
- Regional Breakdown: Filter data by geographic regions including Europe, North America, and the Pacific.
- What-If Analysis: Simulate how cost reductions impact total profit over time.
- Interactive Filters: Slice data by time, product, region, and customer attributes.

## **Metrics Included**

- Revenue & Profit
- Order Volume
- Return Rate
- Top Products & Categories
- Customer Demographics
- Regional Performance
- Monthly Trends & Comparisons

## **Target Audience**

This dashboard is designed for:

- Data Analysts
- Sales Managers
- Financial Planners
- Retail Strategists
- Decision Makers in E-commerce and Retail

## **Data sources and scope**

- Timeframe: 2020-01-01 to 2023-12-31.
- Geography: Multiple stores across regions with store-level data.
- Entities:
  - Sales facts including order ID, date, store, product, customer, quantity, and revenue.
  - Product dimension with category, subcategory, brand, size, and color attributes.
  - Store dimension with location, region, store type, and opening date.
  - Date dimension with standard time attributes like year, quarter, month, week, and day.
  - Customer dimension with customer segment, loyalty status, and demographic signals.
- Metrics include revenue, cost of goods sold, gross profit, gross margin, discounts, net sales, units sold, and return rate.
- Data quality: Data is validated for key constraints, such as non-negative quantities, valid dates, and consistent product IDs across tables. Missing values are flagged, and simple imputation strategies are documented in the data quality section.

## **Data model and architecture**

- Approach: Star schema for fast querying and straightforward maintenance.
- Core tables:
  - FactSales: captures line-level transactions and financials.
  - DimDate: provides consistent time attributes for time-based analysis.
  - DimStore: stores metadata for each location.
  - DimProduct: holds product attributes and category hierarchies.
  - DimCustomer: lists customer attributes and segments.
- Relationships:
  - FactSales to DimDate on order_date_id.
  - FactSales to DimStore on store_id.
  - FactSales to DimProduct on product_id.
  - FactSales to DimCustomer on customer_id.
- Data lineage: Every measure is traceable to the underlying fact data. The model supports advanced time intelligence while preserving accuracy across filters and slicers.
- Modularity: The model is designed for incremental refresh and easy extension. Adding new markets, product lines, or additional facts remains straightforward.

## **Key measures and DAX expressions**

- Total Revenue: SUM(FactSales\[Revenue\])
- Total Cost: SUM(FactSales\[COGS\])
- Gross Profit: \[Total Revenue\] - \[Total Cost\]
- Gross Margin: DIVIDE(\[Gross Profit\], \[Total Revenue\], 0)
- Units Sold: SUM(FactSales\[Quantity\])
- Average Order Value (AOV): DIVIDE(\[Total Revenue\], DISTINCTCOUNT(FactSales\[OrderID\]))
- Customer Count: DISTINCTCOUNT(FactSales\[CustomerID\])
- Discount Amount: SUM(FactSales\[DiscountAmount\])
- Net Revenue: \[Total Revenue\] - \[Discount Amount\]
- Revenue Growth YoY: CALCULATE(\[Total Revenue\], SAMEPERIODLASTYEAR(DimDate\[Date\]))
- Moving Averages: AVERAGEX(CALCULATE(DISTINCT(DimDate\[Date\])), \[Revenue\])
- Time-Intelligence helpers:
  - Period-to-date: TOTALYTD(\[Total Revenue\], DimDate\[Date\])
  - Quarter-to-date: TOTALQTD(\[Total Revenue\], DimDate\[Date\])
  - Month-to-date: TOTALMTD(\[Total Revenue\], DimDate\[Date\])
- Forecasting insertions (time-series):
  - Uses built-in forecasting in Power BI visuals with the date axis and simple trend-based projection. Forecast horizon and confidence bands are configurable per visual.

## **Time intelligence and forecasting**

- Time intelligence: The model treats time as a primary axis for analytics. Power BI’s time-intelligence functions drive KPI calculations and trend analyses.
- Forecasting approach: A practical forecast leverages built-in Power BI forecasting on the time axis. It supports a forecast horizon from 2 to 24 periods, with confidence intervals. The forecast assumes a stable trend but can adapt to seasonal patterns where present in the data.
- Seasonality and cycles: The dashboard allows testing for seasonality by month and quarter. You can decompose seasonality visually and compare year-over-year patterns to identify recurring peaks in holiday periods, promotions, and regional events.
- Forecast validation: Visuals include a forecast with confidence bands. Analysts can adjust the horizon and review historical residuals to understand forecasting accuracy.
- Best practices: For credible forecasts, ensure data completeness for the required periods, maintain a stable data schema, and align your date table with the fiscal calendar if used. Avoid overfitting by keeping the horizon reasonable relative to historical data.

## **Visual design and interactivity**

- Visual toolkit:
  - Time-series line charts showing revenue, units, and gross profit across weeks and months.
  - Stacked and clustered bar charts for category performance and store comparisons.
  - Treemaps showing category contributions to revenue and profit.
  - KPI cards for quick health checks like YoY growth, margin, and AOV.
  - Heat maps for store performance by region and month.
  - Slicers for date ranges, store regions, product categories, and customer segments.
  - Drill-through pages for deep dives into product families or top customers.
- Interactivity:
  - Cross-filtering between visuals ensures a cohesive exploration experience.
  - Highlighting and tooltips provide context for each data point.
  - Bookmarks enable storytelling to present a sequence of insights.
- Accessibility:
  - Color palettes are chosen to support color-vision-deficiency accessibility.
  - Descriptive tooltips explain metrics and how to interpret visuals.
- Storytelling flow:
  - Start with global performance, then drill into growth patterns, product category dynamics, and store-level insights.
  - Highlight key drivers of revenue and margins, followed by customer behavior trends.
  - End with actionable recommendations and forecasted outlooks.

## **Data quality, governance, and security**

- Data quality:
  - The pipeline marks missing values in critical fields and logs anomalies.
  - Key constraints ensure non-negative amounts and valid dates.
  - Regular checks catch mismatches between the fact and dimension tables.
- Governance:
  - The model uses role-based access for sensitive fields in the Power BI Service.
  - Data lineage is documented in the docs/ section to aid audit and compliance.
- Security:
  - Use row-level security to restrict data by region or store if needed.
  - Limit sharing of the report to intended audiences and implement workspace governance in Power BI Service.

## **Performance and optimization**

- Model design:
  - A star schema minimizes complex joins and improves performance in large datasets.
  - Measures are optimized with explicit aggregation and avoidance of heavy CALCULATE nests where possible.
- Visual performance:
  - Large tables are summarized in the visuals, with drill-through options for details.
  - Filters and slicers are applied at the report level to limit data loaded into visuals.
- Refresh performance:
  - Incremental refresh can be configured for large datasets.
  - Date table partitioning helps manage historical data efficiently.
- Troubleshooting tips:
  - If visuals lag, check the data model relationships and ensure no circular filters.
  - When measures return blank values, verify the presence of data in the corresponding tables and the correctness of relationships.

## **How to Use**

1. Open the .pbix file in Power BI Desktop.
2. Use the filters on the right to slice data by date, region, product, or customer.
3. Hover over visuals to see detailed tooltips.
4. Use the “What-If” slider to simulate cost reduction scenarios.
5. Drill down into charts for deeper insights (e.g., product-level details).

## **Data Source**

The dashboard is built using retail sales data from AdventureWorks, covering transactions from January 2020 to December 2023.

## **Pages Overview**

- Summary: Key metrics and trend visualizations.
- Geographic View: Sales performance across regions.
- Product Detail: Performance and targets for individual products.
- Customer Analysis: Customer segments and top spenders.
- Return Analysis: Return trends and most returned products.
- Profit Scenario: Interactive profit simulation.
