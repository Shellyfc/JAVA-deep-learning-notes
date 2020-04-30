Expert Tip

Note that you should not include an alias when you write a subquery in a conditional statement. This is because the subquery is treated as an individual value (or set of values in the IN case) rather than as a table.

Also, notice the query here compared a single value. If we returned an entire column IN would need to be used to perform a logical argument. If we are returning an entire table, then we must use an ALIAS for the table, and perform additional logic on the entire table.


1. Provide the name of the sales_rep in each region with the largest amount of total_amt_usd sales.

		WITH t1 AS ( //表1-名字、地区、钱数
		  SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
		   FROM sales_reps s
		   JOIN accounts a
		   ON a.sales_rep_id = s.id
		   JOIN orders o
		   ON o.account_id = a.id
		   JOIN region r
		   ON r.id = s.region_id
		   GROUP BY 1,2
		   ORDER BY 3 DESC), 
		t2 AS ( //表2保留最大钱数
		   SELECT region_name, MAX(total_amt) total_amt
		   FROM t1
		   GROUP BY 1)
		SELECT t1.rep_name, t1.region_name, t1.total_amt
		FROM t1
		JOIN t2
		ON t1.region_name = t2.region_name AND t1.total_amt = t2.total_amt; //同时绑定两个参数
		
		
2. For the region with the largest sales total_amt_usd, how many total orders were placed?

		WITH t1 AS (
			SELECT SUM(o.total_amt_usd) sum, r.name rname, COUNT(o.*) count
			FROM orders o
	
			JOIN accounts a
			ON o.account_id = a.id
			
			JOIN sales_reps s
			ON a.sales_rep_id = s.id
			
			JOIN region r
			ON s.region_id = r.id
			
			GROUP BY 2
		)

		SELECT MAX(sum), rname, count
		FROM t1
		GROUP BY 2,3
		
		----
		WITH t1 AS ( //region_name & total_amt
		   SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
		   FROM sales_reps s
		   JOIN accounts a
		   ON a.sales_rep_id = s.id
		   JOIN orders o
		   ON o.account_id = a.id
		   JOIN region r
		   ON r.id = s.region_id
		   GROUP BY r.name), 
		t2 AS ( // get only the value that matters, not a table
		   SELECT MAX(total_amt)
		   FROM t1)
		SELECT r.name, COUNT(o.total) total_orders
		FROM sales_reps s
		JOIN accounts a
		ON a.sales_rep_id = s.id
		JOIN orders o
		ON o.account_id = a.id
		JOIN region r
		ON r.id = s.region_id
		GROUP BY r.name
		HAVING SUM(o.total_amt_usd) = (SELECT * FROM t2);
		
		

3. How many accounts had more total purchases than the account name which has bought the most standard_qty paper throughout their lifetime as a customer?

		WITH t1 AS(
			SELECT a.name aname, SUM(o.standard_qty) stan_qty, SUM(total) total_qty
			FROM accounts a
			JOIN orders o
			ON o.account_id = a.id
			GROUP BY 1)
		
		t2 AS(
			SELECT MAX(stan_qty), total_qty
			FROM t1
		)
		
		SELECT aname, total_qty
		FROM t1
		WHERE total_qty > (SELECT * FROM t2)
		
		

4. For the customer that spent the most (in total over their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?

		WITH t1 AS(
			SELECT SUM(o.total_amt_usd), a.name aname, a.id aid
			FROM orders o
			JOIN accounts a
			ON o.account_id = a.id
			GROUP BY 2
			ORDER BY 1 DESC
			LIMIT 1
		)
		SELECT COUNT(w.*), w.channel, t1.aname
		FROM web_events
		JOIN t1
		ON t1.aid = w.account_id
		GROUP BY 2


5. What is the lifetime **average** amount spent in terms of total_amt_usd for the top 10 total spending accounts?

		WITH t1 AS(
			SELECT o.total_amt_usd total, a.name
			FROM orders o
			JOIN accounts a
			ON a.id = o.account_id
			ORDER BY 1 DESC
			LIMIT 10
		)
		SELECT AVG(total)
		FROM t1
		

6. What is the lifetime average amount spent in terms of total_amt_usd, including only the companies that spent more per order, on average, than the average of all orders.

		// AVG1: AVG FOR ALL
		// AVG2: AVG FOR COMPANIES ABOVE AVG
		WITH t1 AS(
			SELECT a.name, o.total_amt_usd allTotal
			FROM orders o
			JOIN accounts a
			ON a.id = o.account_id
			),
		t2 AS(
			SELECT AVG(allTotal)
			FROM t1
		),
		t3 AS(
			//SELECT a.name, o.total_amt_usd subTotal
			SELECT a.name, AVG(o.total_amt_usd)
			FROM orders o
			JOIN accounts a
			ON a.id = o.account_id
			**GROUP BY 1**
			//WHERE o.total_amt_usd > (SELECT * FROM t2)
			HAVING AVG(o.total_amt_usd) > (SELECT * FROM t2)
		)
		SELECT AVG(subTotal)
		FROM t3
		//7917.6384593190998269 -> 4721
		---
		WITH t1 AS (
		   SELECT AVG(o.total_amt_usd) avg_all
		   FROM orders o
		   JOIN accounts a
		   ON a.id = o.account_id),
		t2 AS (
		   SELECT o.account_id, AVG(o.total_amt_usd) avg_amt
		   FROM orders o
		   GROUP BY 1
		   HAVING AVG(o.total_amt_usd) > (SELECT * FROM t1))
		SELECT AVG(avg_amt)
		FROM t2;
		//4721.1397439971747168
				
			
			
			
			
			