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
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource1.JPG "OLTTP Relational Model")
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource2.JPG "OLTTP Relational Model")
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource3.JPG "OLTTP Relational Model")
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/02e9d240e460d03a4fd9bd53e613a7c5f0c7651a/OLTP%20DataSource4.JPG "OLTTP Relational Model")
### OLAP Data Model
![alt text](https://github.com/KLemboye/SALES-ANALYSIS-PROJECT/blob/493ac4dbdba5d1db68aab577ae373e2b0639eb9e/OLAP%20DataSource.JPG "OLAP Snowflake Schema")

## Data Profiling
The sole aim here is to understand the data’s overall quality, completeness, consistency and distribution. To Identify the different tables, their fields and relationship within the data model. Also, to understand the data types, lengths, and formats of the fields. This knowledge will help understand the overall structure and how it aligns with the expected results. The initial Data Profiling was done in SSMS (SQL Server Management Studio).

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

```

**DimProduct**
```

```

## Data Cleansing & Transformation

Clean the data, look out for outliers. This will ultimately lead to more reliable and valuable insights from the analytics system.


To create the necessary data model for doing analysis and fulfilling the business needs defined in the user stories the following tables were extracted using SQL.

One data source (sales budgets) were provided in Excel format and were connected in the data model in a later step of the process.

Below are the SQL statements for cleansing and transforming necessary data.
