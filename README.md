# Redis Search Guide

## Introduction

This guide demonstrates how to use Redis Search with JSON documents stored in Redis. It covers setting up a dataset, creating an index, and performing various search queries using RediSearch.

## Dataset Initialization

To store product data in Redis JSON format, use the following commands:

```
JSON.SET product:101 $ '{"productId": "101", "productName": "Red Running Shoes", "category": "Footwear", "price": 59.99, "description": "Comfortable red running shoes for daily exercise."}'

JSON.SET product:102 $ '{"productId": "102", "productName": "Wireless Earbuds", "category": "Electronics", "price": 29.99, "description": "High-quality wireless earbuds with noise cancellation."}'

JSON.SET product:103 $ '{"productId": "103", "productName": "Stainless Steel Water Bottle", "category": "Accessories", "price": 12.99, "description": "Durable 500ml stainless steel water bottle."}'

JSON.SET product:104 $ '{"productId": "104", "productName": "Blue Denim Jacket", "category": "Clothing", "price": 89.99, "description": "Stylish blue denim jacket for casual wear."}'

JSON.SET product:105 $ '{"productId": "105", "productName": "Digital Alarm Clock", "category": "Home", "price": 19.99, "description": "Compact digital alarm clock with LED display."}'

JSON.SET product:106 $ '{"productId": "106", "productName": "Leather Wallet", "category": "Accessories", "price": 24.99, "description": "Classic leather wallet with multiple compartments."}'

JSON.SET product:107 $ '{"productId": "107", "productName": "Yoga Mat", "category": "Sports", "price": 35.00, "description": "Eco-friendly non-slip yoga mat with cushioning."}'

JSON.SET product:108 $ '{"productId": "108", "productName": "Cookware Set", "category": "Kitchen", "price": 120.00, "description": "Non-stick cookware set, includes pots and pans."}'

JSON.SET product:109 $ '{"productId": "109", "productName": "Smart Watch", "category": "Electronics", "price": 199.99, "description": "Feature-rich smart watch with fitness monitoring."}'

JSON.SET product:110 $ '{"productId": "110", "productName": "Electric Toothbrush", "category": "Personal Care", "price": 39.95, "description": "Rechargeable electric toothbrush with multiple modes."}'
```

## Creating an Index

To enable searching, create an index on the stored JSON documents:

```
FT.CREATE idx:products ON JSON SCHEMA \
    $.productId AS productId TAG \
    $.productName AS productName TEXT \
    $.category AS category TAG \
    $.price AS price NUMERIC \
    $.description AS description TEXT
```

## Querying the Data

### 1. Numeric Range Query

Retrieve products with a price range between 30 and 50:
```
FT.SEARCH idx:products "@price:[30 50]"
```

### 2. Aggregation: Average Price by Category

Calculate the average price of products in each category:
```
FT.AGGREGATE idx:products "*" \
    GROUPBY 1 @category \
    REDUCE AVG 1 @price AS averagePrice \
    SORTBY 2 @averagePrice DESC
```

### 3. Full-Text Search

Find products containing the word "wireless":
```
FT.SEARCH idx:products "wireless"
```

Wildcard search for words starting with "wire":
```
FT.SEARCH idx:products "wire*"
```

### 4. Tag-Based Search

Retrieve products belonging to the "Electronics" category:
```
FT.SEARCH idx:products "@category:{Electronics}"
```

## Conclusion

This guide provides a basic overview of using Redis Search with JSON data. You can extend it by adding more advanced filtering, indexing options, and search optimizations to suit your application’s needs.