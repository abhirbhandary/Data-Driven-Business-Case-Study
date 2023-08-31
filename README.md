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

<img width="200" alt="image" src="https://github.com/abhirbhandary/Data-Driven-Business-Case-Study/blob/main/Images/Picture4.jpg">








