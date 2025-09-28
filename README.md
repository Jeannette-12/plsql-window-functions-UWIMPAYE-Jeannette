# plsql-window-functions-UWIMPAYE-Jeannette

PL/SQL Window Functions Mastery Project

ID: 27745

STEP 1: PROBLEM DEFINITION

A. Business context:
FreshMart is a retail chain (supermarket) with stores in multiple regions (Kigali, Huye, Rubavu). The Sales & Marketing department needs better, data-driven decisions for inventory, promotions and targeted offers.

B. Data challenge:
Sales data is large and row-level; the business needs to identify top products by region/quarter, detect month-over-month growth for store regions, compute running totals for purchasing budget monitoring, and segment customers for targeted campaigns.

C. Expected outcomes:
Identify top 5 products per region and quarterly revenue trends, compute customer quartiles for targeted offers, and deliver recommended promotional actions for the top quartile and underperforming products.

STEP 2: SUCCESS CRITERIA

1. Top 5 products per region/quarter → RANK():produce a ranked list of products for every region and every calendar quarter, so management can target top-selling SKUs per region-quarter.
   
2. Running monthly sales totals → SUM() OVER(): compute cumulative month-to-date (running) sales totals for each region to monitor cumulative performance.
   
3. Month-over-month growth → LAG()/LEAD(): measure percent change from previous calendar month for each region; supports trend alerts.
   
4. Customer quartiles → NTILE(4): segment customers into four buckets (quartiles) by their lifetime spend so you can assign marketing actions per quartile.
   
5. 3-month moving averages → AVG() OVER(): smooth month-to-month volatility by calculating a rolling 3-month average per region.
    
STEP 3: DATABASE SCHEMA

Craetion of three tables : Customers, Products and Transactions.
First we conecct our user to the oracle database I used CMD to verify my codes . So cooncting we do this:
plsql, then it request to input user and password , after  I created new user and password and connect ib sql developer. Then proceeed with tables.

   TABLE 1: CUSTOMERS
 CREATE TABLE customers (
  2  customer_id NUMBER PRIMARY KEY,
  3   name          VARCHAR2(100) NOT NULL,
  4   region        VARCHAR2(50) NOT NULL,
  5      join_date     DATE DEFAULT SYSDATE
  6  );

  Screenshot:
  

   TABLE 2: PRODUCTS
 CREATE TABLE products (
  2  product_id NUMBER PRIMARY KEY,
  3  name          VARCHAR2(100) NOT NULL,
  4    category      VARCHAR2(50)
  5  );

   TABLE 3: TRANSACTIONS
   SQL> CREATE TABLE transactions (
  2      transaction_id NUMBER PRIMARY KEY,
  3      customer_id    NUMBER NOT NULL,
  4      product_id     NUMBER NOT NULL,
  5      sale_date      DATE NOT NULL,
  6      quantity       NUMBER DEFAULT 1,
  7      amount         NUMBER(12,2) NOT NULL,
  8      region         VARCHAR2(50),
  9      CONSTRAINT fk_trx_cust FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
 10      CONSTRAINT fk_trx_prod FOREIGN KEY (product_id) REFERENCES products(product_id)
 11  );

 CREATE SEQUENCE transactions_seq START WITH 1 INCREMENT BY 1 NOCACHE NOCYCLE;

 CREATE OR REPLACE TRIGGER trg_bi_transactions
  2  BEFORE INSERT ON transactions
  3  FOR EACH ROW
  4  BEGIN
  5    IF :NEW.transaction_id IS NULL THEN
  6      SELECT transactions_seq.NEXTVAL INTO:NEW.transaction_id FROM dual;
  7    END IF;
  8  END;
  9
 10  /
 

INSERTING VALUES :
INSERT INTO CUSTOMERS:
 INSERT INTO customers (customer_id, name, region, join_date) VALUES (1, 'Alice Niyigena', 'Kigali', TO_DATE('2024-01-05','YYYY-MM-DD'));
 
INSERT INTO customers (customer_id, name, region, join_date) VALUES (2, 'Ben Kagame',   'Huye',  TO_DATE('2024-02-10','YYYY-MM-DD'));

INSERT INTO customers (customer_id, name, region, join_date) VALUES (3, 'Claire Mutoni','Rubavu',TO_DATE('2024-03-12','YYYY-MM-DD'));

 INSERT INTO customers (customer_id, name, region, join_date) VALUES (4, 'David Tonzi', 'Kigali',TO_DATE('2024-01-20','YYYY-MM-DD'));

 INSERT INTO customers (customer_id, name, region, join_date) VALUES (5, 'Elisa Rukundo', 'Huye',  TO_DATE('2024-04-11','YYYY-MM-DD'));

commit;

INSERT INTO PRODUCTS:


 INSERT INTO products (product_id, name, category) VALUES (1, 'Coffee Beans', 'Beverages');

INSERT INTO products (product_id, name, category) VALUES (2, 'Cooking Oil',  'Household');

 INSERT INTO products (product_id, name, category) VALUES (3, 'Rice 5kg',     'Food');

 INSERT INTO products (product_id, name, category) VALUES (4, 'Soap Pack',    'Toiletries');

