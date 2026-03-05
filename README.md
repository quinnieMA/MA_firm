### Color Coding Legend
| Color Symbol | Type               | Purpose                                                                 |
|--------------|--------------------|-------------------------------------------------------------------------|
| 🔴 Red       | Primary Key        | Marks the unique identifier (`deal_num`) that uniquely identifies each deal |
| 🟢 Green     | Deduplication Key  | Marks fields used to eliminate duplicate records for related entities   |
## MA firm financial information ##
#### **Basic Deal Information**
| Original Pattern | Standardized Output |
|-----------------|---------------------|
| Deal Number | 🔴 `deal_num` |
| Deal type | `deal_type` |
| Deal status | `deal_status` |

#### **Entity Identifiers**
| Original Pattern | Standardized Output |
|-----------------|---------------------|
| Target name | 🟢`tar_name` |
| Target BvD ID number |🟢 `tar_bvd_id_num` |
| Target Orbis ID number | 🟢`tar_orbis_id_num` |
| Acquiror name | 🟢`acq_name` |
| Acquiror BvD ID number |🟢 `acq_bvd_id_num` |
| Acquiror Orbis ID number | 🟢`acq_orbis_id_num` |
| Vendor name |🟢 `ven_name` |
| Vendor BvD ID number |🟢 `ven_bvd_id_num` |
| Vendor Orbis ID number | 🟢`ven_orbis_id_num` |
| Acquiror country code |🟢 `acq_country` |
| Target country code |🟢 `tar_country` |

#### **Deal Value Metrics**
| Original Pattern | Standardized Output |
|-----------------|---------------------|
| Deal value th USD | `DV` |
| Deal value (Native currency) th USD | `DV_local` |
| Deal equity value th USD | `EQV` |
| Deal equity value (Native currency) th USD | `EQV_local` |
| Deal enterprise value th USD | `EV` |
| Deal enterprise value (Native currency) th USD | `EV_local` |
| Deal modelled enterprise value th USD | `MEV` |
| Deal modelled enterprise value (Native currency) th USD | `MEV_local` |
| Deal total target value th USD | `T_tar_value` |
| Deal total target value (Native currency) th USD | `T_tar_value_local` |
| Modelled Fee Income th USD | `M_fee` |
| As Reported Fee Income th USD | `reported_fee` |

### 3. **Financial Abbreviation Dictionary**

| Term Pattern | Abbreviation | Description |
|--------------|--------------|-------------|
| acquiror | `acq` | Acquiror entity |
| target | `tar` | Target entity |
| vendor | `ven` | Vendor entity |
| operating revenue, revenue, turnover | `rev` | Revenue metrics |
| ebitda | `ebitda` | EBITDA |
| ebit | `ebit` | EBIT |
| profit before tax | `pbt` | Profit before tax |
| profit after tax | `pat` | Profit after tax |
| net profit | `np` | Net profit |
| total assets | `ta` | Total assets |
| net assets | `na` | Net assets |
| shareholders funds | `eq` | Shareholders equity |
| market capitalisation | `cap` | Market cap |
| number of employees | `emp` | Employee count |
| enterprise value | `ev` | Enterprise value |
| earnings per share | `eps` | EPS |
| cash flow per share | `cfps` | CFPS |
| dividend per share | `dps` | DPS |
| book value per share | `bvps` | BVPS |
| last avail yr | `ly` | Last available year |
| year 1 | `y1` | Year 1 |
| year 2 | `y2` | Year 2 |
| first | `1st` | First available |
| future | `fut` | Future estimates |
| multiple | `mul` | Multiple values |
| estimate | `est` | Estimates |
| year | `yr` | Year indicator |

### 4. **Prefix Handling**
| Pattern | Standardized |
|---------|--------------|
| pre_deal_ | `pre_` |
| post_deal_ | `post_` |

### 5. **Entity Type Normalization**
| Pattern | Standardized |
|---------|--------------|
| _target_ | `_tar_` |
| _acquiror_ | `_acq_` |
| _vendor_ | `_ven_` |
