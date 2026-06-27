# Advanced DAX Documentation — Product Performance & Ranking

## 1) `Return Rate-P` (Measure)
Product-level return intensity.

```dax
Return Rate-P = 
VAR TotalSoldProductQty = 
    CALCULATE(SUM(Fact_Sales[Quantity]))
VAR TotalReturnedProductQty =
    CALCULATE(
        SUM(Fact_Sales[Quantity]),
        'Return Status'[Return Status] = "Returned"
    )
VAR Result =
    DIVIDE(
        TotalReturnedProductQty, TotalSoldProductQty
    )
RETURN
    Result
```

## 2) `Product Sales Rank` (Measure)
Descending sales rank.

```dax
Product Sales Rank = 
    RANKX(
        ALL(Dim_Product),
        [Total Sales],
        ,
        DESC, 
        Dense
    )
```

## 3) `Product Profit Rank` (Measure)
Descending profit rank.

```dax
Product Profit Rank = 
    RANKX(
        ALL(Dim_Product),
        [Total Profit],
        ,
        DESC,  
        Dense
    )
```

## 4) `Product Return Rank` (Measure)
Ascending return-rate rank (lower return rate = better rank).

```dax
Product Return Rank = 
    RANKX(
        ALL(Dim_Product),
        [Return Rate-P],
        ,
        ASC, 
        Dense
    )
```

## 5) `Product Performance Score` (Measure)
Weighted composite score: Profit (50%), Sales (30%), Returns (20%).

```dax
Product Performance Score = 
[Product Profit Rank] * .5 +
// Weight 50%
[Product Sales Rank] * .3 +
// Weight 30%
[Product Return Rank] * .2 
// Weight 20%
```

## 6) `Best Product Name` (Measure)
Retrieves product with minimum (best) composite score.

```dax
Best Product Name = 
    CALCULATE(
        MAX(
            Dim_Product[Product Name]), 
            FILTER(
                ALL(Dim_Product),
                [Product Performance Score] =
                MINX(
                    ALL(Dim_Product),
                    [Product Performance Score])
            )
    )
```

## 7) `Poorest Product Name` (Measure)
Retrieves product with maximum (worst) composite score.

```dax
Poorest Product Name = 
    CALCULATE(
        MAX(
            Dim_Product[Product Name]), 
            FILTER(
                ALL(Dim_Product),
                [Product Performance Score] =
                MAXX(
                    ALL(Dim_Product),
                    [Product Performance Score])
            )
    )
```
