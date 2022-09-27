# Customer Demography Analysis using Power BI

## **Business Context**
An e-commerce retail company suspects that there is decline in customers purchasing product on their portal. The company collects customer information as a part of sign-up process. Historical data about customers and sales is available from 2010 to 2014. 

## **Problem statement**
Senior leadership has asked the analytics team to validate the suspicion using data. In addition, the leadership wants to leverage historical data to perform demography analysis. The outcome of the demography analysis will be utilized by marketing team to perform segmentation and targeting with greater efficacy. 

## **Data availability**
For demography analysis, following data points are needed.
1.	Customer’s address
2.	Average customer spend
3.	Age
4.	Gender
5.	Marital Status
6.	Annual income
Since customer information is sensitive, the data resides in company’s secured SQL server. The analytics team has been given a read access to this SQL database to perform the analysis. It is known that information collected when customer signs up is available in a dimension table named “dbo.DimCustomer”. The shopping related information is available in a fact table named “dbo.FactInternetSales”

# **Pre-requisites**
Apart from the pre-installed Microsoft Office Suite, we need to install 2 important softwares.

1. **Power BI Desktop** is used which is an open source Data Visualization software created by Microsoft as part of the Microsoft Business Intelligence Toolkit. <br />
1.1 Download Power BI from [here](https://powerbi.microsoft.com/en-us/downloads/).<br />
1.2 Step by step installation guide is [available](https://www.youtube.com/watch?v=T_qnV-HTb-M).

2. **Microsft SQL Server**, a relational database management system developed by Microsoft is used for Data Storage, Retrieval and data transformation.<br />
2.1 Download the SQL Server 2019 [Developer version](https://www.microsoft.com/en-in/sql-server/sql-server-downloads)<br />
2.2 SQL Server 2019 Developer Step by step [installation guide](https://www.youtube.com/watch?v=7GVFYt6_ZFM).

3. **Microsoft SQL Server Management Studio**, a software application first launched with Microsoft SQL<br />
3.1 [Download](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) SQL Server Management Studio 18.10<br />
3.2 [SQL Server Management Studio](https://www.youtube.com/watch?v=CqpURYqK_wU) - Step by step installation guide.

# **Data Extraction, Transformation and Loading**

**1.	Form Select queries to extract relevant data from Customer and Sales table** <br />

```
SELECT [CustomerKey]
      ,[FirstName]
      ,[LastName]
      ,[BirthDate]
      ,[MaritalStatus]
      ,[Gender]
      ,[YearlyIncome]
      ,[AddressLine1]
      ,[DateFirstPurchase]
  FROM [AdventureWorksDW2019].[dbo].[DimCustomer]
```

```
SELECT 
      [CustomerKey]
      ,[OrderQuantity]
      ,[UnitPrice]
      ,[DiscountAmount]
      ,[CustomerPONumber]
      ,[OrderDate]
  FROM [AdventureWorksDW2019].[dbo].[FactInternetSales]
```

**2.	Import in Power BI and apply transformations** <br /><br />
***2.1 Data type change*** : It can be seen that the data type of “BirthDate” column in Customer table is Date\Time <br /><br />
![image](https://user-images.githubusercontent.com/114446174/192600842-4cc442d0-6d93-4031-96ba-719f56c42df3.png)


For the analysis, time component is not required. Using Transform option, the data type is changed from “Date\time” to “Date” <br /><br />
![image](https://user-images.githubusercontent.com/114440549/192378161-83626ef7-e1b4-48da-a042-ac4d1e063c6d.png)<br /><br />


***2.2 Turn on Column Quality and Column Distribution***  : Column Quality and Column Distribution options are enabled from View section. These options help in scanning the data for any errors or blank values <br /><br />
![image](https://user-images.githubusercontent.com/114446174/192601747-9a143d13-108d-4d96-b11f-3e6d96f56bfb.png)<br /><br />


***2.3 Removing null values*** : The CustomerPONumber column from Sales table has null values. Therefore, it is not going to be useful in the analysis and is deleted.<br /><br />
![image](https://user-images.githubusercontent.com/114446174/192602023-bedec4f1-21ac-43c8-b8ab-522313e9229c.png)<br /><br />



***2.4 Adding a custom column*** : A column that contains Sales Amount is needed for the analysis. Sales amount can be calculated using Unit Price, Order Quantity and applying Discounts. The same is formulized to create the custom column as shown below.
![image](https://user-images.githubusercontent.com/114446174/192602302-43cfa95e-9366-40ac-abfb-486cf1e189b4.png)<br /><br />








