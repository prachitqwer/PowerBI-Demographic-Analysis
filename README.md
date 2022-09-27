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



***2.4 Adding a custom column*** : A column that contains Sales Amount is needed for the analysis. Sales amount can be calculated using Unit Price, Order Quantity and applying Discounts. The same is formulized to create the custom column as shown below. Data type of SalesAmount is changed from “Any” to “Decimal” using Transform option
![image](https://user-images.githubusercontent.com/114446174/192602302-43cfa95e-9366-40ac-abfb-486cf1e189b4.png)<br /><br />

**3. Adding Calendar Table** <br /><br />
In order to apply slicer across years, months and days a calendar table is needed. DAX provides a function called “Calendar” which needs start date and end date as input. To create the table with dynamic start and end date, following DAX code is used.

```
Calendar_Table = 
//Calculate Start Date
var start_date = MIN(Sales_Table[OrderDate])

//Calculate End Date
var end_date = MAX(Sales_Table[OrderDate])

RETURN
    // Create a dynamic calendar table
    CALENDAR ( start_date, end_date)
```

In addition, new columns are added which represent year, month, week and day. This is to provide ability to slice across multiple periods.<br />
![image](https://user-images.githubusercontent.com/114446174/192603189-44fed3e7-7709-4f0f-8b06-030bb156ff64.png)

**4. Creating data model** <br />
To be able to use all three tables, relationships must be defined across them. Customer and Sales table are related by CustomerKey column. Sales and Calendar Table are related by Date and OrderDate columns. The final data model is as shown below.

![image](https://user-images.githubusercontent.com/114446174/192603314-abca85c4-ba71-4f87-aefe-cbdf6f845b52.png)

# **Creating visualizations**

***1. Using Maps visual to show customer’s location*** : "AddressLine1" column is used as Location. SalesAmount is added in Bubble size section to show size of purchase made relative to other regions.<br />

![image](https://user-images.githubusercontent.com/114446174/192603694-b9c55a9c-0f95-4afa-933d-9ef64bd7cdee.png)


***2. Area chart visual for average customer trend*** : To calculate Average Customer Spend, number of unique customers is needed. A measure in Sales Table is created using following code and then displayed using area chart.<br />

```
Average Spend Per Customer = 
var number_of_unique_customers = DISTINCTCOUNT(Sales_Table[CustomerKey])
RETURN DIVIDE(SUM(Sales_Table[SalesAmount]), number_of_unique_customers,0)
```
![image](https://user-images.githubusercontent.com/114446174/192603923-667218ab-6f47-444b-bf2a-71edf3f4e81c.png)


***3. Customer’s age at time of purchase*** : A new column is added in Sales table to represent age of the customer. The age is calculated as difference between birth date and date of first purchase. DAX function DATEDIFF is used.<br />

![image](https://user-images.githubusercontent.com/114446174/192604359-47cf1e57-169b-4135-820b-3b0434c91fbd.png)

Card visual is added which displays the calculated column.<br />

![image](https://user-images.githubusercontent.com/114446174/192604460-f4ce0082-3670-4ea7-95ae-1fe32bf1dad0.png)


***4. Gender Ratio*** : A new measure in Sales table is used to calculate gender ratio. It calculates ratio of males to entire data set as shown below. The measure is then displayed as a card.<br />
![image](https://user-images.githubusercontent.com/114446174/192604671-2dd3b76b-690d-4fdf-b0c7-cef897ee4d50.png)

***5. Marital status*** : Similar to Gender ratio, the marital status percentage is calculated and displayed.<br />
![image](https://user-images.githubusercontent.com/114446174/192604802-dae1678f-6b5c-41b9-88c7-2cf4da9207fa.png)

***6. Average Annual Income*** : New column is created in Sales table which refers the average income column in customer table. Card visual is used to display.<br />

![image](https://user-images.githubusercontent.com/114446174/192604984-5341ff30-38be-4bef-a5fc-c3a85949832c.png)


# **Conclusion**
1. Based on the historical data analyzed, it is conclusive that on average the **customers are spending lesser** on the company’s portal. The average spend per customer has reduced from $3102 in 2010 to $55 in 2014, a **decrease of more than 98%** <br /><br />
2. In addition, the demographic profile revealed that the **average customer is a 43 years old male who is married and earns around $60,000 annually**. These data points are shared with marketing team for there action.

![image](https://user-images.githubusercontent.com/114446174/192605517-c6537dee-1bbd-4a38-b65e-89844950a1c8.png)








