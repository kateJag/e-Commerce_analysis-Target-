



4. Seller Revenue and Geographic Performance
Goal: Identify top-performing sellers and potential geographic advantages.
Analysis Steps:
🏪 Who are the top sellers by revenue and number of orders?
🌍 Where are the best-performing sellers located?
Visualize revenue distribution across locations.
⏳ Delivery Performance
Analyze shipping delays, seller-related issues, and delivery costs.

5. Delivery and Shipping Performance
Goal: Analyze delivery delays, costs, and correlations with shipping speed.

Analysis Steps:
✅ Which sellers have the most frequent late deliveries?

Identify late deliveries where order_delivered_customer_date > order_estimated_delivery_date.
✅ Which sellers have the highest refund/cancellation rates?

Track orders with order_status = "canceled".
✅ Which states/cities experience the longest/fastest shipping delays?

Compare estimated vs. actual delivery dates across regions.
✅ Does shipping cost correlate with faster delivery?

Compare freight_value to actual delivery time.
✅ Which products are most frequently delayed in delivery?

Identify products frequently associated with delays.
Visualization Recommendation:

Heatmaps for geographic delays.
Correlation plots to assess shipping cost vs. delivery speed.