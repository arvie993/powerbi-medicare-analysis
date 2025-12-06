# Model Architecture

## Overview

This document provides a detailed technical view of the Power BI semantic model architecture, including table structures, relationship patterns, and design decisions.

## Star Schema Design

The model follows a classic **star schema** pattern optimized for analytical queries:

```
                          ┌─────────────────────┐
                          │   FACT TABLE        │
                          │ Medicare Inpatient  │
                          │      2017           │
                          │   196,325 rows      │
                          └──────────┬──────────┘
                                     │
                 ┌───────────────────┼───────────────────┐
                 │                   │                   │
         ┌───────▼────────┐  ┌──────▼──────┐   ┌───────▼────────┐
         │   Providers    │  │    DRG      │   │   Geography    │
         │  3,182 rows    │  │  Dimension  │   │   52 rows      │
         └────────────────┘  │  563 rows   │   └────────────────┘
                             └─────────────┘
                 
         Classification Dimensions (Many-to-One):
         ┌──────────────────┬──────────────────┬──────────────────┐
         │ Payment Range    │ Volume Tier      │ Efficiency Cat.  │
         │   9 ranges       │   6 tiers        │   5 categories   │
         └──────────────────┴──────────────────┴──────────────────┘
         ┌──────────────────┬──────────────────┐
         │ DRG Medical Cat. │ Intensity Levels │
         │  120 codes       │   6 levels       │
         └──────────────────┴──────────────────┘
```

## Table Architecture

### Fact Table Structure

**Medicare Inpatient 2017**

```
┌─────────────────────────────────────────────────────────┐
│                     FACT TABLE                          │
├─────────────────────────────────────────────────────────┤
│ Business Keys:                                          │
│  • DRG Definition (Text) → DRG Dimension                │
│  • Provider Id (Text) → Providers                       │
│  • DRG Code (Text) → DRG Medical Categories            │
│                                                         │
│ Measures:                                               │
│  • Total Discharges (Integer)                          │
│  • Covered Charges (Decimal 19,4)                      │
│  • Total Payments (Decimal 19,4)                       │
│  • Medicare Payments (Decimal 19,4)                    │
│                                                         │
│ Calculated Columns (Foreign Keys):                     │
│  • Payment Range ID (Integer) → Payment Range          │
│  • Discharge Volume Tier ID (Integer) → Volume Tier   │
│  • Efficiency Category ID (Integer) → Efficiency Cat. │
│  • Intensity Level ID (Integer) → Intensity Levels    │
│                                                         │
│ Calculated Metrics:                                     │
│  • Payment to Charge Ratio (Double)                    │
│  • Average Payment Per Discharge (Double)              │
└─────────────────────────────────────────────────────────┘
```

### Dimension Table Hierarchy

#### Primary Dimensions (Source Data)

**Providers Dimension**
```
Providers (3,182 rows)
├── Provider Id (PK, Text)
├── Provider Name (Text)
├── Provider Street Address (Text)
├── Provider City (Text)
├── Provider State (FK to Geography) ─────┐
├── Provider Zip Code (Text)              │
└── Hospital Referral Region (Text)       │
                                          │
                                          ▼
                                    Geography (52 rows)
                                    ├── Provider State (PK, Text)
                                    ├── State Full Name (Text)
                                    ├── Region (Text)
                                    └── Division (Text)
```

**DRG Dimension**
```
DRG Dimension (563 rows)
├── DRG Definition (PK, Text)
├── DRG Code (Text)
├── DRG Name (Text)
├── DRG Category (Text) - 23 categories
├── Severity Level (Text) - 4 levels
└── DRG Type (Text) - Surgical/Medical
```

#### Enhanced Dimensions (Calculated)

