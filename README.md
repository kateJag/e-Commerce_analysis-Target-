  # 📈 **Product, Revenue and Payment Analysis**
 
 ### **Goal:**  
Determine which product categories are the most profitable and which ones underperform.

---

## 1) **Which product categories have the highest profit margins?**
```sql
SELECT 
    p.product_category_name AS category,
    SUM(order_items.price) AS total_revenue,
    SUM(order_items.freight_value) AS total_shipping_cost,
    SUM(order_items.price) - SUM(order_items.freight_value) AS profit_margin,
    (SUM(order_items.price) - SUM(order_items.freight_value)) / SUM(order_items.price) * 100 AS profit_margin_percentage
FROM 
    order_items 
JOIN 
    products p ON order_items.product_id = p.product_id
GROUP BY 
    p.product_category_name
ORDER BY 
    profit_margin_percentage DESC;
```



## 2) **Profit Margin Thresholds**  
These thresholds were calculated using the 25th, 50th, and 75th percentiles to categorize performance:

- **25th Percentile:** 77.63%  
- **Median (50th Percentile):** 81.54%  
- **75th Percentile:** 85.67%  

---

![Product categories with the highest profit margins](assets/highest_profit_margin.png)

 The visualization above shows the top product categories sorted by their profit margin percentage, with key thresholds displayed for analysis

---

## **✨ Key Insights**
- **Top Performers:** Categories like **PCs**, **House pastals oven and cafe**, and **Fixed telephony** exhibit extremely high profit margins, often exceeding 90%.
- **Potential Targets:** Categories below the 75th percentile need further investigation to improve profitability.
- **Strategic Focus:** Products in the high-margin category can be prioritized for further investment, while low-margin categories can be optimized to reduce costs or improve pricing.


## 3) **Product Revenue Contribution and Volume Analysis**

### Goal:
Identify the products driving revenue and analyze the impact of order volume and product price.

 ## *What percentage of revenue comes from the top 20% of products (Pareto Principle)?*
  
SQL Query:
```sql
SELECT 
    products.product_id, 
    SUM(order_items.price) AS total_sum, 
    products.product_category_name AS category
FROM 
    order_items
JOIN 
    products ON order_items.product_id = products.product_id
GROUP BY 
    products.product_id, products.product_category_name
ORDER BY 
    total_sum DESC;
```
![Pareto](assets/pareto.png)
## **Key Insights**
- The top **8,535** products out of **32,951** total products (approximately 25%) contribute to 75% of the total revenue.
- **Revenue Drivers**: This confirms the presence of the **Pareto Principle** within the dataset, meaning that a small fraction of products generates the majority of revenue. The products contributing to this top 20% should be prioritized for marketing, promotions, and strategic investments
- **Optimization Opportunity:** Products making up the remaining 75% of products but only contributing to 25% of revenue could be optimized by either discontinuing underperforming products or adjusting prices and promotions


## 4) **High Revenue, Low Order Volume vs. High Volume, Low Value**

**Comparoson:**  ![comparison](assets/comparison.png)

## **✨ Key Insights**
High Revenue, Low Order Volume:
- Categories like **Computer Accessories**, **Electrices 2**, and **Construction Tools** generate significant revenue despite relatively low order volumes.
- This indicates that these products have high average revenue per order, likely due to higher unit prices or bulk purchases.

High Volume, Low Value:
- Categories such as **Watches Present**, **Bed Table Bath**, and **Health Beauty** have high order volumes but low revenue per order.
- These products may be lower in price or involve frequent small purchases, indicating a volume-driven revenue strategy.

## 5) Payment Preferences and Revenue Contribution

sql
```
SELECT 
    payment_type, 
    COUNT(payment_type) AS payments_count,
    SUM(payment_value) AS total_payment_value
FROM payments
GROUP BY payment_type
ORDER BY total_payment_value
```
## **✨ Key Insights**
- **Credit Cards Dominate:** based on visualization, credit cards are not only preferred for everyday purchases but also for high-ticket transactions, possibly due to features like higher spending limits and reward programs

- **UPI: Fast-Growing Contender with Moderate Value** a rising star, driven by mobile and digital adoption. However, UPI’s contribution to value per transaction is moderate, indicating that it is frequently used for low to mid-value purchases. This pattern might be linked to its **popularity among younger** users for smaller purchases

- **Vouchers: High Volume but Limited Value**, accounting for 11,550 transactions but generate only 0.76 million in revenue. The gap between transaction count and revenue contribution could indicate that voucher users primarily focus on savings, for smaller purchases or redemptions.

- **Debit Cards: Limited Contribution, Underperforming Potential**, only 3,058 transactions and a total payment value of 0.43 million. This may suggest that debit card users have a conservative spending pattern, possibly due to limited account balances or lack of perks compared to credit cards.

 ![method](assets/payment_methods.png)

 ## **Installment Correlation Analysis**
```python
# correlation and scatter plot
installments_comparison = installments_comparison[(installments_comparison['total_payment_value'] > 0) & (installments_comparison['payment_installments'] > 0)]
correlation = installments_comparison['total_payment_value'].corr(installments_comparison['payment_installments'])
print(f"Correlation between total payment value and payment installments: {correlation:.2f}")

plt.figure(figsize=(8, 6))
sns.scatterplot(data=installments_comparison, x='payment_installments', y='total_payment_value', alpha=0.5)
plt.title('Scatter Plot of Payment Value vs. Installments')
plt.xlabel('Payment Installments')
plt.ylabel('Payment Value')
plt.show()
```

**Scatter Plot:**  ![scatter](assets/scatter.png)
---

## **✨ Key Insights**
- **Installment Preference:** A significant number of high-value payments are made in installments, as evidenced by the larger total payment volume for installment payments compared to single payments.
- **Weak Correlation:** Despite the high total installment payment volume, the correlation analysis shows that there is **no strong linear relationship** between the number of installments and payment value.

---

# **Statewise Analysis Based on Payments**

### **Goal:**  
Understand how payment activity is distributed across different states and over time.



## **States Per Year Based on Total Payments**
![States Per Year Based on Total Payments](assets/Sales_per_Year_Based_on_Totat_Payments.png)



## **✨ Key Insights**
- **Dominant States:** The state of **SP** consistently shows the highest total payment volume across all years, followed by other key states like **MG**and **RJ**.
- **Yearly Trends:** Across multiple states, there is a noticeable increase in total payment volume from 2016 to 2018, reflecting either business expansion or increased customer activity in key regions.

---





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
## **✨ Key Insights**
- The top 3 customers alone generated substantial revenue, indicating that personalized engagement and retention efforts for these customers could yield further benefits. Their behavior over time suggests that maintaining strong relationships with them is critical to sustaining high revenue levels.

![High value customers](assets/High_value_customers.png)
---

## **How many customers stop purchasing after their first order?**

- **94%** of the revenue coming from **new customers**, this highlights the company’s strong customer acquisition efforts. However, this also suggests an opportunity to enhance retention strategies, as **returning customers** contributed only **6%** of total revenue.

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
