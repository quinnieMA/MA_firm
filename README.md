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

# Financial Data Processing and Ratio Calculation - Stata Workflow

## 📊 Workflow Overview
This Stata do-file processes cleaned financial data files, merges them, performs forward-filling of deal numbers, calculates financial ratios for target and acquirer companies, creates logarithmic transformations, and generates separate datasets for target and acquirer financial metrics.

---

## 🔧 Main Processing Steps

### 1. **Program Definition: `process_file`**

| Step | Operation | Description |
|------|-----------|-------------|
| 1.1 | Import CSV | Reads CSV file with `bindquote(strict)` option |
| 1.2 | Convert to String | Converts key identifier variables to string format: `ïunnamed__0`, `deal_num`, entity ID variables |
| 1.3 | Replace Missing Indicators | Replaces "n.a.", "n.s.", "-" with empty strings for all financial variables |
| 1.4 | Save as DTA | Saves processed file as Stata `.dta` format |

### 2. **Batch Processing (Files 1-4)**

| File | Input CSV | Observations | Output DTA |
|------|-----------|--------------|------------|
| File 1 | `acquisition_financial_1_cleaned.csv` | 29,259 | `acq_fin_1.dta` |
| File 2 | `acquisition_financial_2_cleaned.csv` | 22,709 | `acq_fin_2.dta` |
| File 3 | `acquisition_financial_3_cleaned.csv` | 19,202 | `acq_fin_3.dta` |
| File 4 | `acquisition_financial_4_cleaned.csv` | 1,276 | `acq_fin_4.dta` |
| **Total** | | **72,446** | |

### 3. **Data Append and Cleaning**

```stata
// Append all files
forvalues i = 1/4 {
    append using "acq_fin_`i'.dta"
}

// Forward-fill deal_num
sort index
replace deal_num = deal_num[_n-1] if deal_num == "" & deal_num[_n-1] != ""
// 31,420 changes made

// Keep unique records by entity combination
bysort deal_num tar_name tar_bvd_id_num tar_orbis_id_num acq_name acq_bvd_id_num acq_orbis_id_num ven_name ven_bvd_id_num ven_orbis_id_num: keep if _n == 1
// 67 observations deleted
Outliers: Extreme values present (millions) - may need winsorization

Observation Counts: Final deduplicated file has fewer observations than raw sum

Log Transformations: Only calculated for positive values

Relative Metrics: Limited observations due to requiring both entities
### 📈 Financial Ratio Calculations

#### Target Company Ratios (Pre-deal)
| Ratio | Formula | Observations |
|-------|---------|--------------|
| Profit Margin | PAT / Revenue | 29,132 |
| Return on Assets (ROA) | EBITDA / Total Assets | 17,719 |
| EBITDA Margin | EBITDA / Revenue | 17,688 |
| Leverage Ratio | (Total Assets - Net Assets) / Total Assets | 3,584 |
| Debt-to-Equity | (Total Assets - Net Assets) / Net Assets | 3,371 |
| Asset Turnover | Revenue / Total Assets | 33,943 |
| Return on Equity | PAT / Net Assets | 3,341 |
| Log(Total Assets) | ln(Total Assets) | 34,460 |
| Log(Market Cap) | ln(Market Capitalization) | 4,822 |
| EV/EBITDA | Market Cap / EBITDA | 3,396 |
| P/E Ratio | Market Cap / PAT | 3,578 |

#### Acquirer Company Ratios (Pre-deal)
| Ratio | Formula | Observations |
|-------|---------|--------------|
| Profit Margin | PAT / Revenue | 31,033 |
| Return on Assets (ROA) | EBITDA / Total Assets | 21,597 |
| EBITDA Margin | EBITDA / Revenue | 21,251 |
| Leverage Ratio | (Total Assets - Net Assets) / Total Assets | 11,547 |
| Debt-to-Equity | (Total Assets - Net Assets) / Net Assets | 11,323 |
| Asset Turnover | Revenue / Total Assets | 34,321 |
| Return on Equity | PAT / Net Assets | 11,321 |
| Log(Total Assets) | ln(Total Assets) | 35,410 |
| Log(Market Cap) | ln(Market Capitalization) | 18,506 |

