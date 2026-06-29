# Data Model

## Star Schema Design

The project uses a Star Schema to separate transactional events from descriptive attributes.



## Fact Table

### `Fact_Sales`
Primary transactional table containing:
- order-level sales and profit measures,
- quantity,
- order date / ship date keys,
- product/customer/geography references,
- return-status analytical linkage.

## Dimension Tables

### `Dim_Date`
- Date hierarchy, calendar attributes, and support fields for time intelligence.

### `Dim_Customer`
- Customer identity and segmentation fields.
- Supports CLV, churn, cohort, and class analysis.

### `Dim_Product`
- Product/category-level attributes.
- Supports weighted ranking, contribution, and return analysis.

### `Dim_Geography`
- Country/region-level slicing for geographic performance comparisons.

### Supporting Dimensions
- Return status and other helper dimensions used for advanced filtering and interpretation.

## Modeling Principles Applied

- Single-direction filtering where practical for predictable behavior.
- Measure-first analytical approach (logic centralized in DAX measures).
- Reduced redundancy by using dimensions for categorical attributes.
- Separation of base metrics and derived business metrics.

## Performance and Maintainability Notes

- Star Schema improves scan efficiency versus flat denormalized models.
- Dimension conformance increases measure reusability.
- Explicit modeling improves debugging and onboarding for reviewers.
