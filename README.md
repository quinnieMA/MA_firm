# Company Financial Dataset - Processing Pipeline & Variable Definition

## 1. Data Volume Change Flowchart
The flowchart above illustrates the full processing pipeline for the Company Financial dataset, with key data volume changes at each step.

## 2. Key Data Volume Summary
| Dataset Stage               | Initial Obs | Action                                  | Removed Obs | Final Obs |
|-----------------------------|-------------|-----------------------------------------|-------------|-----------|
| Raw CSV (1-8)               | -           | Aggregation                             | -           | 75,841    |
| Merged Dataset              | 75,841      | Forward fill deal_num (32,249 missing)  | 0           | 75,841    |
| Post-Deduplication (Company ID) | 75,841  | Remove duplicates                       | 3,462       | 72,379    |
| acq_tar_fin.dta (Target)    | 72,379      | Deduplicate by deal_num + target ID     | 19,877      | 52,502    |
| acq_acq_fin.dta (Acquirer)  | 72,379      | Deduplicate by deal_num + acquirer ID   | 18,281      | 54,098    |
| acq_com_fin_with_ln.dta     | 72,379      | Add 144 log variables                   | 0           | 72,379    |

## 3. Financial Ratio Generation
### 3.1 Target Company Ratios (21 Total: 7 Categories × 3 Time Points)
| Ratio Category          | Latest Year (ly) | Y1 (1 Year Prior) | Y2 (2 Years Prior) |
|-------------------------|------------------|-------------------|--------------------|
| Profit Margin           | ✓                | ✓                 | ✓                  |
| Return on Assets (ROA)  | ✓                | ✓                 | ✓                  |
| Leverage                | ✓                | ✓                 | ✓                  |
| EBITDA Margin           | ✓                | ✓                 | ✓                  |
| Asset Turnover          | ✓                | ✓                 | ✓                  |
| Revenue per Employee    | ✓                | ✓                 | ✓                  |
| Size Metrics (ln)       | ✓                | ✓                 | ✓                  |

### 3.2 Acquirer Company Ratios (18 Total: 6 Categories × 3 Time Points)
| Ratio Category          | Latest Year (ly) | Y1 (1 Year Prior) | Y2 (2 Years Prior) |
|-------------------------|------------------|-------------------|--------------------|
| Profit Margin           | ✓                | ✓                 | ✓                  |
| Return on Assets (ROA)  | ✓                | ✓                 | ✓                  |
| Leverage                | ✓                | ✓                 | ✓                  |
| EBITDA Margin           | ✓                | ✓                 | ✓                  |
| Asset Turnover          | ✓                | ✓                 | ✓                  |
| Size Metrics (ln)       | ✓                | ✓                 | ✓                  |

## 4. Log Variable Definition (144 Total)
### 4.1 Naming Convention
| Component | Description                  | Examples          |
|-----------|------------------------------|-------------------|
| Prefix    | Log transformation           | `ln_`             |
| Entity    | Company type                 | `t`(Target), `a`(Acquirer), `v`(Vendor) |
| Metric    | Financial indicator          | `rev`(Revenue), `ebd`(EBITDA), `ta`(Total Assets), `cap`(Market Cap), `emp`(Employees) |
| Time      | Time point (0=Latest, 1=Y1, 2=Y2) | `0`, `1`, `2`    |

### 4.2 Variable Count Breakdown
| Entity       | Metric Categories | Time Points | Total Variables |
|--------------|-------------------|-------------|-----------------|
| Target       | 16                | 3           | 48              |
| Acquirer     | 16                | 3           | 48              |
| Vendor       | 16                | 3           | 48              |
| **Total**    | **48**            | -           | **144**         |

### 4.3 Examples
| Variable Name | Definition                                  |
|---------------|---------------------------------------------|
| `ln_t_rev_0`  | Log of Target's latest year revenue         |
| `ln_a_cap_1`  | Log of Acquirer's market cap (1 year prior) |
| `ln_v_ebd_2`  | Log of Vendor's EBITDA (2 years prior)      |

## 5. Key Observations
1. **Raw Data**: 75,841 observations from 8 CSV files, with significant volume variation across batches (3,395 to 14,498 obs per file).
2. **Missing Values**: 42.5% of `deal_num` values (32,249) were missing and filled via forward fill.
3. **Duplication**: 9.11% of observations (6,908) had duplicates; 3,462 were removed to retain 72,379 unique records.
4. **Entity-Level Deduplication**:
   - Target: 30.2% reduction (72,379 → 52,502) to retain 1 record per deal-target pair
   - Acquirer: 25.3% reduction (72,379 → 54,098) to retain 1 record per deal-acquirer pair
5. **Variable Generation**: 39 financial ratios (21 Target + 18 Acquirer) and 144 log-transformed variables for regression analysis.
6. **Data Quality**: Severe missing values (30k-70k per financial metric) reflect incomplete raw financial data.

## 6. Core Use Cases
This dataset provides comprehensive financial data for Target/Acquirer/Vendor companies, including:
- Raw financial metrics (Revenue, EBITDA, Total Assets, etc.)
- Calculated financial ratios (Profitability, Efficiency, Leverage)
- Log-transformed size metrics
- Used for financial performance analysis, M&A research, and regression modeling (as control variables).
