 # 🛒 **Customer Insights Analysis**

## **Steady Growth in Orders**
The number of orders steadily increased between 2017 and 2018, showcasing effective customer acquisition strategies. The growth suggests that the company has been successful in expanding its reach and attracting new customers. However, the limited data for 2016 influence interpreting overall growth trend.

![Number of orders](assets/Number_of_orders.png)

---

## **High-Value Customers Drive Sustained Revenue**
```sql
-- Query to identify the top 3 customers by revenue in each year
query = """
SELECT years, customer_id, payment, d_rank
FROM
(
    SELECT 
        YEAR(orders.order_purchase_timestamp) AS years,
        orders.customer_id,
        SUM(payments.payment_value) AS payment,
        DENSE_RANK() OVER (PARTITION BY YEAR(orders.order_purchase_timestamp)
                           ORDER BY SUM(payments.payment_value) DESC) AS d_rank
    FROM orders 
    JOIN payments ON payments.order_id = orders.order_id
    GROUP BY years, orders.customer_id
) AS a
WHERE d_rank <= 3;
"""
```

The top 3 customers alone generated substantial revenue, indicating that personalized engagement and retention efforts for these customers could yield further benefits. Their behavior over time suggests that maintaining strong relationships with them is critical to sustaining high revenue levels.

![High value customers](assets/High_value_customers.png)
---

## **How many customers stop purchasing after their first order?**
The analysis reveals that new customers significantly outperformed returning customers in revenue contribution. With **94%** of the revenue coming from new customers, this highlights the company’s strong customer acquisition efforts. However, this also suggests an opportunity to enhance retention strategies, as returning customers contributed only **6%** of total revenue.

![New Customers vs Returning](assets/one-time_vs_returned.png)

**Recommendation:**  
- Given that 93,098 customers are one-time buyers, creating targeted post-purchase engagement campaigns and offering loyalty rewards to encourage repeat purchases. Personalized follow-up marketing via email, personalized product recommendations, and special discount offers can effectively convert these one-time buyers

---

## **What is contribution to the revenue One-Time Buyers vs Returning customers?**

```sql
-- query calculates customer segmentation by categorizing customers as either 
--'One-Time Buyer' or 'Retained Customer' based on their order count. 
-- and aggregates metrics, such as customer count, total revenue, and average orders per customer.
query = """
SELECT 
    CASE 
        WHEN order_count = 1 THEN 'One-Time Buyer'
        ELSE 'Retained Customer'
    END AS customer_type,
    COUNT(customer_unique_id) AS customer_count,
    SUM(total_payment) AS total_revenue,
    AVG(order_count) AS avg_orders_per_customer
FROM (
    SELECT 
        customers.customer_unique_id,
        COUNT(DISTINCT orders.order_id) AS order_count,
        SUM(payments.payment_value) AS total_payment
    FROM customers
    JOIN orders ON customers.customer_id = orders.customer_id
    JOIN payments ON orders.order_id = payments.order_id
    GROUP BY customers.customer_unique_id
) AS customer_summary
GROUP BY customer_type;
"""
```
A majority of customers **(93,098)** were one-time buyers, accounting for most of the revenue. However, retained customers **(2,997)** displayed higher revenue per customer, making them more valuable in the long run.

![Contribution](assets/contribution.png)

**Recommendation:**  
- Converting one-time buyers into loyal, repeat customers through follow-up marketing, incentives, and post-purchase engagement could substantially increase overall revenue stability.

---

## **Are there seasonal trends in purchases?**
The seasonal trend analysis indicates consistent growth in purchases throughout the year, with noticeable spikes during **November** and **December**, possibly due to holiday promotions or end-of-year sales. September showed a dip, suggesting this period may benefit from promotional campaigns to stimulate demand.

![Seasonality](assets/seasonal_order_trends.png)

---

## **Product Category Performance**
Sales analysis across product categories reveals that certain segments, such as **Bed Table Bath**, **Computer Accessories**, and **Automotive**, dominate revenue contributions. Conversely, categories like **Cine Photo** and **Blu-Ray DVDs** underperform.

![Product Category Performance](assets/top_sales_per_category.png)

**Recommendation:**  
- Prioritizing high-performing categories for promotions and optimizing underperforming ones through targeted advertising or product re-evaluation could boost revenue.