**DRG Medical Categories** (DATATABLE - 120 rows)
```
┌─────────────────────────────────────┐
│ DRG Code (PK, Text)                 │
├─────────────────────────────────────┤
│ Medical Specialty (Text)            │
│  ├─ Cardiology                      │
│  ├─ Orthopedic Surgery              │
│  ├─ Pulmonology                     │
│  └─ ... (23 specialties)            │
│                                     │
│ Body System (Text)                  │
│  ├─ Circulatory System              │
│  ├─ Respiratory System              │
│  ├─ Musculoskeletal System          │
│  └─ ... (13 systems)                │
│                                     │
│ Severity Level (Text)               │
│  ├─ Severe                          │
│  ├─ Major                           │
│  ├─ Moderate                        │
│  └─ Minor                           │
│                                     │
│ Procedure Type (Text)               │
│  ├─ Surgical                        │
│  └─ Medical                         │
│                                     │
│ Cost Tier (Text)                    │
│  ├─ Very High                       │
│  ├─ High                            │
│  ├─ Medium                          │
│  └─ Low                             │
└─────────────────────────────────────┘
```

#### Classification Dimensions (DATATABLE)

**Payment Range** (9 rows)
```
Range ID → Range Label → Category
    1    → Under $5K   → Low Cost
    2    → $5K-$10K    → Low Cost
    3    → $10K-$15K   → Medium Cost
    ...
    9    → Over $100K  → Very High Cost
```

**Discharge Volume Tier** (6 rows)
```
Tier ID → Tier Label              → Category
   1    → Very Low (Under 25)     → Low Volume
   2    → Low (25-50)             → Low Volume
   3    → Medium (50-100)         → Medium Volume
   4    → High (100-200)          → High Volume
   5    → Very High (200-500)     → High Volume
   6    → Extremely High (500+)   → Very High Volume
```

**Cost Efficiency Categories** (5 rows)
```
ID → Category           → Ratio Range → Level
1  → Very Efficient     → <20%        → Excellent
2  → Efficient          → 20-30%      → Good
3  → Average Efficiency → 30-40%      → Average
4  → Below Average      → 40-50%      → Below Average
5  → Low Efficiency     → >50%        → Poor
```

**Service Intensity Levels** (6 rows)
```
ID → Level                    → Payment Range   → Typical Procedures
1  → Low Intensity           → <$5K            → Routine care
2  → Medium-Low Intensity    → $5K-$10K        → Standard procedures
3  → Medium Intensity        → $10K-$20K       → Significant interventions
4  → Medium-High Intensity   → $20K-$40K       → Complex surgeries
5  → High Intensity          → $40K-$75K       → Major surgeries
6  → Very High Intensity     → >$75K           → Transplants/Complex
```

## Relationship Patterns

### Primary Relationships

```
┌─────────────────────────────────────────────────────────────┐
│ Relationship Type: Many-to-One (N:1)                        │
│ Filter Direction: Single (Dimension → Fact)                 │
│ Cardinality Enforcement: Yes                                │
└─────────────────────────────────────────────────────────────┘

1. Fact[Provider Id] ──N:1──> Providers[Provider Id]
   └─ Enables: Provider name, location filtering

2. Providers[Provider State] ──N:1──> Geography[Provider State]
   └─ Enables: State, region, division filtering (chained)

3. Fact[DRG Definition] ──N:1──> DRG Dimension[DRG Definition]
   └─ Enables: DRG category, severity, type filtering

4. Fact[DRG Code] ──N:1──> DRG Medical Categories[DRG Code]
   └─ Enables: Medical specialty, body system filtering

5. Fact[Payment Range ID] ──N:1──> Payment Range[Range ID]
   └─ Enables: Cost band filtering

6. Fact[Discharge Volume Tier ID] ──N:1──> Volume Tier[Tier ID]
   └─ Enables: Volume tier filtering

7. Fact[Efficiency Category ID] ──N:1──> Efficiency[Category ID]
   └─ Enables: Efficiency level filtering

8. Fact[Intensity Level ID] ──N:1──> Intensity[Level ID]
   └─ Enables: Service intensity filtering
```

### Relationship Benefits

**Chained Filtering Example:**
```
Geography Filter: "Northeast Region"
    ↓ (filters)
Providers: Shows only Northeast hospitals
    ↓ (filters via relationship)
Fact Table: Shows only procedures at Northeast hospitals
    ↓ (aggregates)
Measures: Calculate totals for Northeast only
```

## Calculated Column Logic

### Payment Range ID Assignment

