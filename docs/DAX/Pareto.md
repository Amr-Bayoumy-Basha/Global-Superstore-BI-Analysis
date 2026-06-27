# Advanced DAX Documentation — Dynamic Pareto Analysis

This documentation preserves the original technical logic and explains the analytical purpose of each component.

## 1) `X_axis` (Calculated Table)
Used as a disconnected continuous axis for plotting the Pareto curve.

```dax
X_axis = 
SELECTCOLUMNS(
    GENERATESERIES(1, COUNTROWS(Dim_Customer)),
    "X", [Value]
)
```

## 2) `Metric` (Calculated Table)
Provides user-selectable metric options for dynamic Pareto evaluation.

```dax
Metric = 
SELECTCOLUMNS(
    {
        "Total Sales",
        "Total Profit",
        "Total Cost",
        "Total Orders"
    },
    "Measure", [Value]
)
```

## 3) `Metric Value` (Measure)
Returns selected measure value dynamically.

```dax
Metric Value = 
SWITCH(
    SELECTEDVALUE(Metric[Measure]),
    "Total Sales", [Total Sales],
    "Total Profit", [Total Profit],
    "Total Cost", [Total Cost],
    "Total Orders", [Total Orders],
    ERROR("Measure Not Selected In Metric[Measure]")
)
```

## 4) `Pareto Cumulative` (Measure)
Calculates cumulative contribution ordered by descending metric value using `WINDOW`.

```dax
Pareto Cumulative = 
VAR X_Pos = SELECTEDVALUE(X_axis[X]) 
VAR Points =
    ADDCOLUMNS(
        VALUES(Dim_Customer[Customer ID]),
        "@Value", [Metric Value]
    )
VAR Cumulated_Points =
    WINDOW(
        1, ABS,
        X_Pos,ABS,
        Points,
        ORDERBY([@Value], DESC, Dim_Customer[Customer ID],ASC)
    )
VAR Result = 
    SUMX(
        Cumulated_Points,
        [@Value]
    )
RETURN
    Result
```

## 5) `Pareto Amount` (Measure)
Alternative point-specific amount retrieval at current x-position.

```dax
Pareto Amount = 
VAR X_Pos = SELECTEDVALUE(X_axis[X])
VAR Points =
    ADDCOLUMNS(
        VALUES(Dim_Customer[Customer ID]),
        "@Value", [Metric Value]
    )
VAR Cumulated_Points =
    WINDOW(
        X_Pos, ABS,
        X_Pos,ABS,
        Points,
        ORDERBY([@Value], DESC, Dim_Customer[Customer ID],ASC)
    )
VAR Result = 
    SUMX(
        Cumulated_Points,
        [@Value]
    )
RETURN
    Result
```

## 6) `Customer Name` (Measure)
Returns customer at selected Pareto rank, useful for tooltips/cards.

```dax
Customer Name = 
VAR X_Pos = SELECTEDVALUE(X_axis[X]) 
VAR Points =
    ADDCOLUMNS(
        ALLSELECTED(Dim_Customer[Customer ID], Dim_Customer[Customer Name]),
        "@Value", [Metric Value]
    )
VAR Cumulated_Points =
    WINDOW(
        X_Pos, ABS,
        X_Pos,ABS,
        Points,
        ORDERBY([@Value], DESC, Dim_Customer[Customer ID],ASC)
    )
VAR Result = 
    SELECTCOLUMNS(
        Cumulated_Points,
        Dim_Customer[Customer Name]
    )
RETURN
    Result
```

## 7) `Pareto %` (Measure)
Computes cumulative share of selected metric.

```dax
Pareto % = 
DIVIDE(
    [Pareto Cumulative],
    CALCULATE(
        [Metric Value],
        ALLSELECTED(
            Dim_Customer)
))
```

## 8) `What If Pareto %` (Measure)
Used to mark threshold point for What-If scenario analysis.

```dax
What If Pareto % = 
VAR Threshold = SELECTEDVALUE('What If Pareto'[What If Pareto])
VAR ParetoPercent = 
    DIVIDE(
        [Pareto Cumulative],
        CALCULATE(
            [Metric Value],
            ALL(Dim_Customer)
        )
    )
RETURN
    IF(
        ABS(ParetoPercent - Threshold) <0.01, ParetoPercent, BLANK()
    )
```