INSERT INTO products (product_id, name, category) VALUES (5, 'Sugar 2kg',    'Food');

commit;

INSERT INTO TRANSACTIONS:


INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2  VALUES (1, 1, TO_DATE('2024-01-15','YYYY-MM-DD'), 1, 25000, 'Kigali');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (2, 3, TO_DATE('2024-01-20','YYYY-MM-DD'), 2, 18000, 'Huye');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (3, 2, TO_DATE('2024-02-03','YYYY-MM-DD'), 1, 15000, 'Rubavu');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (4, 1, TO_DATE('2024-02-11','YYYY-MM-DD'), 1, 26000, 'Kigali');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (5, 4, TO_DATE('2024-03-05','YYYY-MM-DD'), 3, 9000,  'Huye');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (1, 3, TO_DATE('2024-03-18','YYYY-MM-DD'), 1, 9000,  'Kigali');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (2, 1, TO_DATE('2024-04-02','YYYY-MM-DD'), 1, 25000, 'Huye');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (3, 5, TO_DATE('2024-04-12','YYYY-MM-DD'), 2, 12000, 'Rubavu');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (4, 2, TO_DATE('2024-05-08','YYYY-MM-DD'), 1, 15000, 'Kigali');

 INSERT INTO transactions (customer_id, product_id, sale_date, quantity, amount, region)
  2   VALUES (5, 3, TO_DATE('2024-05-20','YYYY-MM-DD'), 1, 9000,  'Huye');

 COMMIT;

 ER DIAGRAM:


















STEP 4: WINDOW FUNCTIONS IMPLEMENTATIONS

1. Ranking: ROW_NUMBER(), RANK(), DENSE_RANK(), PERCENT_RANK() Use case: Top N customers by
revenue:

WITH cust_total AS (
  SELECT
    customer_id,
    SUM(amount) AS total_spend          -- total amount spent by this customer across all transactions
  FROM transactions
  GROUP BY customer_id                  -- one row per customer with their total_spend
)

-- Now select from the CTE and apply several analytic (window) functions
SELECT
  customer_id,                           -- the customer's ID
  total_spend,                            -- the pre-calculated total spend from the CTE

  -- 1. Assigns a unique sequential number starting at 1, ordered by highest total_spend.
  ROW_NUMBER() OVER (ORDER BY total_spend DESC) AS row_num,

  -- 2. Assigns a rank based on total_spend, with gaps if two customers tie.
  --    Example: 1,2,2,4 (notice the gap after a tie).
  RANK() OVER (ORDER BY total_spend DESC) AS rnk,

  -- 3. Like RANK but without gaps; ties get the same rank and the next rank is consecutive.
  --    Example: 1,2,2,3.
  DENSE_RANK() OVER (ORDER BY total_spend DESC) AS dense_rnk,

  -- 4. Returns the relative rank of the current row:
  --    (rank - 1) / (total rows - 1), producing a value between 0 and 1.
  --    Useful for percentile-style calculations.
  PERCENT_RANK() OVER (ORDER BY total_spend DESC) AS pct_rank

FROM cust_total
ORDER BY total_spend DESC;               -- show customers from highest to lowest spender

Screenshot:
<img width="951" height="594" alt="ranking functions" src="https://github.com/user-attachments/assets/05d15bb2-1f66-4691-b14a-a9bdade6d032" />


 2. Aggregate: SUM(), AVG(), MIN(), MAX() with frame comparisons (ROWS vs RANGE) Use case: Running totals & trends:



SELECT
  t.region,                                -- region where the sale occurred
  t.sale_date,                             -- date of the sale
  t.amount,                                -- amount of the individual transaction

  -- Running total using a ROWS frame:
  -- For each row (each individual transaction) within the same region,
  -- order by sale_date and sum all amounts from the very first row
  -- (UNBOUNDED PRECEDING) up to the current row.
  SUM(t.amount) OVER (
    PARTITION BY t.region                  -- restart running total for each region
    ORDER BY t.sale_date                   -- order transactions by date
    ROWS BETWEEN UNBOUNDED PRECEDING       -- frame starts from the first row
         AND CURRENT ROW                   -- frame ends at the current row
  ) AS running_total_rows,

  -- Running total using a RANGE frame:
  -- Similar to the above but the frame is defined by the ORDER BY value,
  -- i.e. all rows that have a sale_date less than or equal to the current
  -- row's sale_date. If multiple transactions share the same date,
  -- RANGE will include all of them at once.
  SUM(t.amount) OVER (
    PARTITION BY t.region                  -- again, compute separately for each region
    ORDER BY t.sale_date
    RANGE BETWEEN UNBOUNDED PRECEDING
          AND CURRENT ROW
  ) AS running_total_range

FROM transactions t
ORDER BY t.region, t.sale_date;            -- final output sorted first by region then by date