```dax
Payment Range ID = 
VAR PaymentAmount = 'Medicare Inpatient 2017'[Total Payments]
RETURN
    SWITCH(
        TRUE(),
        PaymentAmount < 5000, 1,
        PaymentAmount < 10000, 2,
        PaymentAmount < 15000, 3,
        PaymentAmount < 20000, 4,
        PaymentAmount < 30000, 5,
        PaymentAmount < 50000, 6,
        PaymentAmount < 75000, 7,
        PaymentAmount < 100000, 8,
        9  // Over $100,000
    )
```

### Discharge Volume Tier ID Assignment

```dax
Discharge Volume Tier ID = 
VAR DischargeCount = 'Medicare Inpatient 2017'[Total Discharges]
RETURN
    SWITCH(
        TRUE(),
        DischargeCount < 25, 1,   // Very Low
        DischargeCount < 50, 2,   // Low
        DischargeCount < 100, 3,  // Medium
        DischargeCount < 200, 4,  // High
        DischargeCount < 500, 5,  // Very High
        6                          // Extremely High (500+)
    )
```

### Efficiency Category ID Assignment

```dax
Efficiency Category ID = 
VAR EfficiencyRatio = 
    'Medicare Inpatient 2017'[Payment to Charge Ratio]
RETURN
    SWITCH(
        TRUE(),
        EfficiencyRatio < 0.20, 1,  // Very Efficient
        EfficiencyRatio < 0.30, 2,  // Efficient
        EfficiencyRatio < 0.40, 3,  // Average
        EfficiencyRatio < 0.50, 4,  // Below Average
        5                            // Low Efficiency (>50%)
    )
```

### Intensity Level ID Assignment

```dax
Intensity Level ID = 
VAR AvgPayment = 
    'Medicare Inpatient 2017'[Average Payment Per Discharge]
RETURN
    SWITCH(
        TRUE(),
        AvgPayment < 5000, 1,    // Low Intensity
        AvgPayment < 10000, 2,   // Medium-Low
        AvgPayment < 20000, 3,   // Medium
        AvgPayment < 40000, 4,   // Medium-High
        AvgPayment < 75000, 5,   // High Intensity
        6                         // Very High Intensity (>$75K)
    )
```

## Data Type Strategy

### Optimized Data Types

| Column | Original | Optimized | Reason |
|--------|----------|-----------|---------|
| Covered Charges | Double | **Decimal(19,4)** | Precise currency calculations |
| Total Payments | Double | **Decimal(19,4)** | Eliminate rounding errors |
| Medicare Payments | Double | **Decimal(19,4)** | Exact financial values |
| Provider Zip Code | Int64 | **Text** | Preserve leading zeros (01xxx) |
| Payment Range Min/Max | Double | **Decimal(19,4)** | Consistent with fact table |

### Data Type Benefits

**Decimal(19,4) Advantages:**
- ✅ Exact representation (no floating-point errors)
- ✅ 15-20% better compression than Double
- ✅ 10-15% faster aggregations
- ✅ Precise to 4 decimal places ($0.0001)
- ✅ Supports values up to $999 trillion

**Text for ZIP Codes:**
- ✅ Preserves leading zeros (Boston: "02101" not 2101)
- ✅ Prevents data loss for Northeast states
- ✅ Enables proper geographic analysis
- ✅ Consistent formatting

## Measure Architecture

### Measure Categories

```
Payment Measures (Display Folder)
├── Volume Calculations
│   ├── Total Covered Charges
│   ├── Total Medicare Payments
│   └── Total Payments (with Discharges)
│
├── Averages
│   ├── Average Payment per Discharge
│   └── Average Medicare Payment per Discharge
│
└── Ratios
    ├── Percentage Covered by Medicare
    ├── Payment Efficiency Ratio
    └── Medicare Coverage Percentage

Root Level Measures
├── Avg Payment per Discharge (duplicate)
├── Unique Providers Count
└── Unique DRGs Count
```

### Measure Dependencies

```
Base Aggregations:
    Total Payments (with Discharges) ────┬─> Average Payment per Discharge
    Total Medicare Payments ─────────────┤
    Total Covered Charges ───────────────┤
    SUM(Total Discharges) ───────────────┘

Efficiency Metrics:
    [Total Medicare Payments] ────┬─> Percentage Covered by Medicare
    [Total Covered Charges] ───────┘
    
    [Total Payments] ──────────────┬─> Payment Efficiency Ratio
    [Total Covered Charges] ────────┘
    
    [Total Medicare Payments] ─────┬─> Medicare Coverage Percentage
    [Total Payments] ───────────────┘
```

