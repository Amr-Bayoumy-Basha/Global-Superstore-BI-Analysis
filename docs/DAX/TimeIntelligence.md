# Advanced DAX Documentation — Time Intelligence

## 1) `Sales_YTDoPY %` (Measure)
Year-to-date percentage change against prior year period.

```dax
Sales_YTDoPY % = 
VAR Sales_PYC = 
    IF(
        [ShowValueForDates], 
        CALCULATE(
            [Total Sales],
            PARALLELPERIOD(
                Dim_Date[Date],
                -1,
                YEAR
            )
        )
    )
VAR ValueCurrentPeriod = [Sales_YTD]
VAR ValuePreviousPeriod = Sales_PYC
VAR Sales_YTDoPY =
    IF(
        NOT ISBLANK(ValueCurrentPeriod) && NOT ISBLANK(ValuePreviousPeriod),
        ValueCurrentPeriod - ValuePreviousPeriod
    )
RETURN
    DIVIDE(
        Sales_YTDoPY,
        Sales_PYC
    )
```

## 2) `Rolling Avg 3M` (Measure)
Three-month rolling average using filtered period table and data availability safeguards.

```dax
Rolling Avg 3M = 
VAR Period3M = 
    CALCULATETABLE(
        DATESINPERIOD(
            Dim_Date[Date],
            MAX(Dim_Date[Date]),
            -3,
            MONTH
        ),
        Dim_Date[DatesWithSales] = TRUE() 
    )

VAR FirstDayWithData = 
    CALCULATE(
        MIN(Fact_Sales[Order Date]),
        REMOVEFILTERS()
    )

VAR FirstDayInPeriod =
    MINX(
        Period3M,
        Dim_Date[Date]
    )

VAR Result =
    IF(
        FirstDayWithData <= FirstDayInPeriod, 
        AVERAGEX(
            Period3M,
            [Metric Value]
        )
    )

RETURN
    Result
```
