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
  2    SELECT customer_id, SUM(amount) AS total_spend
  3    FROM transactions
  4    GROUP BY customer_id
  5  )
  6  SELECT
  7    customer_id,
  8    total_spend,
  9  ROW_NUMBER()    OVER (ORDER BY total_spend DESC) AS row_num,
 10  RANK()          OVER (ORDER BY total_spend DESC) AS rnk,
 11  DENSE_RANK()    OVER (ORDER BY total_spend DESC) AS dense_rnk,
 12    PERCENT_RANK()  OVER (ORDER BY total_spend DESC) AS pct_rank
 13  FROM cust_total
 14  ORDER BY total_spend DESC;

 2. Aggregate: SUM(), AVG(), MIN(), MAX() with frame comparisons (ROWS vs RANGE) Use case: Running totals & trends:

   
 SELECT
  2    t.region,
  3    t.sale_date,
  4    t.amount,
  5    SUM(t.amount) OVER (
  6      PARTITION BY t.region
  7      ORDER BY t.sale_date
  8      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
  9    ) AS running_total_rows,
 10    SUM(t.amount) OVER (
 11      PARTITION BY t.region
 12      ORDER BY t.sale_date
 13      RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
 14    ) AS running_total_range
 15  FROM transactions t
 16  ORDER BY t.region, t.sale_date; 

 3. Navigation: LAG(), LEAD(), growth % calculations Use case: Period-to-period analysis


 WITH monthly AS (
  2    SELECT TRUNC(sale_date,'MM') AS month_start,
  3           region,
  4           SUM(amount) AS month_total
  5    FROM transactions
  6    GROUP BY TRUNC(sale_date,'MM'), region
  7  )
  8  SELECT
  9    month_start,
 10    region,
 11    month_total,
 12    LAG(month_total) OVER (PARTITION BY region ORDER BY month_start) AS prev_month_total,
 13    CASE
 14      WHEN LAG(month_total) OVER (PARTITION BY region ORDER BY month_start) IS NULL THEN NULL
 15      WHEN LAG(month_total) OVER (PARTITION BY region ORDER BY month_start) = 0 THEN NULL
 16      ELSE ROUND((month_total - LAG(month_total) OVER (PARTITION BY region ORDER BY month_start))
 17                 / LAG(month_total) OVER (PARTITION BY region ORDER BY month_start) * 100,2)
 18    END AS mom_pct_change
 19  FROM monthly
 20  ORDER BY region, month_start;

 4. Distribution: NTILE(4), CUME_DIST() Use case: Customer segmentation

    
 WITH cust_total AS (
  2    SELECT customer_id, SUM(amount) AS total_spend
  3    FROM transactions
  4    GROUP BY customer_id
  5  )
  6  SELECT
  7    customer_id,
  8    total_spend,
  9    NTILE(4) OVER (ORDER BY total_spend DESC) AS quartile_bucket,
 10    CUME_DIST() OVER (ORDER BY total_spend DESC) AS cume_dist
 11  FROM cust_total
 12  ORDER BY total_spend DESC;

 


 