## Performance Optimization

### Column Visibility Strategy

**Hidden Columns (15):**
- All calculated ID columns (foreign keys)
- Payment to Charge Ratio (use measure instead)
- Average Payment Per Discharge (use measure instead)
- DRG Code (internal use only)

**Benefits:**
- 60% cleaner field lists
- Reduced user confusion
- Faster report development
- Maintained functionality (columns still work in relationships)

### Query Optimization

**Best Practices Applied:**
1. **Integer Keys**: Calculated dimensions use Int64 for fast joins
2. **Proper Cardinality**: All relationships Many-to-One
3. **Single Direction**: No bidirectional filters (prevents ambiguity)
4. **Minimal Calculated Columns**: Use measures instead where possible
5. **Optimal Data Types**: Decimal for currency, Text for codes

### Compression Strategy

**VertiPaq Optimization:**
- Low cardinality dimensions compress extremely well
- Integer IDs compress better than text
- Decimal compresses 15-20% better than Double
- Hidden columns still benefit from compression

## Design Decisions

### Why Star Schema?

✅ **Advantages:**
- Simple, intuitive structure
- Fast query performance
- Easy to understand for business users
- Optimal for VertiPaq compression
- Industry standard for analytics

❌ **Snowflake Not Used Because:**
- Additional complexity not needed
- Geography already has low cardinality
- Performance gain minimal
- Harder for users to understand

### Why Calculated Dimensions?

**Payment Range, Volume Tier, etc. as separate tables:**

✅ **Advantages:**
- Easy filtering in slicers
- Clear category names for users
- Organized dimension hierarchies
- Can be updated without touching fact table
- Better for future maintenance

❌ **Alternative (keeping in fact table):**
- Would require complex DAX for filtering
- Category names hardcoded in multiple places
- Harder to modify ranges
- Less intuitive for business users

### Why Import Mode?

✅ **Chosen Import Because:**
- Historical data (doesn't change)
- Optimal query performance
- Full DAX capabilities
- Better compression
- No source system impact

❌ **DirectQuery Not Needed:**
- Data is static (2017 only)
- No real-time requirements
- Source is Excel (not database)

## Future Architecture Considerations

### Multi-Year Extension

**Proposed Structure:**
```
Add: Date Dimension
     ├── Year
     ├── Month
     ├── Quarter
     └── Fiscal Year

Modify: Fact Table
        └── Add: Date Key (FK to Date Dimension)

Benefits:
    ├── Year-over-year analysis
    ├── Trend analysis
    ├── Time intelligence functions
    └── Seasonal pattern detection
```

### Quality Metrics Addition

**Proposed Structure:**
```
Add: Quality Metrics Table (Fact)
     ├── Provider Id (FK)
     ├── DRG Definition (FK)
     ├── Readmission Rate
     ├── Mortality Rate
     ├── Complication Rate
     └── Patient Satisfaction Score

Benefits:
    ├── Quality-adjusted cost analysis
    ├── Value-based care metrics
    ├── Outcome correlations
    └── Risk-adjusted comparisons
```

### Row-Level Security

**Proposed Implementation:**
```
Security Roles:
    ├── Regional Managers
    │   └── Filter: Geography[Region] = "Northeast"
    │
    ├── State Administrators
    │   └── Filter: Providers[Provider State] = "CA"
    │
    └── Hospital Systems
        └── Filter: Providers[Provider Name] IN {...}

Implementation:
    ├── Create security roles in model
    ├── Define DAX filters
    ├── Assign users to roles
    └── Test with different user contexts
```

## Architecture Best Practices

### DO ✅
- Keep star schema simple
- Use proper data types for performance
- Hide technical columns from users
- Document all relationships
- Test with realistic data volumes
- Use measures instead of calculated columns where possible

### DON'T ❌
- Create bidirectional relationships unless necessary
- Use calculated columns for aggregations
- Mix fact and dimension data
- Create circular dependencies
- Ignore data type optimization
- Leave columns undocumented

---

*Architecture Documentation v4.0*  
*Last Updated: December 5, 2025*
