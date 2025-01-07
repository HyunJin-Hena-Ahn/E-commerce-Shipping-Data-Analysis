# E-commerce-Shipping-Data-Analysis
### Analyzing Shipment Delays and Identifying Process Optimizations for Improved Delivery Performance
Dashboard can be seen [here](https://public.tableau.com/app/profile/hyun.jin.ahn3409/viz/ShipmentManagementDataAnalysis/Dashboard3)

**Tools:** Excel, Python, SQL(GoogleBigQuery), Tableau
## Table of Contents 

- [Background and Overview](#background-and-overview)
- [Data Structure and Processing](#data-structure-and-preprocessing)
- [Executive Summary](#executive-summary)
- [Insights Deep Dive](#insights-deep-dive)
- [Recommendations](#recommendations)

## Background and Overview

This project investigates e-commerce shipment data to uncover insights that can help optimize shipment performance and improve delivery efficiency. The analysis focuses on shipment delays, their correlation with discounts, product importance, and shipping modes. The ultimate goal is to understand key operational bottlenecks and recommend improvements to enhance shipping efficiency.
- Shipments with discounts above 10% face operational bottlenecks leading to 100% delays.
- High-importance products are disproportionately delayed in Ship and Road modes.
- Specific warehouse blocks (B, D, F) contribute to the highest delay rates.

## Data Structure and Preprocessing 

The dataset used for this analysis includes shipment data with the following key columns. 
- **ID:** Unique shipment identifier.
- **Warehouse_block:** Location in the warehouse where the product was stored.
- **Mode_of_Shipment:** Method of transportation (e.g., Flight, Ship, Road).
- **Customer_care_calls:** Number of calls made by the customer to customer service.
- **Customer_rating:** Rating given by the customer (1 to 5).
- **Cost_of_the_Product:** Price of the product (formatted as currency).
- **Prior_purchases:** Number of prior purchases made by the customer.
- **Product_importance:** Importance level of the product (e.g., low, medium, high).
- **Discount_offered:** Discount percentage provided on the product.
- **Weight_g:** Weight of the product in grams.
- **Arrived_on_Time:** Whether the product arrived on time (1 for no, 0 for yes).

Data preprocessing involved cleaning and transforming the dataset to ensure its readiness for analysis.
- Converted Cost_of_the_Product to numeric format for ease of analysis.
- Standardized Weight to kilograms.
- Renamed Arrived_on_Time for clarity in communication.
- Identified the weight distribution heavily right-skewed, created Weight Categories based on product weight for better classification.(e.g., Light, Medium, Heavy).

``` python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

file_path = r'C:\Users\Owner\OneDrive - UW\Attachments\DataAnalysisProjects\E-commerce_Shipping_orignialdataset.csv'
dataset = pd.read_csv(file_path)

plt.figure(figsize=(14, 6))

# Distribution plot for Weight in kg
plt.subplot(1, 2, 2)
sns.histplot(dataset['Weight_kg'], kde=True, bins=20, color='green')
plt.title('Distribution of Weight in Kg')
plt.xlabel('Weight (kg)')
plt.ylabel('Frequency')

plt.tight_layout()
plt.show()
```


## Executive Summary 

The analysis revealed several key insights into shipping delays and performance.
- **Delay Rates and Discounts:** Shipments with discounts above 10% experienced 100% delays, indicating a significant operational bottleneck for these shipments.
- **High-Importance Products:** Contrary to expectations, high-importance products faced the highest delay rates across all shipping modes, highlighting the challenges in prioritizing critical shipments.
- **Mode and Warehouse Block Impact:** Specific warehouse blocks and shipping modes exhibited higher delay rates for high-importance products, suggesting areas for operational improvements.

The insights are aimed at identifying areas for process optimization, improving shipment efficiency, and recommending changes on shipping operations to enhance performance.
## Insights Deep Dive 

#### Delays by Discount Offered
```sql
SELECT
    Discount_offered,
    COUNT(*) AS Total_Shipments,
    SUM(CASE WHEN Arrived_on_Time = false THEN 1 ELSE 0 END) AS Delayed_Shipments,
    ROUND(SUM(CASE WHEN Arrived_on_Time = false THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS Delay_Percentage
FROM `optimizing-delivery-efficiency.Ecommerce_shipping_data.shipping`
GROUP BY Discount_offered
ORDER BY Discount_offered ASC;
```
Shipments with discounts over 10% showed a sudden increase in delay rates. This could be due to operational bottlenecks, such as bulk processing of discounted items or increased shipment volume.

**Key Question: Are high-discount shipments under-prioritized, or is there increased complexity with these shipments?**

#### Impact of Warehouse Block on Shipping Mode Delays
The visualizations showed the following. [View here](https://public.tableau.com/app/profile/hyun.jin.ahn3409/viz/ShipmentManagementDataAnalysis/Dashboard3)



- **High-Importance Products and Ship Mode**: High-importance products shipped via Ship mode exhibited the highest delay rates, particularly from Warehouse Blocks B, D, and F. This suggests that, despite being priority shipments, they are facing operational challenges that hinder timely delivery when shipped through this mode.
- **High-Importance Products and Road Mode**: Similarly, Road mode also showed significant delays for high-importance products, with Warehouse Blocks A and C contributing to the highest delays. This indicates that Road mode is not as effective for handling high-importance shipments, even though it generally performs better than Ship mode for non-priority products.
#### Product Importance and Delivery Rate
High-importance products were found to be more delayed than lower priority ones, even though they should have been prioritized. This suggests a need for a refined approach to managing these critical shipments, possibly involving dedicated processing lanes or more advanced tracking mechanisms.
```sql
SELECT
    Product_importance,
    COUNT(*) AS Total_Shipments,
    SUM(CASE WHEN Arrived_on_Time = 1 THEN 1 ELSE 0 END) AS Delayed_Shipments,
    ROUND(SUM(CASE WHEN Arrived_on_Time = 1 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS Delay_Percentage
FROM
    `optimizing-delivery-efficiency.shippingdataset.shipping`
GROUP BY
    Product_importance
ORDER BY
    Delay_Percentage DESC;
```
## Recommendations 

Based on the analysis, the following recommendations are provided to optimize shipping operations and reduce delays. 
- **Analyze and Address Delays in High-Discount Shipments.** Shipments with discounts above 10% are experiencing 100% delays. To address this, it's necessary to adjust prioritization strategies for discounted shipments and streamline processes for bulk orders. Additionally, investigate whether specific warehouse blocks or shipping modes are contributing to these delays.
- **Enhance Prioritization for High-Importance Products.** High-importance products face significant delays in both Ship and Road modes. A solution would be to implement dedicated routes and processing teams for these critical shipments to ensure they are prioritized and delivered on time.
- **Improve Warehouse Blocks and Operational Processes.** Delays are concentrated in specific warehouse blocks: B, D, F for Ship mode, and A, C for Road mode. Optimizing workflows and staffing in these blocks can significantly reduce delays and improve delivery efficiency for both high-priority and discounted shipments.

#### Data Source 
[View here](https://www.kaggle.com/datasets/prachi13/customer-analytics)
