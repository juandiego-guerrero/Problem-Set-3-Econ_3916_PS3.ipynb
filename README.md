# Problem-Set-3-Econ_3916_PS3.ipynb
Problem Set 3: The Number Nobody Can Reproduce

**ECON 3916 · Applied Data Analytics** · Topics 9–11

## Objective
The dashboard reported $1,240 average revenue per West customer, while Finance reported $812. I set out to find which number is right, why they differ, and whether the West's account-management programme actually raised revenue.

## Methodology
I built a simulated warehouse in DuckDB with 3,000 customers (2,000 in the West) and 20,570 orders, where revenue rises with order count. I wrote both teams' queries in SQL and compared their row counts. Then I set 15% of customer regions to missing to see how filters treat unknown values, and I used a window function to compare each customer with their region's average without collapsing rows. To test the causal question, I ran a separate simulation of the programme with a known true effect of +$25 and compared regression estimates with and without controls. Finally, I used AI to build and test a query reviewer that flags these problems before a number reaches a dashboard.

## Key Findings
- **Finance's number is the correct one.** Both queries average the same 2,000 West customers, but the dashboard's join to orders turns them into 14,004 rows. Customers who order more are counted more often, so the average rises from $823.93 to $1,256.76.
- **Missing regions disappear silently.** With 450 regions unknown, `region != 'West'` returns 848 customers instead of the 1,298 I meant to keep, because a comparison with an unknown value is neither true nor false. West plus "not West" covers only 2,550 of 3,000 customers.
- **The West-versus-rest gap says almost nothing about the programme.** The raw gap was +$213.72 against a true effect of +$25, because West accounts were bigger to begin with. Adjusting for account size recovered +$26.88.
- **The wrong control can erase a real effect.** Adding support tickets, which the programme itself increases, pushed the estimate to −$1.06.
- **The query reviewer passed all seven test queries**, with 3 hits, 0 misses and 0 false alarms.

## Limitations
All data is simulated, so the programme result shows how the methods behave, not what the programme does in reality. The adjustment works only because I knew and measured the confounder; with real data, other confounders could remain. The query reviewer was tested on seven queries and handles only simple query shapes.

## Tools and Data
Python (pandas, NumPy, statsmodels), DuckDB for SQL, and Google
