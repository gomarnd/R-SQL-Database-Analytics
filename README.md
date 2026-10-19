# R-SQL-Database-Analytics
# 🗄️ Relational Database Management & SQL Analytics in R

## 🎯 Project Overview
This repository demonstrates the integration of **SQL relational database querying** within the **R** statistical computing environment using `DBI` and `RSQLite`. It bridges data engineering and marketing analytics by showcasing how to build in-memory relational schemas, execute complex join operations, and aggregate cross-channel transactional data directly from structured SQL engines.

---

## 🛠️ Core Database Concepts & Methods Applied

### 1. In-Memory Database Schema & Table Construction
- **Engine Setup:** Instantiating an in-memory SQLite database connection using `dbConnect(RSQLite::SQLite(), ":memory:")`.
- **Data Ingestion:** Programmatically populating database tables from structured datasets using `dbWriteTable()`.

### 2. SQL Query Execution & Relational Operations
- **Single-Table Extraction:** Filtering and sorting records using `SELECT`, `WHERE`, and `ORDER BY`.
- **Cross-Table Joins (`INNER JOIN`):** Unifying customer demographic tables with campaign performance tables via common primary/foreign key attributes (`Customer_ID`, `Campaign_ID`).
- **Aggregations & Grouping:** Calculating total spend and regional revenues using `GROUP BY`, `SUM()`, `AVG()`, and `COUNT()`.

---

## 💻 R & SQL Code Implementation

### Complete RSQLite Database Pipeline
```R
library(DBI)
library(RSQLite)

# 1. Establish In-Memory SQLite Connection
con <- dbConnect(RSQLite::SQLite(), ":memory:")

# 2. Create Sample Marketing Relational Tables
customers_df <- data.frame(
  Customer_ID = c(101, 102, 103, 104),
  Name = c("Omar", "Sara", "Khaled", "Laila"),
  Region = c("Damascus", "Aleppo", "Damascus", "Homs")
)

transactions_df <- data.frame(
  Transaction_ID = 1:5,
  Customer_ID = c(101, 102, 101, 103, 104),
  Spend_Amount = c(250, 400, 150, 300, 500),
  Channel = c("Google Ads", "Instagram", "Facebook", "Google Ads", "TikTok")
)

# 3. Write DataFrames to SQLite Database Tables
dbWriteTable(con, "Customers", customers_df, overwrite = TRUE)
dbWriteTable(con, "Transactions", transactions_df, overwrite = TRUE)

# 4. Execute Relational SQL Query (INNER JOIN & Grouping)
sql_query <- "
  SELECT 
    c.Region,
    t.Channel,
    COUNT(t.Transaction_ID) AS Total_Transactions,
    SUM(t.Spend_Amount) AS Total_Regional_Spend,
    ROUND(AVG(t.Spend_Amount), 2) AS Avg_Transaction_Value
  FROM Transactions t
  INNER JOIN Customers c ON t.Customer_ID = c.Customer_ID
  GROUP BY c.Region, t.Channel
  ORDER BY Total_Regional_Spend DESC;
"

# 5. Retrieve Query Results into R DataFrame
regional_analytics <- dbGetQuery(con, sql_query)
print(regional_analytics)

# 6. Disconnect Database Engine
dbDisconnect(con)
