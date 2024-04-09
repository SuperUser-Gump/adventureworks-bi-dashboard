# Power BI Dashboard for AdventureWorks

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

- **Product Details Page:** Offers in-depth analysis of product trends and performance.

  ![Product Details Page](/images/dashboard_images/03%20Product%20Detail.png)

- **Customer Details Page:** Identifies and helps understand high-value customer segments for targeted marketing strategies.

  ![Customer Details Page](/images/dashboard_images/04%20Customer%20Detail.png)

**Development Tools:** Power BI Desktop Version: 2.126.1261.0 64-bit (February 2024)

**Disclaimer:**

This project utilizes Power BI Desktop and adheres to the design and structure of the [Microsoft Power BI Desktop for Business Intelligence](https://www.udemy.com/course/microsoft-power-bi-up-running-with-power-bi-desktop) course from [Maven Analytics](https://mavenanalytics.io/). The primary objective is to apply and solidify the concepts learned, not the development of an original dashboard design.

## Data Sources and Transformations

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
