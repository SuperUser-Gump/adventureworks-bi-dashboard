# Power BI Dashboard for AdventureWorks

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources and Transformations](#data-sources-and-transformations)
  - [Data Source Description](#data-source-description)
  - [Data Transformations](#data-transformations)
- [Data Model and Measures](#data-model-and-measures)
  - [Data Model](#data-model)
  - [Measures](#measures)
- [Dashboard Functionalities](#dashboard-functionalities)
- [Getting Started](#getting-started)

<a id="project-overview"></a>

## Project Overview

This Power BI dashboard empowers AdventureWorks, a global cycling equipment manufacturer, with data-driven insights for informed decision-making. It equips the management team to analyze key performance indicators (KPIs), identify regional sales variations, understand product performance trends, and segment high-value customers.

**Target Audience:** AdventureWorks Management Team

**Data Sources:** Raw CSV files on transactions, returns, products, customers, and sales territories.

**Key Functionalities:**

- **KPI Tracking:** Monitor overall sales health with metrics like total revenue, profit, orders, and return rate.
- **Regional Sales Analysis:** Visualize sales performance across regions and pinpoint high performers.
- **Product-Level Insights:** Explore trends, identify best-sellers, analyze individual product performance, and assess impact of price adjustments on profit.
- **High-Value Customer Segmentation:** Understand customer base through total count, average revenue per customer, and identify high-value segments based on demographics. Easily pinpoint top customers and gain insights into their buying behavior.

**Organized for Clarity:**

The dashboard leverages four dedicated pages to present information effectively:

- **Executive Dashboard:** Provides a high-level overview of KPIs for quick decision-making.

  ![Executive Dashboard](/images/dashboard_images/01%20Exec%20Dashboard.png)

- **Regional Sales Map:** Visualizes sales performance across different regions for geographically-oriented insights.

  ![Regional Sales Map](/images/dashboard_images/02%20Map.png)

- **Product Details:** Offers in-depth analysis of product trends and performance.

  ![Product Details](/images/dashboard_images/03%20Product%20Detail.png)

- **Customer Details:** Identifies and helps understand high-value customer segments for targeted marketing strategies.

  ![Customer Details](/images/dashboard_images/04%20Customer%20Detail.png)

**Development Tools:** Power BI Desktop Version: 2.126.1261.0 64-bit (February 2024)

**Live Demo:**

Due to budget limitations, the live demo is currently unavailable. However, I'm actively working towards securing the necessary Power BI Pro license to host it. This repository will be updated with the live demo link as soon as it becomes available.

**Disclaimer:**

This project utilizes Power BI Desktop and adheres to the design and structure of the [Microsoft Power BI Desktop for Business Intelligence](https://www.udemy.com/course/microsoft-power-bi-up-running-with-power-bi-desktop) course from [Maven Analytics](https://mavenanalytics.io/). The primary objective is to apply and solidify the concepts learned, not the development of an original dashboard design.

<a id="data-sources-and-transformations"></a>

## Data Sources and Transformations

<a id="data-source-description"></a>

### Data Source Description

This section provides a detailed description of the tables included in the [AdventureWorks sample dataset](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms). These tables facilitate data exploration and analysis using Power BI.

**Tables Included in the Dataset:**

- **Calendar Lookup:** Contains a set of dates. Include:

  - `Date`: Date in DD-MM-YY format.

- **Customer Lookup:** Stores customer information, including demographics and contact details. Include:

  - `CustomerKey`: Unique identifier for a customer.
  - `Prefix`: Title prefix (e.g., Mr., Ms.).
  - `FirstName`: First name.
  - `LastName`: Last name.
  - `BirthDate`: Date of birth.
  - `MaritalStatus`: Marital status.
  - `Gender`: Gender.
  - `EmailAddress`: Email address.
  - `AnnualIncome`: Annual income.
  - `TotalChildren`: Total number of children.
  - `EducationLevel`: Highest level of education.
  - `Occupation`: Occupation.
  - `HomeOwner`: Indicates home ownership.

- **Product Categories Lookup:** Defines product categories within the system. Include:

  - `ProductCategoryKey`: Unique identifier for a product category.
  - `CategoryName`: Name of the category.

- **Product Lookup:** Provides detailed information about products. Include:

  - `ProductKey`: Unique identifier for a product.
  - `ProductSubcategoryKey`: Foreign key to Product Subcategory.
  - `ProductSKU`: Stock Keeping Unit.
  - `ProductName`: General name.
  - `ModelName`: Specific model name.
  - `ProductDescription`: Detailed description.
  - `ProductColor`: Color.
  - `ProductSize`: Size.
  - `ProductStyle`: Style.
  - `ProductCost`: Cost to the company.
  - `ProductPrice`: Selling price.

- **Product Subcategories Lookup:** Establishes relationships between products and subcategories. Include:

  - `ProductSubcategoryKey`: Unique identifier for a subcategory.
  - `SubcategoryName`: Name of the subcategory.
  - `ProductCategoryKey`: Foreign key to Product Category.

- **Returns Data:** Tracks product returns. Include:

  - `ReturnDate`: Date of return.
  - `TerritoryKey`: Foreign key to Territory.
  - `ProductKey`: Foreign key to Product.
  - `ReturnQuantity`: Quantity returned.

- **Sales Data:** Stores sales order information. Include:

  - `OrderDate`: Date of order placement.
  - `StockDate`: Date of shipment from inventory.
  - `OrderNumber`: Unique identifier for the order.
  - `ProductKey`: Foreign key to Product.
  - `CustomerKey`: Foreign key to Customer.
  - `TerritoryKey`: Foreign key to Territory.
  - `OrderLineItem`: Line item number.
  - `OrderQuantity`: Quantity sold per line item.

<a id="data-transformations"></a>

### Data Transformations

This section details transformations applied to various data sources to prepare them for analysis in Power BI. These transformations focus on:

- **Extracting new insights:** Deriving additional information like day of week from dates or category from product SKUs.
- **Data cleaning:** Removing invalid data, correcting inconsistencies, and standardizing formats.
- **Enriching data:** Creating calculated columns to combine data points or categorize information.

**Transformations by Table**

- **Calendar Lookup:**

  - Extracted day of week, week/month/quarter start dates, month name/number, and year information from the `Date` column.
  - Calculated columns:

    - `Month Short`: First 3 characters of `Month Name` in uppercase.

      ```
      Month Short =
      UPPER(
          LEFT(
          'Calendar Lookup'[Month Name],
          3
          )
      )
      ```

    - `Weekend`: Categorizes days based on `Day of Week` (weekends are Saturday & Sunday).

      ```
      Weekend =
      IF(
          'Calendar Lookup'[Day of Week] IN {6,7},
          "Weekend",
          "Weekday"
      )
      ```

- **Customer Lookup:**

  - Cleaned `CustomerKey` by removing invalid data.
  - Standardized name formatting (`Prefix`, `FirstName`, `LastName`) to title case.
  - Derived columns:
    - `Full Name`: Merged `FirstName` and `LastName`.
    - `Email Domain`: Extracted from `EmailAddress`.
  - Calculated columns:

    - `Is Parent?`: Identifies customers with children using `TotalChildren` (Yes/No).

      ```
      Is Parent ? =
      IF(
          'Customer Lookup'[TotalChildren] > 0,
          "Yes",
          "No"
      )
      ```

    - `Customer Priority`: High-value customers (parents with income > $100,000) are assigned "Priority".

      ```
      Customer Priority =
      IF(
          'Customer Lookup'[Is Parent ?]="Yes" &&
          'Customer Lookup'[AnnualIncome] > 100000,
          "Priority",
          "Standard"
      )
      ```

    - `Income Level`: Segments customers based on annual income (Very High, High, Average, Low).

      ```
      Income Level =
      IF('Customer Lookup'[AnnualIncome] >= 150000, "Very High",
      IF('Customer Lookup'[AnnualIncome] >= 100000, "High",
      IF('Customer Lookup'[AnnualIncome] >= 50000, "Average",
      "Low")))
      ```

    - `Education Category`: Segments customers based on education level (High School, Undergrad, Graduate).

      ```
      Education Category =
      SWITCH(
          TRUE(),
          'Customer Lookup'[EducationLevel] = "High School" ||
          'Customer Lookup'[EducationLevel] = "Partial High School", "High School",
          'Customer Lookup'[EducationLevel] = "Bachelors" ||
          'Customer Lookup'[EducationLevel] =  "Partial College", "Undergrad",
          'Customer Lookup'[EducationLevel] =  "Graduate Degree", "Graduate"
      )
      ```

- **Product Lookup:**

  - Data cleaning:
    - Removed unnecessary `ProductSize` column.
    - Replaced invalid `ProductStyle` values (0) with "NA".
  - Converted `ProductCost` and `ProductPrice` to Fixed Decimal for better precision.
  - Derived columns:
    - `SKU Type`: Extracted from existing product data.
    - `Discount Price` Calculated by multiplying the 'ProductPrice' by 0.9 (90% discount).
  - Calculated columns:

    - `Price Point`: Segments products based on price (High, Mid-Range, Low).

      ```
      Price Point =
      SWITCH(
          TRUE(),
          'Product Lookup'[ProductPrice] > 500, "High",
          'Product Lookup'[ProductPrice] > 100, "Mid-Range",
          "Low"
      )
      ```

    - `SKU Category`: Extracts category from product SKUs.

      ```
      SKU Category =
      LEFT(
          'Product Lookup'[ProductSKU],
          SEARCH(
              "-",
              'Product Lookup'[ProductSKU]
          )
          - 1
      )
      ```

<a id="data-model-and-measures"></a>

## Data Model and Measures

<a id="data-model"></a>

### Data Model

This section describes the data model for the AdventureWorks dataset, focusing on tables and their relationships. The model utilizes a snowflake schema due to its multiple dimension tables with normalized attributes.

**Tables Included in the Data Model:**

- **Fact Tables:**
  - Sales
  - Returns
- **Dimension Tables:**
  - Customer Lookup
  - Territory Lookup
  - Calendar Lookup
  - Product Lookup
  - Product Subcategories Lookup
  - Product Categories Lookup

**Relationships:**

Foreign key relationships connect the tables, enabling efficient data retrieval and analysis. Key relationships include:

- **Sales & Returns to Customer & Territory:**
  - `Sales.CustomerKey` links to `Customer Lookup.CustomerKey` (customer for each sale).
  - `Sales.TerritoryKey` links to `Territory Lookup.SalesTerritoryKey` (territory for each sale).
  - `Returns.TerritoryKey` links to `Territory Lookup.SalesTerritoryKey` (territory where product was sold).
- **Sales & Returns to Time & Products:**
  - `Sales.OrderDate` links to `Calendar Lookup.Date` (time-based sales analysis).
  - `Returns.ReturnDate` links to `Calendar Lookup.Date` (time-based return analysis).
  - `Sales.ProductKey` links to `Product Lookup.ProductKey` (products sold).
  - `Returns.ProductKey` links to `Product Lookup.ProductKey` (products returned).
- **Product Dimension Hierarchy:**
  - `Product Lookup.ProductSubcategoryKey` links to `Product Subcategories Lookup.ProductSubCategoryKey` (subcategories for products).
  - `Product Subcategories Lookup.ProductCategoryKey` links to `Product Categories Lookup.ProductCategoryKey` (product category hierarchy).

![Data Model](/images/Data%20Model%20Structure.drawio.png)

<a id="measures"></a>

### Measures

This section outlines the key metrics used to evaluate performance throughout the project.

- **Quantity Sold:** Calculates the total number of units sold across all orders.

  ```
  Quantity Sold =
  SUM(
    'Sales Data'[OrderQuantity]
  )
  ```

- **Quantity Returned:** Calculates the total number of units returned across all return transactions.

  ```
  Quantity Returned =
  SUM(
    'Returns Data'[ReturnQuantity]
  )
  ```

- **Average Retail Price:** Calculates the average price at which products are sold.

  ```
  Average Retail Price =
  AVERAGE(
    'Product Lookup'[ProductPrice]
  )
  ```

- **Total Returns:** Calculates the total number of separate return transactions initiated by customers.

  ```
  Total Returns =
  COUNT(
    'Returns Data'[ReturnQuantity]
  )
  ```

- **Total Orders:** Calculates the total number of unique orders placed by customers.

  ```
  Total Orders =
  DISTINCTCOUNT(
    'Sales Data'[OrderNumber]
  )
  ```

- **Total Customers:** Calculates the total number of distinct customers who have placed at least one order.

  ```
  Total Customers = DISTINCTCOUNT('Sales Data'[CustomerKey])
  ```

- **Return Rate:** Calculates the percentage of units returned relative to the total number sold. If no sales occurred, the measure displays "No Sales" to prevent division by zero errors.

  ```
  Return Rate =
  DIVIDE(
    [Quantity Returned],
    [Quantity Sold],
    "No Sales"
  )
  ```

- **Bulk Orders:** Calculates the number of orders classified as "bulk" based on a minimum order quantity threshold. In this context, any order exceeding one unit is considered bulk.

  ```
  Bulk Orders =
  CALCULATE(
    [Total Orders],
    'Sales Data'[OrderQuantity] > 1
  )
  ```

- **Weekend Orders:** Calculates the total number of orders placed on Saturdays and Sundays.

  ```
  Weekend Orders =
  CALCULATE(
    [Total Orders],
    'Calendar Lookup'[Weekend] = "Weekend"
  )
  ```

- **Bike Returns:** Calculates the total number of returns specifically for bicycles.

  ```
  Bike Returns =
  CALCULATE(
    [Total Returns],
    'Product Categories Lookup'[CategoryName] = "Bikes"
  )
  ```

- **Bike Sales:** Calculates the total number of bicycles sold.

  ```
  Bike Sales =
  CALCULATE(
    [Quantity Sold],
    'Product Categories Lookup'[CategoryName] = "Bikes"
  )
  ```

- **Bike Return Rate:** Calculates the return rate specifically for bicycles.

  ```
  Bike Return Rate =
  CALCULATE(
    [Return Rate],
    'Product Categories Lookup'[CategoryName] = "Bikes"
  )
  ```

- **All Orders:** Calculates the total number of orders across the entire dataset, regardless of any filters applied elsewhere in the report. It provides a grand total of all orders.

  ```
  All Orders =
  CALCULATE(
    [Total Orders],
    All(
      'Sales Data'
    )
  )
  ```

- **% of All Orders:** Calculates the percentage contribution of each order amount to the total orders.

  ```
  % of All Orders =
  DIVIDE(
    [Total Orders],
    [All Orders]
  )
  ```

- **Overall Average Price:** Calculates the average retail price across the entire dataset, regardless of any filters applied elsewhere in the report. It provides a grand total average price.

  ```
  Overall Average Price =
  CALCULATE(
    [Average Retail Price],
    ALL(
      'Product Lookup'
    )
  )
  ```

- **All Returns:** Calculates the total number of returns across the entire dataset, regardless of any filters applied elsewhere in the report. It provides a grand total of all returns.

  ```
  All Returns =
  CALCULATE(
    [Total Returns],
    ALL(
      'Returns Data'
    )
  )
  ```

- **% of All Returns:** Calculates the percentage contribution of each return's quantity to the total quantity of returns.

  ```
  % of All Returns =
  DIVIDE(
    [Total Returns],
    [All Returns]
  )
  ```

- **High Ticket Orders:** Calculates the total number of orders containing high-priced products. An order is considered "high ticket" if it includes at least one product with a price exceeding the overall average price of all products.

  ```
  High Ticket Orders =
  CALCULATE(
    [Total Orders],
    FILTER(
      'Product Lookup',
      'Product Lookup'[ProductPrice] > [Overall Average Price]
    )
  )
  ```

- **Total Revenue:** Calculates the total revenue generated from all sales. It considers both the quantity of products sold and their corresponding prices.

  ```
  Total Revenue =
  SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    RELATED(
      'Product Lookup'[ProductPrice]
    )
  )
  ```

- **Average Revenue per Customer (ARPC):** Calculates the average revenue generated per customer.

  ```
  Average Revenue per Customer =
  DIVIDE(
    [Total Revenue],
    [Total Customers]
  )
  ```

- **Total Cost (COGS):** Calculates the total cost of goods sold (COGS) incurred across all sales. It considers the quantity of products sold and their corresponding purchase costs.

  ```
  Total Cost =
  SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    RELATED(
      'Product Lookup'[ProductCost]
    )
  )
  ```

- **Total Profit:** Calculates the total profit generated from all sales. It takes into account both the revenue earned and the cost of goods sold (COGS).

  ```
  Total Profit = [Total Revenue] - [Total Cost]
  ```

- **YTD Revenue:** Calculates the total revenue generated within the current year, up to today's date. It provides a year-to-date (YTD) snapshot of the sales performance.

  ```
  YTD Revenue =
    CALCULATE(
      [Total Revenue],
      DATESYTD(
      'Calendar Lookup'[Date]
    )
  )
  ```

- **Previous Month Revenue:** Calculates the total revenue generated in the previous month relative to the current date context in the report. It provides a quick comparison of the sales performance between the current and previous month.

  ```
  Previous Month Revenue =
  CALCULATE(
    [Total Revenue],
    DATEADD(
      'Calendar Lookup'[Date],
      -1,
      MONTH
    )
  )
  ```

- **Revenue Target:** Sets a revenue target for the upcoming month based on a 10% increase over the previous month's revenue.

  ```
  Revenue Target = [Previous Month Revenue] * 1.1
  ```

- **10-day Rolling Revenue:** Calculates the rolling sum of total revenue for the past 10 days, excluding today. It provides a dynamic view of the revenue trend over the most recent 10-day period.

  ```
  10-day Rolling Revenue =
  CALCULATE(
      [Total Revenue],
      DATESINPERIOD(
        'Calendar Lookup'[Date],
      MAX(
        'Calendar Lookup'[Date]
      ),
      -10,
      DAY
    )
  )
  ```

- **Previous Month Returns:** Calculates the total number of returns that occurred in the previous month relative to the current date context in the report. It provides a quick way to compare the return activity between the current and previous month.

  ```
  Previous Month Returns =
  CALCULATE(
    [Total Returns],
    DATEADD(
      'Calendar Lookup'[Date],
      -1,
      MONTH
    )
  )
  ```

- **Previous Month Orders:** Calculates the total number of orders placed in the previous month relative to the current date context in the report. It provides a quick way to compare the order volume between the current and previous month.

  ```
  Previous Month Orders =
  CALCULATE(
    [Total Orders],
    DATEADD(
      'Calendar Lookup'[Date],
      -1,
      MONTH
    )
  )
  ```

- **Previous Month Profit:** Calculates the total profit generated in the previous month relative to the current date context in the report. It provides a quick way to compare the profit between the current and previous month.

  ```
  Previous Month Profit =
  CALCULATE(
    [Total Profit],
    DATEADD(
      'Calendar Lookup'[Date],
      -1,
      MONTH
    )
  )
  ```

- **Order Target:** Sets a target for the number of orders expected in the upcoming month based on a 10% increase over the previous month's orders.

  ```
  Order Target = [Previous Month Orders] * 1.1
  ```

- **Profit Target:** Sets a profit target for the upcoming month based on a 10% increase over the previous month's profit.

  ```
  Profit Target = [Previous Month Profit] * 1.1
  ```

- **Total Revenue (Customer Detail):** Ensures the "Total Revenue" field displays unique values for each customer within the customer detail context. It addresses potential data ambiguity when viewing data by customer.

  ```
  Total Orders (Customer Detail) =
  IF(
    HASONEVALUE(
      'Customer Lookup'[CustomerKey]
    ),
    [Total Orders],
    "-"
  )
  ```

- **Full Name (Customer Detail):** Ensures the "Full Name" field displays the appropriate customer name within the customer detail context. It addresses potential data ambiguity when viewing data by customer.

  ```
  Full Name (Customer Detail) =
  IF(
    HASONEVALUE(
      'Customer Lookup'[CustomerKey]
    ),
    MAX(
      'Customer Lookup'[Full Name]
    ),
    "Multiple Customers"
  )
  ```

- **Order Target Gap:** Calculates the difference between the actual order volume and the established order target.

  ```
  Order Target Gap = [Total Orders] - [Order Target]
  ```

- **Revenue Target Gap:** Calculates the difference between the actual revenue and the established revenue target.

  ```
  Revenue Target Gap = [Total Revenue] - [Revenue Target]
  ```

- **Profit Target Gap:** Calculates the difference between the actual profit and the established profit target.

  ```
  Profit Target Gap = [Total Profit] - [Profit Target]
  ```

- **Adjusted Price:** Calculates an adjusted price based on the average retail price and a user-defined percentage adjustment.

  ```
  Adjusted Price = [Average Retail Price] * (1 + 'Price Adjustment (%)'[Price Adjustment (%) Value])
  ```

- **Adjusted Revenue:** Calculates the total revenue generated by considering both the adjusted price (which factors in user-defined price adjustments) and the order quantities.

  ```
  Adjusted Revenue =
  SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    [Adjusted Price]
  )
  ```

- **Adjusted Profit:** Calculates the estimated profit considering both adjusted revenue and total costs.

  ```
  Adjusted Profit = [Adjusted Revenue] - [Total Cost]
  ```

<a id="dashboard-functionalities"></a>

## Dashboard Functionalities

This interactive dashboard provides a comprehensive overview of AdventureWorks' sales performance across four dedicated pages accessible via a side navigation menu. Each page offers specific functionalities to explore various sales aspects.

**Executive Dashboard:**

- **KPI Overview:** Cards display key performance indicators (KPIs) including total revenue, total profit, total orders, and return rate.

  ![KPI Overview](/images/dashboard_images/01%20KPI%20Cards.png)

- **Revenue Trend:** A line chart with drill-up/down capabilities allows users to explore revenue trends over time at different granularities (year, month, week, date).

  ![Revenue Trend Drill-down](/images/dashboard_animations/revenue_trending_01.gif)
  ![Revenue Trend Drill-up](/images/dashboard_animations/revenue_trending_02.gif)

- **Sales by Category:** A clustered bar chart visualizes the distribution of orders across different product categories.

  ![Sales by Category](/images/dashboard_images/03%20Orders%20by%20Category.png)

- **Monthly Performance:** Cards display trends in monthly revenue, orders, and returns, along with the percentage change compared to the previous month.

  ![Monthly Performance](/images/dashboard_images/05%20Monthly%20Cards.png)

- **Top-Selling Products:** A matrix displays the top ten products by order volume. Users can then drill through to the Product Details page for a deeper analysis of individual product metrics (total orders, revenue, and return rate).

  ![Top-Selling Products Drill Through](/images/dashboard_animations/drill_through.gif)

- **Product Type Insights:** Cards display the most ordered and most returned product types.

  ![Product Type Insights](/images/dashboard_images/06%20Most%20Orders%20&%20Returned%20Product%20Type%20Cards.png)

- **Slicer Panel:** A filter button opens a slicer panel that enables users to filter and view data by year and continent.

  ![Slicer Panel 01](/images/dashboard_animations/slicer_panel_01.gif)
  ![Slicer Panel 02](/images/dashboard_animations/slicer_panel_03.gif)

- **Reset View:** A reset button restores the dashboard to its initial state, removing all applied filters.

  ![Executive Dashboard Reset View](/images/dashboard_animations/exec_dashboard_reset.gif)

**Regional Sales Map:**

- **Global Sales:** A map displays bubbles sized proportionally to the total order volume of each country.

  ![Global Sales](/images/dashboard_images/07%20Map.png)

- **Regional Exploration with Slicer:** A slicer enables users to filter and view data by continent.

  ![Regional Exploration with Slicer](/images/dashboard_animations/map_slicer_01.gif)

**Product Details (Accessible via Drill-Through from Executive Dashboard):**

- **In-Depth Product Performance:** A card displays the selected product.

  ![In-Depth Product Performance](/images/dashboard_images/08%20Selected%20Product%20Card.png)

- **Goal-Oriented Gauges:** Gauges display monthly orders, revenue, and profit for the selected product, enabling users to monitor performance against predefined targets.

  ![Goal-Oriented Gauges](/images/dashboard_images/09%20Gauges.png)

- **Price Sensitivity:** A line with chart drill-up/down capabilities visualizes both total profit and adjusted profit based on a user-defined percentage range for price changes over time at different granularities (year, month, week, date).

  ![Price Sensitivity](/images/dashboard_animations/price_sensitivity.gif)
  ![Price Sensitivity Drill-down](/images/dashboard_animations/profit_trending_01.gif)
  ![Price Sensitivity Drill-up](/images/dashboard_animations/profit_trending_02.gif)

- **Performance Trend:** An area chart with drill-up/down capabilities allows users to explore various product metrics (orders, revenue, profit, returns, return rate) over time at different granularities (year, month, week, date).

  ![Performance Trend](/images/dashboard_animations/product_matric_selection.gif)
  ![Performance Trend Drill-down](/images/dashboard_animations/product_matric_selection_drill_down.gif)
  ![Performance Trend Drill-up](/images/dashboard_animations/product_matric_selection_drill_up.gif)

**Customer Details:**

- **Customer Base Summary:** Cards display a quick summary of total customers and average revenue per customer.

  ![Customer Base Summary](/images/dashboard_images/14%20Customer%20Cards.png)

- **Customer Trend:** A line chart with field selection and drill-up/down capabilities allows users to explore trends in total customers or revenue per customer over time at different granularities (year, month, week, date).

  ![Customer Trend](/images/dashboard_animations/customer_trending.gif)
  ![Customer Trend Drill-down](/images/dashboard_animations/customer_trending_drill_down.gif)
  ![Customer Trend Drill-up](/images/dashboard_animations/customer_trending_drill_up.gif)

- **Customer Segmentation:** Donut charts visualize the distribution of orders by income level and occupation.

  ![Orders by Income Level](/images/dashboard_images/16%20Orders%20by%20Income%20Level.png)
  ![Orders by Occupation](/images/dashboard_images/17%20Orders%20by%20Occupation.png)

- **Top Customer Identification:** A table displays the top 100 customers by order volume, along with their associated total orders and total revenue. Grand totals for all customers are also provided.

  ![Top Customer Identification](/images/dashboard_images/18%20Top%20100%20Customers.png)

- **Customer Insights:** An information button allows users to apply filters and gain deeper insights into specific customer segments. (For example, identifying the skilled manual customer who generated the highest revenue in 2022).

  ![Customer Insights](/images/dashboard_animations/customer_insight.gif)

- **Year-Specific Filter:** A slicer allows users to filter customer data for a specific year.

  ![Year-Specific Filter 01](/images/dashboard_animations/year_slicer_01.gif)
  ![Year-Specific Filter 02](/images/dashboard_animations/year_slicer_02.gif)

- **Top Customer Recognition:** Cards display the top customer by revenue, along with their associated total orders and revenue.

  ![Top Customer Recognition](/images/dashboard_images/20%20Top%20Customer%20by%20Revenue%20Cards.png)

- **Reset View:** A reset button restores the dashboard to its initial state, removing all applied filters.

  ![Customer Details Reset View](/images/dashboard_animations/customer_reset.gif)

**Seamless Navigation:** The side navigation bar provides navigation between the four dashboard pages. Users can simply click the desired button (Executive Dashboard, Regional Sales Map, Product Details, or Customer Details) to switch to the corresponding page.

![Navigation](/images/dashboard_animations/navigation.gif)

<a id="getting-started"></a>

## Getting Started

**Prerequisites:** [Microsoft Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)

**Instructions**

1. Download and install Power BI Desktop.
2. Clone this repository to your local machine.
3. Navigate to the [/adventureworks-report/src/](/src/) folder.
4. Double-click the [adventureworks-report.pbip](/src/adventureworks-report.pbip) file. Power BI Desktop will launch and open the Executive Dashboard.

**Note:** [Images](/images/) and the [dataset](/data/) are not included in this repository as they are exclusive to students enrolled in the [Microsoft Power BI Desktop for Business Intelligence](https://www.udemy.com/course/microsoft-power-bi-up-running-with-power-bi-desktop) course from [Maven Analytics](https://mavenanalytics.io/).