Screenshot:
<img width="956" height="599" alt="aggregate functions" src="https://github.com/user-attachments/assets/4905d569-7326-4d78-9c5e-2d05da21fe8c" />


 3. Navigation: LAG(), LEAD(), growth % calculations Use case: Period-to-period analysis

WITH monthly AS (
  SELECT
    TRUNC(sale_date,'MM') AS month_start,  -- first day of the month of each sale
    region,                                -- sales region
    SUM(amount) AS month_total             -- total sales amount for that region & month
  FROM transactions
  GROUP BY
    TRUNC(sale_date,'MM'),
    region                                 -- group by month and region
)

-- From that monthly summary, calculate month-over-month growth
SELECT
  month_start,                              -- month being reported (first day of the month)
  region,                                   -- region for which totals are calculated
  month_total,                              -- total sales for this region/month

  -- Previous month's total for the same region
  LAG(month_total) OVER (
    PARTITION BY region                     -- look only within the same region
    ORDER BY month_start                    -- order months chronologically
  ) AS prev_month_total,

  -- Month-over-month % change compared to the previous month
  CASE
    -- If there is no previous month (first month) → NULL
    WHEN LAG(month_total) OVER (PARTITION BY region ORDER BY month_start) IS NULL THEN NULL
    -- If previous month total is 0 → avoid division by zero → NULL
    WHEN LAG(month_total) OVER (PARTITION BY region ORDER BY month_start) = 0 THEN NULL
    -- Otherwise calculate % change: (current – previous) / previous * 100
    ELSE ROUND(
           (month_total
            - LAG(month_total) OVER (PARTITION BY region ORDER BY month_start))
           / LAG(month_total) OVER (PARTITION BY region ORDER BY month_start)
           * 100, 2
         )
  END AS mom_pct_change                      -- Month-over-month percentage change

FROM monthly
ORDER BY region, month_start;                -- show results in region & time order

Screenshot:
<img width="948" height="621" alt="navigation function" src="https://github.com/user-attachments/assets/27ceaf3c-43a0-47d6-857c-be83e52a358f" />



 4. Distribution: NTILE(4), CUME_DIST() Use case: Customer segmentation

    
-- Step 1: Create a CTE (Common Table Expression) that calculates
-- each customer's total spending across all transactions.
WITH cust_total AS (
  SELECT
    customer_id,
    SUM(amount) AS total_spend       -- total amount this customer has spent
  FROM transactions
  GROUP BY customer_id               -- one row per customer with their total_spend
)

-- Step 2: From that summary, calculate quartiles and cumulative distribution
SELECT
  customer_id,                        -- ID of the customer
  total_spend,                         -- total amount spent by the customer

  -- NTILE(4) splits the ordered set into 4 nearly equal groups (quartiles).
  -- Because we order by total_spend DESC:
  --   quartile_bucket = 1 -> top 25% spenders
  --   quartile_bucket = 4 -> bottom 25% spenders.
  NTILE(4) OVER (ORDER BY total_spend DESC) AS quartile_bucket,

  -- CUME_DIST() returns the cumulative distribution: a number between 0 and 1
  -- showing the proportion of rows with total_spend greater than or equal
  -- to the current row's value (given the DESC order).
  -- Example: value 0.75 means 75% of customers have spent at least this much.
  CUME_DIST() OVER (ORDER BY total_spend DESC) AS cume_dist

FROM cust_total
ORDER BY total_spend DESC;             -- display customers from highest to lowest spender

Screenshot:
<img width="951" height="598" alt="distribution function" src="https://github.com/user-attachments/assets/cf23f471-7745-4ba8-bcca-7df6e043f31c" />



STEP 6: RESULT ANALYSIS

1. Descriptive:
   The top-selling products in Kigali for Q1 were Coffee Beans and Rice 5kg. Kigali shows a steady increase in monthly totals from Jan → Mar (running total and MoM +12%).

  2. Diagnostic:
     The increase in Kigali corresponds with promotional activity in Feb and the launch of a loyalty discount; customer quartile analysis shows that top-quartile customers (NTILE=1) account for ~45% of revenue, indicating concentration risk.

   3. Prescriptive:
      Target top-quartile customers with personalized bundles and move underperforming products into a 2-week promotional price or replace with higher-margin SKUs; monitor running totals weekly and set alerts when regional MoM drops below -5%.


STEP 7: REFERENCES

1. https://www.w3schools.com/sql/sql_ref_keywords.asp
2. https://www.youtube.com/watch?v=Ww71knvhQ-s
3. https://mode.com/sql-tutorial/sql-window-functions
4. https://www.geeksforgeeks.org/plsql/window-functions-in-plsql/
5. https://www.datacamp.com/cheat-sheet/sql-window-functions-cheat-sheet
6. https://medium.com/@hisyam126/oracle-sql-window-functions-0114899572c4
7. https://www.youtube.com/watch?v=rIcB4zMYMas
8. https://www.youtube.com/watch?v=y1KCM8vbYe4
9. https://www.youtube.com/watch?v=zAmJPdZu8Rg
10. https://www.youtube.com/watch?v=o666k19mZwE
    



 



