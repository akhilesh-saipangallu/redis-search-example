# Electronic Products Search Guide

## Introduction
This guide covers key Redis Search features using a electronic product catalog. It demonstrates:

1. Full-Text Search – How search results are ranked based on weights.
2. Fuzzy Search – Handling minor typos in queries.
3. Synonyms – Expanding search results using alternative terms.
4. Sorting Based on a Custom Field – Enhancing search relevancy with custom ranking


## Create product index

```sh
FT.CREATE idx:product ON JSON PREFIX 1 "product:"
    SCHEMA $.name AS name TEXT WEIGHT 5.0
    $.description AS description TEXT WEIGHT 1.0
    $.category AS category TAG
```

## Add products
```sh
JSON.SET product:1 $ '{"name": "iPhone 14 Pro", "description": "Apple iPhone 14 Pro with A16 Bionic chip and 48MP camera.", "category": "smartphone"}'
JSON.SET product:2 $ '{"name": "iPhone 13", "description": "Apple iPhone 13 with A15 Bionic chip and dual-camera system.", "category": "smartphone"}'
JSON.SET product:3 $ '{"name": "iPhone Case - MagSafe", "description": "Official Apple MagSafe case for iPhone 14 Pro.", "category": "accessories"}'
JSON.SET product:4 $ '{"name": "MacBook Pro M2", "description": "Apple MacBook Pro with M2 chip and Retina display.", "category": "laptop"}'
JSON.SET product:5 $ '{"name": "MacBook Air M1", "description": "Apple MacBook Air with M1 chip, lightweight design, and long battery life.", "category": "laptop"}'
JSON.SET product:6 $ '{"name": "Dell XPS 15", "description": "Dell XPS 15 with Intel i7 processor and 4K OLED display.", "category": "laptop"}'
JSON.SET product:7 $ '{"name": "Samsung Galaxy S23", "description": "Samsung Galaxy S23 with Snapdragon 8 Gen 2 and Dynamic AMOLED display.", "category": "smartphone"}'
JSON.SET product:8 $ '{"name": "Google Pixel 7", "description": "Google Pixel 7 with Tensor G2 chip and AI-enhanced camera.", "category": "smartphone"}'
JSON.SET product:9 $ '{"name": "iPhone 14 Charger", "description": "20W USB-C fast charger compatible with iPhone 14 and later models.", "category": "accessories"}'
JSON.SET product:10 $ '{"name": "MacBook Charger 67W", "description": "67W USB-C power adapter for MacBook Air and MacBook Pro.", "category": "accessories"}'
JSON.SET product:11 $ '{"name": "iPhone 15 Pro", "description": "Apple iPhone 15 Pro with A17 Bionic chip and Titanium design.", "category": "smartphone"}'
JSON.SET product:12 $ '{"name": "iPhone Case - Silicone", "description": "Soft-touch silicone case for iPhone 15 Pro with microfiber lining.", "category": "accessories"}'
JSON.SET product:13 $ '{"name": "MacBook Pro M3", "description": "Apple MacBook Pro with M3 chip, Liquid Retina XDR display, and powerful GPU.", "category": "laptop"}'
JSON.SET product:14 $ '{"name": "HP Spectre x360", "description": "HP Spectre x360 convertible laptop with Intel i7 and OLED touchscreen.", "category": "laptop"}'
JSON.SET product:15 $ '{"name": "OnePlus 11", "description": "OnePlus 11 with Snapdragon 8 Gen 2, Hasselblad cameras, and 120Hz AMOLED display.", "category": "smartphone"}'
JSON.SET product:16 $ '{"name": "Samsung Galaxy Z Flip 5", "description": "Samsung foldable phone with 120Hz AMOLED display and Snapdragon 8 Gen 2.", "category": "smartphone"}'
JSON.SET product:17 $ '{"name": "iPhone 15 Charger", "description": "30W USB-C power adapter for iPhone 15 series.", "category": "accessories"}'
JSON.SET product:18 $ '{"name": "Dell XPS 13", "description": "Dell XPS 13 with Intel i5, InfinityEdge display, and lightweight design.", "category": "laptop"}'
JSON.SET product:19 $ '{"name": "Wireless Phone Charger", "description": "15W fast wireless charger for iPhone, Samsung, and Pixel devices.", "category": "accessories"}'
JSON.SET product:20 $ '{"name": "USB-C Laptop Charger 100W", "description": "100W USB-C charger compatible with MacBook, Dell XPS, and HP Spectre.", "category": "accessories"}'
```



The **weight** given to `name` is higher, **meaning matches in product names rank higher than descriptions**.

## 1. Full-Text Search Example

Run the following search query:

```sh
FT.SEARCH idx:product "iPhone"
```

### Expected Behavior

- Products with `"iPhone"` in their **name** (e.g., `"iPhone 14 Pro"`) will appear **first** due to the higher weight.
- Products where `"iPhone"` appears in the **description** (e.g., "Wireless Phone Charger - compatible with iPhones") will be ranked **lower**.

## 2. Fuzzy Search (Handling Typos)
If a user types `"iphne"` (instead of `"iPhone"`), we can use **FUZZY** matching to still get relevant results.

```sh
FT.SEARCH idx:product "%iphne%"
```

### Expected Behavior

- This will return results containing `"iPhone"` despite the misspelling.
- Useful for catching typos like `"iPhon"` or `"iphobe"`.

## Synonyms Search (Before & After Comparison)
Run the search:

```sh
FT.SEARCH idx:product "smartphone"
```

### Expected Result

No **iPhone** products appear unless "smartphone" is in their description.

### Defining Synonyms
To make **"smartphone"** and **"iPhone"** interchangeable, run:
```sh
FT.SYNUPDATE idx:product 1 "iPhone" "smartphone"
```

Now, search for **"smartphone"** again:

```sh
FT.SEARCH idx:product "smartphone"
```

### Expected Behavior

- **iPhone products** now appear in the results!
- This expands search results without modifying product data.

To verify the synonym mapping:

```sh
FT.SYNDUMP idx:product
```


## Relevance Scoring
We can modify the search query to enhance result relevance based on a **custom-defined priority**. For example, when a user searches for `"iPhone"`, we want to ensure that **iPhone models (e.g., iPhone 14, iPhone 15 Pro)** appear **before** accessories like cases and chargers.

By leveraging a **custom scoring mechanism**, we can assign a higher priority to core products and a lower priority to accessories, ensuring that users see the most relevant items first.

```sh
FT.SEARCH idx:product "iphone" load 1 category APPLY "(@category == 'smartphone') * 5 + (@category == 'accessories') * 3" AS r_score SORTBY r_score DESC
```

- This method refines search results, ensuring a more intuitive and user-friendly shopping experience, where primary products take precedence over related add-ons.