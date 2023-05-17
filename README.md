As a fresher data analyst, I recently embarked on a project to analyze the Brazilian E-Commerce Public Dataset by Olist.
This analysis aims to build Sales & Operation Dashboard and analyze how much Olist have sold of what products and how it has been over time, all to gain insight into how consumers are purchasing in this platform.
For this project, I used Microsoft SQL Server Management Studio and Power BI. 

INTRODUCTION

This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses FROM all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to the customers using Olist logistics partners. See more on our website: www.olist.com

After a customer purchases the product FROM Olist Store a seller gets notified to fulfill that order. Once the customer receives the product, or the estimated delivery date is due, the customer gets a satisfaction survey by email where he can give a note for the purchase experience and write down some comments.

DESCRIPTION OF THE DATASETS

1) olist_orders_dataset: This table is connected to 4 other tables. It is used to connect all the details related to an order.
- order_id：Order ID number
- customer_id： Customer ID number
- order_status：Status of order process
- order_purchase_timestamp：Time of the product is ordered
- order_approved_at：Time of payment for the order
- order_delivered_carrier_date：Time of notifying the logistics in order process
- order_delivered_customer_date：Time of the product arrived at the consumer
- order_estimated_delivery_date：Estimated arrival time of the order

2) olist_order_items_dataset: It contains the details of an item that had been purchased such as shipping date, price and so on.
- order_item_id：The number of the product in the order (ex：If there are three items in the order, the order_item_id is 1, 2, 3)
- product_id：Product ID number
- seller_id ：Seller ID number
- shipping_limit_date：The deadline for the seller deliver the goods to the logistics
- price：Product price
- freight_value：delivery fee

3) olist_order_reviews_dataset: It contains details related to any reviews posted by the customer on a particular product that he had purchased.
- review_id：The comment ID number
- review_score：Customer satisfaction
- review_comment_title：Comment title
- review_comment_message：Comment message
- review_creation_date：Time of inviteing customer make a comment
- review_answer_timestamp：Time of customer finish the comment

4) olist_products_dataset: It contains related to a product such as the ID, category name and measurements.
- product_category_name：Product category
- product_name_lenght：Number of words in product name
- product_description_lenght：Number of words in product description
- product_photos_qty：Number of product photo
- product_weight_g：Weight of product（gram）
- product_length_cm：Length of product (centimeter)
- product_height_cm：Height of product（centimeter）
- product_width_cm：Width of product（centimeter)

5) olist_order_payments_dataset: The information contained in this table is related to the payment details associated with a particular order.
- order_id：unique identifier of an order
- payment_sequential：a customer may pay an order with more than one payment method. If he does so, a sequence will be created to
- payment_type：method of payment
- payment_installments：number of installments
- payment_value：transaction value
 
6) olist_customers_dataset: Details the customer base information of this firm.
- customer_unique_id：customer ID number
- customer_zip_code_prefix：Zip code of customer
- customer_city：City of customer
- customer_state：State of customer

7) olist_geolocation_dataset: It contains geographical information related to both the sellers and customers.
- geolocation_zip_code_prefix：Zip code of geolocation
- geolocation_lat: Latitude of geolocation
- geolocation_lng: Longitude of geolocation
- geolocation_city：City name
- geolocation_state：State name

8) olist_sellers_dataset: This table contains the information related to all the sellers who have registered with this firm.
- seller_zip_code_prefix：Zip code of customer
- seller_city：City of seller
- seller_state：State of seller

PREPARE

This involves the data cleansing & transformation process. During this process, SQL was used.
Below are the SQL statements for cleaning and transforming necessary data:

Change column data type

Change column name
- sp_rename 'geolocation.geolocation_zip_code_prefix', 'zip_code', 'COLUMN';
- sp_rename 'geolocation.geolocation_city', 'city', 'COLUMN';
- sp_rename 'geolocation.geolocation_state', 'state', 'COLUMN';
- sp_rename 'geolocation.geolocation_lat', lat, 'COLUMN';
- sp_rename 'geolocation.geolocation_lng', lng, 'COLUMN';
- sp_rename 'customers.customer_city', 'city', 'COLUMN';
- sp_rename 'customers.customer_state', 'state', 'COLUMN';
- sp_rename 'sellers.seller_zip_code_prefix', 'zip_code', 'COLUMN';
- sp_rename 'sellers.seller_city', 'city', 'COLUMN';
- sp_rename 'sellers.seller_state', 'state', 'COLUMN';

Check datasets have any missing value
- There are some datetime variables in table orders have some missing value, infer that some order be canceled lead to order process be interupt.
- There are some string variable in table reviews such as title and message have a lot missing value, infer that Olist does not enforce customers make the comment.

Generate Key Primary (payment_id) for table ‘payments’

ALTER TABLE payments

ADD payment_id INT IDENTITY(1,1)

Drop the duplicated value of review_id in table ‘reviews’

DELETE FROM reviews WHERE ROW_NUMBER () OVER (PARTITION BY review_id ORDER BY order_id) > 1;
