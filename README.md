![](https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/sales_banner.PNG)

# Sales Analysis with Budget Management
### Business Analysis project related to Realtime Product Sales Analysis

## The purpose of this project is to provide an end-to-end Business Analysis Solution to minimize Customer Attrition for a Telecom service provider.

#### The project aims to determine the factors associated with Customer churn along with quantification and categorization of the churn risk associated with any user in the subscriber base and to find out the most popular churn segments.   


## Demo-Preview

The end goal is to create a business solution report satisfying all the demands of the client (as per the specified acceptance criteria) mentioned in the business request document. Here's a glimpse of the final report.

![](https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/Sales_analysis_preview.gif)
<p align="center">
  <em>Business Solution Report</em>
</p

[(Back to top)](#table-of-contents)


## Table of contents

- [Demo-Preview](#demo-preview)
- [Installation](#installation)
- [Background](#background)
- [Business case](#business-case)
- [Requirement Gathering and Documentation](#requirement-gathering-and-documentation)
- [Data Cleansing and Transformation](#data-cleansing-and-transformation)
- [Data Modelling](#data-modelling)
- [Sales Management Dashboard](#sales-management-dashboard)
- [Conclusion](#conclusion)



## Installation
  
Apart from the pre-installed Microsoft Office Suite, we need to install 2 important softwares.

1. Power BI Desktop is used which is an open source Data Visualization software created by Microsoft as part of the Microsoft Business Intelligence Toolkit.
  
   1.1 As per your system specifications. [Download Power BI from here.](https://powerbi.microsoft.com/en-us/downloads/)
  
   1.2 [Step by step installation guide.](https://www.youtube.com/watch?v=T_qnV-HTb-M)
 
2. Microsft SQL Server, a relational database management system developed by Microsoft is used for Data Storage, Retrieval and data transformation.
  
   2.1 [Download the SQL Server 2019 Developer version from this link](https://www.microsoft.com/en-in/sql-server/sql-server-downloads)
   
   2.2 [SQL Server 2019 Developer - Step by step installation guide.](https://www.youtube.com/watch?v=7GVFYt6_ZFM)
  
 
3. Microsoft SQL Server Management Studio, a software application first launched with Microsoft SQL
  
   3.1 [Download SQL Server Management Studio 18.10 from this link](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)
  
   3.2 [SQL Server Management Studio - Step by step installation guide. ](https://www.youtube.com/watch?v=CqpURYqK_wU)
  

[(Back to top)](#table-of-contents)


## Background

### What is Sales analysis?

As the name suggests, sales analysis involves analyzing the sales made by a company over a period of time. Many companies have a monthly sales analysis , a quarterly sales analysis or an annual sales analysis. A regular sales analysis helps the company understand where they are performing better and where they need to improve. Sales analysis also allows the company to make proper budget allocations so that the profit is maximized.
> [For more details about Sales Analysis, go through my blog.](https://inblog.in/Sales-Analysis-and-Management-v78SlMKYMg)

[(Back to top)](#table-of-contents)

#### Let us see how an actual sales analysis is performed.

## Business case

Any Business Intelligence project begins with the understanding of the client demands. A client had sent a Business Request in form of an email stating their requirements and demands. Below is the Business Request received from the customer. I have changed the name due to non disclosure agreement issues and retracted the client's name.

<p align="center">
  <img src="https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/BR%20sales.PNG" width="1050" title ="Business Request Document">
  <em>Business Request Document</em>
</p>


[(Back to top)](#table-of-contents)



## Requirement Gathering and Documentation

Once a Business Request document is received, the first step is to determine the exact demands of the customer. Usually a one-on-one meeting with the designated person helps us to map the client's mind and get on our journey.

Documentation is the most critical part of a project development as it is the foundation of all further project deliverables describing what inputs and outputs are associated with each process function. After analyzing the Business Request document, a Business Requirement Document is created mentioning the  Reporter name, the value of change required, the necessary systems to be incorporated and other relevant info.

Based on the request that was made from the client, the following user stories were defined to fulfill delivery and ensure that acceptance criteria’s were maintained throughout the project.

I have created a Business Requirement documentation for our business case as shown below. (PS: Names are changed due to confidentiality issues).


<p align="center">
  <img src="https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/BRD_Sales_Analysis.PNG" width="1050" title ="Business Requirement Document">
  <em>Business Requirement Document</em>
</p>

[(Back to top)](#table-of-contents)


## Data Cleansing and Transformation

The client has provided the backup sales database which contained combination of fact as well as dimension table data.

The data provided was from 2010, however since we are required to analyze only the last year and the current year data we will first perform two steps.

1. **Update the database.**

2. **Filter out the data for the two required years.**

To create the necessary data model for doing analysis and fulfilling the business needs defined in the user stories we need to extract only the required tables from the entire dataset.

Also in order to ease the analysis and dashboarding process, only the most important fields from the required tables are retained in the final transformed data. 
We have chosen the 

1. **Sales Fact Table**
2. **Customer Details Table** (Dimension table)
3. **Product details table** (Dimension table)
4. **Calendar table** (Dimension table). 

Along with these, the client had also provided the **budget data** in an excel sheet which also forms the part of a fact table.

Below are the **SQL statements** for cleansing and transforming necessary data.

### Dimension_Calendar:

```
-- Cleansed DIM_Date Table --
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  --[DayNumberOfWeek], 
  [EnglishDayNameOfWeek] AS Day, 
  --[SpanishDayNameOfWeek], 
  --[FrenchDayNameOfWeek], 
  --[DayNumberOfMonth], 
  --[DayNumberOfYear], 
  --[WeekNumberOfYear],
  [EnglishMonthName] AS Month, 
  Left([EnglishMonthName], 3) AS MonthShort,   -- Useful for front end date navigation and front end graphs.
  --[SpanishMonthName], 
  --[FrenchMonthName], 
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year --[CalendarSemester], 
  --[FiscalQuarter], 
  --[FiscalYear], 
  --[FiscalSemester] 
FROM 
 [AdventureWorksDW2019].[dbo].[DimDate]
WHERE 
  CalendarYear >= 2019
```

### Dimension_Customers:

```
-- Cleansed DIM_Customers Table --
SELECT 
  c.customerkey AS CustomerKey, 
  --      ,[GeographyKey]
  --      ,[CustomerAlternateKey]
  --      ,[Title]
  c.firstname AS [First Name], 
  --      ,[MiddleName]
  c.lastname AS [Last Name], 
  c.firstname + ' ' + lastname AS [Full Name], 
  -- Combined First and Last Name
  --      ,[NameStyle]
  --      ,[BirthDate]
  --      ,[MaritalStatus]
  --      ,[Suffix]
  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender,
  --      ,[EmailAddress]
  --      ,[YearlyIncome]
  --      ,[TotalChildren]
  --      ,[NumberChildrenAtHome]
  --      ,[EnglishEducation]
  --      ,[SpanishEducation]
  --      ,[FrenchEducation]
  --      ,[EnglishOccupation]
  --      ,[SpanishOccupation]
  --      ,[FrenchOccupation]
  --      ,[HouseOwnerFlag]
  --      ,[NumberCarsOwned]
  --      ,[AddressLine1]
  --      ,[AddressLine2]
  --      ,[Phone]
  c.datefirstpurchase AS DateFirstPurchase, 
  --      ,[CommuteDistance]
  g.city AS [Customer City] -- Joined in Customer City from Geography Table
FROM 
  [AdventureWorksDW2019].[dbo].[DimCustomer] as c
  LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey 
ORDER BY 
  CustomerKey ASC -- Ordered List by CustomerKey
```

### Dimension_Product:

```
-- Cleansed DIM_Products Table --
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS ProductItemCode, 
  --      ,[ProductSubcategoryKey], 
  --      ,[WeightUnitMeasureCode]
  --      ,[SizeUnitMeasureCode] 
  p.[EnglishProductName] AS [Product Name], 
  ps.EnglishProductSubcategoryName AS [Sub Category], -- Joined in from Sub Category Table
  pc.EnglishProductCategoryName AS [Product Category], -- Joined in from Category Table
  --      ,[SpanishProductName]
  --      ,[FrenchProductName]
  --      ,[StandardCost]
  --      ,[FinishedGoodsFlag] 
  p.[Color] AS [Product Color], 
  --      ,[SafetyStockLevel]
  --      ,[ReorderPoint]
  --      ,[ListPrice] 
  p.[Size] AS [Product Size], 
  --      ,[SizeRange]
  --      ,[Weight]
  --      ,[DaysToManufacture]
  p.[ProductLine] AS [Product Line], 
  --     ,[DealerPrice]
  --      ,[Class]
  --      ,[Style] 
  p.[ModelName] AS [Product Model Name], 
  --      ,[LargePhoto]
  p.[EnglishDescription] AS [Product Description], 
  --      ,[FrenchDescription]
  --      ,[ChineseDescription]
  --      ,[ArabicDescription]
  --      ,[HebrewDescription]
  --      ,[ThaiDescription]
  --      ,[GermanDescription]
  --      ,[JapaneseDescription]
  --      ,[TurkishDescription]
  --      ,[StartDate], 
  --      ,[EndDate], 
  ISNULL (p.Status, 'Outdated') AS [Product Status] 
FROM 
  [AdventureWorksDW2019].[dbo].[DimProduct] as p
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey 
order by 
  p.ProductKey asc
```

### Fact_Sales:

```
-- Cleansed FACT_InternetSales Table --
SELECT 
  [ProductKey], 
  [OrderDateKey], 
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey], 
  --  ,[PromotionKey]
  --  ,[CurrencyKey]
  --  ,[SalesTerritoryKey]
  [SalesOrderNumber], 
  --  [SalesOrderLineNumber], 
  --  ,[RevisionNumber]
  --  ,[OrderQuantity], 
  --  ,[UnitPrice], 
  --  ,[ExtendedAmount]
  --  ,[UnitPriceDiscountPct]
  --  ,[DiscountAmount] 
  --  ,[ProductStandardCost]
  --  ,[TotalProductCost] 
  [SalesAmount] --  ,[TaxAmt]
  --  ,[Freight]
  --  ,[CarrierTrackingNumber] 
  --  ,[CustomerPONumber] 
  --  ,[OrderDate] 
  --  ,[DueDate] 
  --  ,[ShipDate] 
FROM 
  [AdventureWorksDW2019].[dbo].[FactInternetSales]
WHERE 
  LEFT (OrderDateKey, 4) >= YEAR(GETDATE()) -2 -- Ensures we always only bring two years of date from extraction.
ORDER BY
  OrderDateKey ASC
```

>Now that we have transformed the required dataset using the above SQL statements, we can export the generated tables to a CSV file for the final modelling and dashboarding. This is achieved by using the Import/Export wizard of SQL Server Management Studio.


[(Back to top)](#table-of-contents)


## Data Modelling

Data Modelling is the process of analyzing the data objects and their relationship to the other objects. It is used to analyze the data requirements that are required for the business processes. 

In order to achieve this we will load our datasets to Microsoft Power BI Desktop. Since the initial cleansing and transformation is already done using SQL querying statements we can skip the Power Query Editor and directly begin our modelling part.

Once the dataset is loaded in Power BI Desktop, we have to create the schema diagram using the **star schema** mechanism. 

The primary keys of the dimension table are connected to the corresponding foreign key of the Fact Sales table on a one to many relationship as shown below.

<p align="center">
  <img src="https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/schema.png" width="1050" title="Schema Diagram ">
  <em>Schema Diagram </em>
</p>

Once the data modelling is done we have to create certain measures which would allow us to better depict the affect on sales by different customer and products overtime as per the client's demands. 

The three measures once calculated are moved to a newly created standalone table called the **measure table**.

The three measures calculate are:

1. **Total Sales over the specified time period.**

```

Sales = SUM ( FACT_InternetSales[SalesAmount] )

```


2. **Total Budget of the specified time period.**

```

Budget = SUM ( FACT_Budget[Budget] )

```


3. **Total profit made**  

```

Profit = [Sales] - [Budget]

```

>With the three calculated measures along with the fact and dimension tables we now move on to designing the dashboard

[(Back to top)](#table-of-contents)


## Sales Management Dashboard

As per the client's demands and based on the acceptance criteria we have created three separate dashboard namely 

- **Sales Overview**

- **Customer Details**

- **Product Details**


> These dashboard together form the Customer Sales Analysis and Budget Management Report.


### 1. Sales Overview:

The Sales overview dashboard is an **interactive real-time sales analysis dashboard** fulfilling the **first business request** of **migration from static reports to interactive reports.**

It displays the:

- **KPI of Sales vs Budget** allowing for proper visualization of the profits made. 

- As the customer needed to **view the effects overtime** , we have included the **Year as well as month filters**. 

The Sales overview also shows the:

- **Sales based on Product category** as the client has mentioned that they have specialized Sales representative for each sub category and wanted to analyze the performance of their sales employees. 

- **Top 10 Customers and Products based on the sales**, which were also **part of the Business demand** overview. 

- In order to facilitate the analysis of **sales in different region** we have included a **map visual** displaying the sales amount as circles on specific locations with radius proportionality to sales amount.

<p align="center">
  <img src="https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/salesoverview.png" width="1050" title="Sales Overview Dashboard ">
  <em>Sales Overview Dashboard</em>
</p>

### 2. Customer Details:

The customer details dashboard contains visuals depicting the more specific details of **sales based on the customer** i.e. 

- The **Top 10 customer based on sales**, 

- **Purchase by the top 10 customer over time**  (monthly analysis) 

- **Comparison of the budget allocated and the revenue generated.**


<p align="center">
  <img src="https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/custodetails.png" width="1050" title="Customer Details dashboard">
  <em>Customer Details dashboard</em>
</p>

### 3. Product Details:

The Product details dashboard contains visual the more specific details of **sales based on the product** i.e. 

- The **Top 10 products based on sales**, 

- **Sale of the top 10 product over time** 

- **Comparison of the budget allocated and the revenue generated.**

<p align="center">
  <img src="https://github.com/ankitkash101/Sales-Analysis-with-Budget-Management/blob/main/proddetails.png" width="1050" title="Product Details dashboard">
  <em>Product Details dashboard</em>
</p>



[(Back to top)](#table-of-contents)



## Conclusion

All the tasks mentioned in the BRD has been completed as per the specified acceptance criteria. Once the final report is prepared, we publish the dashboard to  Power BI service and schedule automated data refresh based on the client's specification.

This concludes a Business Analysis project of Customer Churn Prediction analysis.



[(Back to top)](#table-of-contents)

