Q1. Total Amount Spent by Each Customer
``` SQL
SELECT 
    sales.customer_id,
    SUM(menu.price) AS total_spend
FROM sales
JOIN menu
    ON sales.product_id = menu.product_id
GROUP BY sales.customer_id;
``

Analysis:

Customer A generated the highest total revenue ($76), closely followed by Customer B ($74), indicating similar overall spending value.

Customer C spent significantly less ($36), suggesting fewer visits or lower-value purchases.

This spending gap highlights a segmentation opportunity where Customer C could be targeted with loyalty incentives to increase average spend.

Q2. Number of Days Each Customer Visited the Restaurant
``` SQL

SELECT 
    customer_id,
    COUNT(DISTINCT order_date) AS total_visits
FROM sales
GROUP BY customer_id
ORDER BY customer_id;
``
Analysis:

Customer B is the most frequent visitor, with visits across six different days, indicating higher engagement.

Customer A shows moderate visit frequency.

Customer C has the lowest engagement, visiting only twice, suggesting potential for visit-frequency promotions.

Q3. First Item Purchased by Each Customer
WITH first_visit AS (
    SELECT 
        customer_id,
        MIN(order_date) AS first_order_date
    FROM sales
    GROUP BY customer_id
)
SELECT 
    s.customer_id,
    m.product_name,
    s.order_date
FROM sales s
JOIN first_visit f
    ON s.customer_id = f.customer_id
   AND s.order_date = f.first_order_date
JOIN menu m
    ON s.product_id = m.product_id
ORDER BY s.customer_id;

Analysis:

All customers made their first purchase on 1 January 2021.

Multiple items were purchased on the first visit by each customer.

This indicates strong opening-day engagement and suggests the first purchase should be treated as a basket of items, not a single product.

Q4. Most Purchased Item on the Menu
SELECT 
    menu.product_name,
    COUNT(*) AS total_purchases
FROM sales
JOIN menu
    ON sales.product_id = menu.product_id
GROUP BY menu.product_name
ORDER BY total_purchases DESC
LIMIT 1;

Analysis:

Ramen is the most frequently purchased item with 8 total purchases.

This indicates a strong customer preference and positions ramen as a key revenue-driving product.

The item could be prioritized in promotions or bundled offers.

Q5. Most Popular Item for Each Customer
WITH customer_item_counts AS (
    SELECT 
        sales.customer_id,
        menu.product_name,
        COUNT(*) AS purchase_count
    FROM sales
    JOIN menu
        ON sales.product_id = menu.product_id
    GROUP BY sales.customer_id, menu.product_name
),
ranked_items AS (
    SELECT
        customer_id,
        product_name,
        purchase_count,
        RANK() OVER (
            PARTITION BY customer_id
            ORDER BY purchase_count DESC
        ) AS item_rank
    FROM customer_item_counts
)
SELECT
    customer_id,
    product_name,
    purchase_count
FROM ranked_items
WHERE item_rank = 1
ORDER BY customer_id;

Analysis:

Ramen is the most frequently purchased item for Customers A and C.

Customer B purchased all menu items equally, indicating no dominant preference.

This supports personalized promotions: ramen-focused offers for A and C, and variety bundles for B.

Q6. First Item Purchased After Becoming a Member
SELECT 
    customer_id,
    product_name,
    order_date
FROM (
    SELECT 
        s.customer_id,
        m.product_name,
        s.order_date,
        ROW_NUMBER() OVER (
            PARTITION BY s.customer_id
            ORDER BY s.order_date
        ) AS rn
    FROM sales s
    JOIN menu m
        ON s.product_id = m.product_id
    JOIN members mem
        ON s.customer_id = mem.customer_id
    WHERE s.order_date >= mem.join_date
) t
WHERE rn = 1;

Analysis:

Customer A’s first post-membership purchase was ramen.

Customer B’s first post-membership purchase was sushi.

Customers continued buying familiar items after joining, suggesting membership did not immediately change preferences.

