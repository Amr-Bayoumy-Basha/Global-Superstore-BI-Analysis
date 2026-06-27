# Advanced DAX Documentation — Customer Analytics (Cohort & CLV)

## 1) `FirstOrderYear` (Calculated Column)
Identifies first purchase year by customer for cohort assignment.

```dax
FirstOrderYear = 
CALCULATE(
    MIN(Fact_Sales[Year]),
    ALLEXCEPT(Fact_Sales, Fact_Sales[Customer ID])
)
```

## 2) `Retention Rate` (Measure)
Measures active cohort users over cohort base.

```dax
Retention Rate = 
VAR RetentionRate = 
    DIVIDE(
        [ActiveCustomers],
        CALCULATE(
            [ActiveCustomers],
            ALL(Fact_Sales),
            Fact_Sales[FirstOrderYear] = MIN(Fact_Sales[FirstOrderYear])
        )
    )
RETURN
    IF(
        RetentionRate = 1, BLANK(), RetentionRate
    )
```

## 3) `Exited` (Calculated Column)
Classifies customers as retained vs churned based on multi-year behavior.

```dax
Exited = 
VAR FirstPurchasedYear = 
    CALCULATE(
        MIN(Dim_Date[Year]),
        RELATEDTABLE(Fact_Sales)
    )

VAR LastPurchasedYear = 
    CALCULATE(
        MAX(Dim_Date[Year]),
        RELATEDTABLE(Fact_Sales)
    )

RETURN
    IF(
        LastPurchasedYear > FirstPurchasedYear, 
        "Retained", 
        "Churned"
    )
```

## 4) `Lifetime Churn Rate` (Measure)
Quantifies proportion of customers classified as churned.

```dax
Lifetime Churn Rate = 
DIVIDE(
    [Churned Customers],
    [#Customers]
)
```

## 5) `Customer Lifetime Value (CLV)` (Measure)
Uses APV × purchase frequency × expected lifespan approximation.

```dax
Customer Lifetime Value (CLV) = 
VAR APV = 
    DIVIDE(
        [Total Sales], [Total Orders]
    )

VAR PFR = 
    DIVIDE(
        [Total Orders], [#Customers]
    )

VAR ACV = APV * PFR

VAR ACL = 
    DIVIDE(1, [Lifetime Churn Rate])

VAR CLV = ACV * ACL
RETURN
    CLV
```

## 6) `Avg. Profit Per Customer` (Measure)
Profit productivity KPI at customer level.

```dax
Avg. Profit Per Customer = 
DIVIDE(
    [Total Profit],
    [#Customers]
)
```