#### Post-deal Ratios
| Entity | Ratio | Formula |
|--------|-------|---------|
| Target | Profit Margin | PAT / Revenue |
| Target | ROA | EBITDA / Total Assets |
| Target | Asset Turnover | Revenue / Total Assets |
| Acquirer | Profit Margin | PAT / Revenue |
| Acquirer | ROA | EBITDA / Total Assets |
| Acquirer | Asset Turnover | Revenue / Total Assets |

#### Relative Performance Metrics
| Metric | Formula | Observations |
|--------|---------|--------------|
| Relative Profit Margin | Acquirer Margin - Target Margin | 22,455 |
| Relative ROA | Acquirer ROA - Target ROA | 13,116 |
| Relative Size | Acquirer Log(Assets) - Target Log(Assets) | 28,880 |
| Relative Leverage | Acquirer Leverage - Target Leverage | 2,085 |

#### Performance Change Metrics
| Metric | Formula | Observations |
|--------|---------|--------------|
| Target ROA Change | Post ROA - Pre ROA | 11,381 |
| Acquirer ROA Change | Post ROA - Pre ROA | 18,506 |
| Target Profit Margin Change | Post Margin - Pre Margin | 17,795 |
| Acquirer Profit Margin Change | Post Margin - Pre Margin | 23,011 |

### 📊 Summary Statistics

#### Target Pre-deal Ratios
| Variable | Obs | Mean | Std Dev | Min | Max |
|----------|-----|------|---------|-----|-----|
| Profit Margin | 29,132 | 2,029.7 | 950,396 | -78.1M | 142M |
| ROA | 17,719 | 81.03 | 10,667 | -44,973 | 1.42M |
| Leverage Ratio | 3,584 | 0.608 | 1.530 | -1.063 | 67.46 |
| EBITDA Margin | 17,688 | -93.09 | 14,143 | -1.49M | 1.04M |
| Asset Turnover | 33,943 | 1,214 | 215,292 | -0.795 | 39.7M |

#### Acquirer Pre-deal Ratios
| Variable | Obs | Mean | Std Dev | Min | Max |
|----------|-----|------|---------|-----|-----|
| Profit Margin | 31,033 | -31,355 | 5.19M | -664M | 261M |
| ROA | 21,597 | 166.8 | 12,837 | -36,259 | 942,782 |
| Leverage Ratio | 11,547 | 0.628 | 11.13 | 0.0004 | 1,191 |
| EBITDA Margin | 21,251 | -387.6 | 25,452 | -2.49M | 89,535 |
| Asset Turnover | 34,321 | 116.4 | 10,260 | -0.197 | 947,555 |

#### Relative Metrics
| Variable | Obs | Mean | Std Dev | Min | Max |
|----------|-----|------|---------|-----|-----|
| Relative Profit Margin | 22,455 | -57,425 | 6.53M | -714M | 78.1M |
| Relative ROA | 13,116 | 177.0 | 20,604 | -1.42M | 942,782 |
| Relative Size | 28,880 | 2.237 | 2.538 | -20.32 | 23.78 |
| Relative Leverage | 2,085 | 0.564 | 26.10 | -7.029 | 1,191 |

#### Change Metrics
| Variable | Obs | Mean | Std Dev | Min | Max |
|----------|-----|------|---------|-----|-----|
| Target ROA Change | 11,381 | -4.103 | 1,076 | -104,943 | 44,973 |
| Acquirer ROA Change | 18,506 | -19.30 | 1,904 | -183,061 | 8,457 |
| Target Margin Change | 17,795 | 347.1 | 48,832 | -3.48M | 4.35M |
| Acquirer Margin Change | 23,011 | -4,674 | 981,342 | -146M | 27.4M |

### 📁 Output Files
| File | Description | Variables |
|------|-------------|-----------|
| acq_fin_all.dta | Master file with all variables | All original + calculated ratios |
| tar_fin.dta | Target-only financial data | Deal num, target identifiers, target ratios |
| acq_fin.dta | Acquirer-only financial data | Deal num, acquirer identifiers, acquirer ratios |
| acq_fin_with_ln.dta | Master file with log transformations | Includes 60+ ln_* variables |
