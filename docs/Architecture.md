# Project Architecture

## Architecture Overview

This project follows a layered BI architecture designed for reliability, scalability, and business interpretability.

```text
Raw Dataset (Excel)
   ↓
Power Query (Data Preparation)
   ↓
Star Schema (Data Modeling)
   ↓
DAX Semantic Model (Business Logic)
   ↓
Interactive Dashboard (Power BI)
   ↓
Insights & Recommendations (Decision Layer)
```

## Layer-by-Layer Detail

### 1) Raw Dataset
- Source: Global Superstore transactional dataset.
- Includes orders, customer attributes, products, geography, shipping, and return status.

### 2) Power Query Layer
- Data cleansing and standardization.
- Data type enforcement.
- Business-ready transformations for consistent modeling.
- Preparation of fields required for segmentation and analytical logic.

### 3) Modeling Layer (Star Schema)
- Central fact table with conformed dimensions.
- Optimized relationship paths for analytical performance.
- Supports reusable measures and cross-filter consistency.

### 4) Semantic Layer (DAX)
- KPI measures for sales, profit, churn, CLV, and returns.
- Time-intelligence logic for YTD/PY and rolling windows.
- Advanced ranking and Pareto patterns for prioritization.

### 5) Visualization Layer
- Multi-page dashboard serving executive, manager, and analyst audiences.
- High-level summary + drill-through detail patterns.
- Metrics aligned to business questions and decisions.

### 6) Recommendation Layer
- Findings translated into strategic options with priorities.
- Action framing based on risk, return, and implementation complexity.

