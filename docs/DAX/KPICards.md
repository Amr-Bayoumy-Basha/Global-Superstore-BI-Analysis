# Advanced DAX Documentation — KPI Cards & Conditional Formatting

## 1) `Total Sales Growth %` (Measure)
Compares current total sales to prior-year equivalent.

```dax
Total Sales Growth % = 
VAR TotaLSalesPY =
    CALCULATE(
        [Total Sales],
        PREVIOUSYEAR(
            Dim_Date[Date])
    )

VAR TotalSalesGrowth = 
    [Total Sales] - TotaLSalesPY

VAR TotalSalesGrowthh =
    DIVIDE(
        TotalSalesGrowth,
        TotaLSalesPY
    )
RETURN
    TotalSalesGrowthh
```

## 2) `Total Sales Growth With Arrow` (Measure)
Human-readable KPI display with directional indicator.

```dax
Total Sales Growth With Arrow = 
    IF(
        ISBLANK([Total Sales Growth %]),
        BLANK(),
        IF(
            [Total Sales Growth %] >=0,
            "+" & ROUND([Total Sales Growth %] * 100,1) & "% ↑",
            ROUND([Total Sales Growth %] * 100,1) & "% ↓"
        )
    )
```

## 3) `Total Sales Growth % Colors` (Measure)
Color label output for conditional formatting.

```dax
Total Sales Growth % Colors = 
    IF(
        [Total Sales Growth %] > 0,
        "Green",
        "Red"
    )
```

## Methodology Note

These card measures are part of a broader pattern used throughout the report for dynamic KPI communication, trend signaling, and executive-friendly interpretation.
