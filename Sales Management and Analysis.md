# Sales Management and Analysis

## Business Requirements Gathering
The business requirements for this project was gathered from the sales manager and other business stakeholders who wanted an executive sales report for all online product sales. Based off the business requirement gathering sessions, the following business requirements as seen below were agreed to be the acceptable functional business requirements for the project.
1. Improvement of sales reports for Internet Sales only with transition from static reports to visual dashboards for better sales reporting.
2. Want to know how much we have sold of what products, to which clients and how it has been over time.
3. Ability for sales persons to be able to filter across all different types of products and customers for there respective geographical locations.
4. What to know how forecasted budgeted annual sales compares to a actual annual sales.

## Data Source
This crucial step identifies all relevant data that needs to be collected, processed, and analyzed to address the business requirements while building the analytical environment. It is essential to regularly review and update data sources as needed so as to ensure that the analytics system continues to receive accurate and up to date data for analysis.

Below here are the models for the existing OLTP (Online Transactional Processing) and OLAP (Online Analytical Processing) data sources. These data sources where modelled using Relational and SnowFlake Schema modeling respectively. There also exist an external data source (SalesBudget) that was provided in Excel format and will be connected to the data model in a later step of the process.
### OLTP Data Model
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource1.JPG "OLTTP Relational Model 1")
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource2.JPG "OLTTP Relational Model 2")
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource3.JPG "OLTTP Relational Model 3")
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource4.JPG "OLTTP Relational Model 4")
### OLAP Data Model
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/493ac4dbdba5d1db68aab577ae373e2b0639eb9e/OLAP%20DataSource.JPG "OLAP Snowflake Schema")

## Data Profiling
The sole aim here is to understand the data’s overall quality, completeness, consistency and distribution. To Identify the different tables, their fields and relationship within the data model. Also, to understand the data types, lengths, and formats of the fields. This knowledge will help understand the overall structure and how it aligns with the expected results.

Below is the preliminary Data Profiling done in SSMS (SQL Server Management Studio) by using SQL scripts to extrat the needed data that fulfills the business requirements for analysis.

### Dimension Tables
**DimCustomer**
```
Select 
  C.CustomerKey,
  C.CustomerAlternateKey As CustomerIDCode,
  C.FirstName As 'First Name',
  C.MiddleName As 'Middle Name',
  C.LastName As 'Last Name',
  C.FirstName + ' ' + (IsNull(C.MiddleName, (Left(C.FirstName,1)))) + '. ' + C.LastName As 'Customer Name', 
  Case
	When C.Gender = 'M' Then 'Male'
	When C.Gender = 'F' Then 'Female'
	End As Gender,
  Case
	When C.MaritalStatus = 'S' Then 'Single'
	When C.MaritalStatus = 'M' Then 'Married'
	End As 'Marital Status',
  C.DateFirstPurchase, 
  G.City, 
  G.StateProvinceCode,
  G.StateProvinceName,
  G.CountryRegionCode As CountryCode,
  G.EnglishCountryRegionName As Country
From 
  DimCustomer As C 
Left Join
  DimGeography As G
  On C.GeographyKey = G.GeographyKey 
Order By 
  CustomerKey
  ```

**DimDate**
```
Select 
  DateKey, 
  FullDateAlternateKey As Date, 
  EnglishDayNameOfWeek As Day, 
  WeekNumberOfYear As 'Week Number', 
  EnglishMonthName As Month, 
  Left(EnglishMonthName, 3) As MonthAbbreviation, 
  MonthNumberOfYear As 'Month Number', 
  CalendarQuarter As Quarter, 
  CalendarYear As Year 
From 
  DimDate 
```

**DimProduct**
```
Select
  P.ProductKey,
  P.ProductAlternateKey As ProductItemCode,
  P.EnglishProductName As 'Product Name',
  PS.ProductSubcategoryAlternateKey As 'Product Subcategory Code',
  Isnull(PS.EnglishProductSubcategoryName, 'NA') As 'Product Sub Category',
  PC.ProductCategoryAlternateKey As 'Product Category Code',
  Isnull(PC.EnglishProductCategoryName, 'NA') As 'Product Category',
  P.Color As 'Product Colour',
  Isnull(P.Size, 'NA') As 'Product Size',
  Isnull(P.ProductLine, 'NA') As 'Product Line',
  Isnull(P.ModelName, 'NA') As 'Product Model Name',
  Isnull(P.EnglishDescription, 'NA') As 'Product Description',
  Isnull(P.Status, 'Outdated') As 'Product Status'
From
  DimProduct As P
Left Join DimProductSubcategory As PS
  On P.ProductSubcategoryKey = PS.ProductSubcategoryKey
Left Join DimProductCategory As PC
  On PS.ProductCategoryKey = PC.ProductCategoryKey
Order By
 P.ProductKey
```
### Fact Table
**FactInternetSales**
```
Select
  ProductKey,
  OrderDateKey,
  DueDateKey,
  ShipDate,
  CustomerKey,
  SalesOrderNumber,
  SalesOrderLineNumber,
  OrderQuantity,
  UnitPrice,
  ExtendedAmount,
  UnitPriceDiscountPct,
  DiscountAmount,
  ProductStandardCost,
  TotalProductCost,
  SalesAmount As FactSalesAmount,
  TaxAmt,
  SalesAmount + TaxAmt As TotalPaidAmount
From
  FactInternetSales
Where
  Convert(Date,(Convert(Varchar, OrderDateKey)), 112) >= DateAdd(Year, -2, GetDate())
Order By OrderDateKey
```

## Data Cleansing & Transformation
Upon data profiling in SSMS, Power Query inside of Power BI was used for the core data cleansing and transformation of the required data set needed to fully fulfill the business requirements for analysis. While cleaning, it is important to look out for outliers as they mosttimes negatively impact data analytics and reporting outcomes thus making the less reliable and invaluable.

Below is an image of the data model from Power BI after data cleasing and transformation. This data model also show how the external data source (FactInternetSalesBudget) have been connected to the data model via the Date dimension table.

![alt text](https://github.com/KLemboye/Projects/blob/c41b7837d5871e4f2d594e7b65df641ab946f836/Data%20Model_Power%20BI.JPG "Power BI Data Model")

## Sales Management Dashboard
Below is the Sales Management Dashboard for the business's Internet Sales only. Page 1 (Sales Overview) gives an overall overview of product sales within the time period seen. Pages 2 (Customer Sales Details) and 3 (Product Sales Details) provides detailed insight and analysis on how the business product sales interacts with the customer and product details respectively.


[Sales Management](https://github.com/KLemboye/Projects/blob/a5dac8c2e7469ca6d1b232e7d7896062c1875c41/InternetSales.svg)


![alt text](https://github.com/KLemboye/Projects/blob/f0e179aea5b9e0141a7d4f6a3568ba6cad87f830/InternetSales_Page1.jpg "Sales Overview")


![alt text](https://github.com/KLemboye/Projects/blob/f0e179aea5b9e0141a7d4f6a3568ba6cad87f830/InternetSales_Page2.jpg "Customer Sales Detail")


![alt text](https://github.com/KLemboye/Projects/blob/f0e179aea5b9e0141a7d4f6a3568ba6cad87f830/InternetSales_Page3.jpg "Product Sales Detail")


