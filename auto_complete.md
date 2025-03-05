# Auto-Complete with FT.SUGADD


## Introduction
This guide demonstrates how to use FT.SUGADD to store and retrieve autocomplete terms efficiently.

## Adding Suggestions

```sh
FT.SUGADD product_suggestions "iPhone 14 Pro" 100
FT.SUGADD product_suggestions "iPhone 13" 90
FT.SUGADD product_suggestions "iPhone Case - MagSafe" 80
FT.SUGADD product_suggestions "MacBook Pro M2" 100
FT.SUGADD product_suggestions "MacBook Air M1" 95
FT.SUGADD product_suggestions "Dell XPS 15" 85
FT.SUGADD product_suggestions "Samsung Galaxy S23" 88
FT.SUGADD product_suggestions "Google Pixel 7" 87
FT.SUGADD product_suggestions "iPhone 14 Charger" 75
FT.SUGADD product_suggestions "MacBook Charger 67W" 70
FT.SUGADD product_suggestions "iPhone 15 Pro" 100
FT.SUGADD product_suggestions "iPhone Case - Silicone" 78
FT.SUGADD product_suggestions "MacBook Pro M3" 100
FT.SUGADD product_suggestions "HP Spectre x360" 82
FT.SUGADD product_suggestions "OnePlus 11" 85
FT.SUGADD product_suggestions "Samsung Galaxy Z Flip 5" 80
FT.SUGADD product_suggestions "iPhone 15 Charger" 74
FT.SUGADD product_suggestions "Dell XPS 13" 84
FT.SUGADD product_suggestions "Wireless Phone Charger" 77
FT.SUGADD product_suggestions "USB-C Laptop Charger 100W" 72
```

- The number at the end (e.g., 100, 90) is the score, which determines the ranking of suggestions.
- Higher scores appear earlier in the suggestion results.
- The suggestion dictionary (product_suggestions) stores all product names for autocomplete.


## Fetching Suggestions
To retrieve autocomplete suggestions when a user types a query, use:

```sh
FT.SUGGET product_suggestions "iPh" FUZZY
```

- FUZZY allows minor typos (e.g., iPhne → iPhone).
- This will return suggestions like:
    ```sh
    1) "iPhone 13"
    2) "iPhone 15 Pro"
    3) "iPhone 14 Pro"
    4) "iPhone 14 Charger"
    5) "iPhone 15 Charger"
    ```

## Use Case Example
If a user starts typing "Mac", we can fetch suggestions dynamically:

```sh
FT.SUGGET product_suggestions "Mac"
```

## Expected Output:

```sh
1) "MacBook Pro M2"
2) "MacBook Pro M3"
3) "MacBook Air M1"
4) "MacBook Charger 67W"
```

## Conclusion

- FT.SUGADD makes search faster by preloading suggestions.
- FT.SUGGET helps retrieve relevant matches efficiently.
- Adding higher scores to popular items ensures they show up first.

This improves user experience by reducing keystrokes needed to find a product.