Q7. Item Purchased Just Before Becoming a Member
SELECT 
    customer_id,
    product_name,
    order_date
FROM (
    SELECT 
        sales.customer_id,
        menu.product_name,
        sales.order_date,
        ROW_NUMBER() OVER (
            PARTITION BY sales.customer_id
            ORDER BY sales.order_date DESC, sales.product_id
        ) AS rn
    FROM sales
    JOIN menu
        ON sales.product_id = menu.product_id
    JOIN members
        ON sales.customer_id = members.customer_id
    WHERE sales.order_date < members.join_date
) ranked_orders
WHERE rn = 1
ORDER BY customer_id;

Analysis:

Customer A last purchased sushi before joining.

Customer B last purchased curry before joining.

Customers were actively engaged prior to enrollment, indicating strong pre-membership brand interaction.

Q8. Total Amount Spent Before Becoming a Member
SELECT 
    sales.customer_id, 
    SUM(menu.price) AS total_spend
FROM sales
JOIN menu
    ON sales.product_id = menu.product_id
JOIN members
    ON sales.customer_id = members.customer_id
WHERE sales.order_date < members.join_date
GROUP BY sales.customer_id
ORDER BY customer_id;

Analysis:

Customer B spent more than double Customer A before joining the program.

This indicates Customer B was already a high-value customer pre-membership.

Customer A’s value appears to have grown more gradually over time.

Q9. Loyalty Points Earned with Sushi 2× Multiplier
SELECT 
    sales.customer_id,
    SUM(
        CASE
            WHEN menu.product_name = 'sushi' THEN menu.price * 20
            ELSE menu.price * 10
        END
    ) AS total_points
FROM sales
JOIN menu
    ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id;

Analysis:

Customer B earned the highest points despite not having the highest spend.

Sushi’s 2× multiplier significantly impacts total points accumulation.

This demonstrates how incentive design can shift reward outcomes beyond pure spending.

Q10. Loyalty Points After First-Week Double Points Promotion
SELECT
    sales.customer_id,
    SUM(
        CASE
            WHEN sales.order_date BETWEEN members.join_date 
                 AND DATE_ADD(members.join_date, INTERVAL 6 DAY)
                THEN menu.price * 20
            WHEN menu.product_name = 'sushi'
                THEN menu.price * 20
            ELSE menu.price * 10
        END
    ) AS total_points
FROM sales
JOIN menu
    ON sales.product_id = menu.product_id
JOIN members
    ON sales.customer_id = members.customer_id
WHERE sales.order_date < '2021-02-01'
GROUP BY sales.customer_id
ORDER BY sales.customer_id;

Analysis:

Customer B accumulated more points (940) than Customer A (860) by the end of January.

Timing of purchases during the promotional window played a critical role.

Points outcomes are influenced by both purchase timing and product mix.

Q11. Detailed Transaction Dataset with Membership Status and Ranking
SELECT
    sales.customer_id,
    sales.order_date,
    sales.product_id,
    menu.product_name,
    CASE
        WHEN members.join_date IS NOT NULL
             AND sales.order_date >= members.join_date
            THEN 'Y'
        ELSE 'N'
    END AS membership_status,
    CASE
        WHEN members.join_date IS NOT NULL
             AND sales.order_date >= members.join_date
            THEN ROW_NUMBER() OVER (
                PARTITION BY sales.customer_id
                ORDER BY sales.order_date
            )
        ELSE NULL
    END AS purchase_rank
FROM sales
JOIN menu
    ON sales.product_id = menu.product_id
LEFT JOIN members
    ON sales.customer_id = members.customer_id
WHERE members.join_date IS NULL
   OR sales.order_date >= members.join_date
ORDER BY sales.customer_id, sales.order_date;

Analysis:

The dataset integrates transaction data, product details, and membership status.

Purchase ranking captures chronological behavior after membership enrollment.

This structure enables advanced analysis of customer retention, repeat behavior, and loyalty program effectiveness.



