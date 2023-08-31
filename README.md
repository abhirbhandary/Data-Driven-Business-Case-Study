# 📈 Data Driven Business Case Study

## 📌 Solution
Eniac is an online marketplace specializing in Apple-compatible accessories. Eniac is exploring an expansion to the Brazilian market.
Here comes Magist. Magist is a Brazilian Software as a Service company that offers a centralized order management system to connect small and medium-sized stores with the biggest Brazilian marketplaces.
Eniac is considering signing a 3-year contract with Magist as an intermediate step.

There are three main concerns:
1. Customer Satisfaction.
2. It is not clear that Magist is a good partner for these high-end tech products.
3. Are deliveries fast enough?

## Customer Satisfaction

### 1. Sentiment Analysis of Customer Reviews
![Customer Sentiment](https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture1.jpg)
![Top Key Phrase](https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture2.jpg)

#### 1.1 How is the average review of Magist products?
````sql
SELECT 
    CASE
        WHEN pt.product_category_name_english IN ('audio', 'cds_dvds_musicals', 'consoles_games', 'dvds_blu_ray', 'electronics', 'computers_accessories', 'pc_gamer', 'computers', 'tablets_printing_image', 'telephony', 'fixed_telephony') 
        THEN 'Tech'
        ELSE 'others'
    END AS category,
    SUM(oi.order_item_id) AS total_order_items, Avg(review_score) as review_score
FROM order_items as oi
JOIN products as p ON oi.product_id = p.product_id join order_reviews as ro on ro.order_id = oi.order_id
JOIN product_category_name_translation as pt ON p.product_category_name = pt.product_category_name
GROUP BY category
#HAVING category = 'Tech'
ORDER BY total_order_items DESC;
````
**Answer:**

<img width="400" alt="image" src="https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture9.jpg">


***
## 2. Is Magist a good partner for these high-end tech products
The percentage of tech-product orders is relatively low.
![Low](https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture3.jpg)

#### 2.1 How many products of these tech categories have been sold (within the time window of the database snapshot)? What percentage does that represent from the overall number of products sold?
````sql
SELECT 
    CASE
        WHEN pt.product_category_name_english IN ('audio', 'cds_dvds_musicals', 'consoles_games', 'dvds_blu_ray', 'electronics', 'computers_accessories', 'pc_gamer', 'computers', 'tablets_printing_image', 'telephony', 'fixed_telephony') 
        THEN 'Tech'
        ELSE 'others'
    END AS category,
    SUM(oi.order_item_id) AS total_order_items, SUM(order_item_id) * 100.0 / (SELECT SUM(order_item_id) FROM order_items) as Percentage
FROM order_items as oi
JOIN products as p ON oi.product_id = p.product_id
JOIN product_category_name_translation as pt ON p.product_category_name = pt.product_category_name
GROUP BY category
#HAVING category = 'Tech'
ORDER BY total_order_items DESC;
````
**Answer:**

<img width="400" alt="image" src="https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture4.jpg">

#### 2.2 Are expensive tech products popular?
````sql
SELECT
    CASE
        WHEN price <= 100 THEN 'Low'
        WHEN price > 100 AND price <= 500 THEN 'Medium'
        ELSE 'High'
    END AS price_range,
    AVG(price) AS avg_price,
    SUM(oi.order_item_id) AS total_units_sold
FROM products as p
JOIN order_items as oi ON p.product_id = oi.product_id
JOIN product_category_name_translation as pt ON p.product_category_name = pt.product_category_name
WHERE pt.product_category_name_english IN ('audio', 'cds_dvds_musicals', 'consoles_games', 'dvds_blu_ray', 'electronics', 'computers_accessories', 'pc_gamer', 'computers', 'tablets_printing_image', 'telephony', 'fixed_telephony')
GROUP BY price_range
ORDER BY avg_price;
````
**Answer:**

<img width="400" alt="image" src="https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture5.jpg">

***
## 3. Are deliveries fast enough?
Delivery days vs. Days in Post (for Tech products)
![Average Days](https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture6.jpg)

#### 3.1 What’s the average time between the order being placed and the product being delivered?
````sql
SELECT YEAR(o.order_approved_at) AS order_year,
    CASE
        WHEN pt.product_category_name_english IN ('audio', 'cds_dvds_musicals', 'consoles_games', 'dvds_blu_ray', 'electronics', 'computers_accessories', 'pc_gamer', 'computers', 'tablets_printing_image', 'telephony', 'fixed_telephony') 
        THEN 'Tech'
        ELSE 'others'
    END AS category,
    AVG(DATEDIFF(o.order_delivered_customer_date, o.order_approved_at)) as average_time_difference
FROM order_items as oi
JOIN products as p ON oi.product_id = p.product_id
JOIN product_category_name_translation as pt ON p.product_category_name = pt.product_category_name
JOIN orders as o ON oi.order_id = o.order_id
GROUP BY order_year, category
ORDER BY order_year;
````
**Answer:**

<img width="200" alt="image" src="https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture7.jpg">

#### 3.2 Is there any pattern for delayed orders, e.g. big products being delayed more often?
````sql
SELECT 
    CASE
        WHEN product_weight_g <= 1000 THEN 'Small'
        WHEN product_weight_g > 1000 AND product_weight_g <= 5000 THEN 'Medium'
        ELSE 'Large'
    END AS weight_range,
    COUNT(*) AS total_orders,
    SUM(CASE
        WHEN order_delivered_customer_date > order_estimated_delivery_date THEN 1
        ELSE 0
    END) AS delayed_orders,
    (SUM(CASE
        WHEN order_delivered_customer_date > order_estimated_delivery_date THEN 1
        ELSE 0
    END) / COUNT(*)) * 100 AS delayed_percentage
FROM orders
JOIN order_items AS oi ON orders.order_id = oi.order_id
JOIN products AS p ON oi.product_id = p.product_id
JOIN product_category_name_translation AS pt ON p.product_category_name = pt.product_category_name
GROUP BY weight_range;

````
**Answer:**

<img width="400" alt="image" src="https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture8.jpg">

***







