# Medicare Inpatient Cost Analysis - Model Enhancement Guide

## Overview
Your Power BI model has been enhanced with **5 new dimension tables** that provide comprehensive filtering and analysis capabilities for your Medicare inpatient cost data.

---

## New Dimension Tables Created

### 1. **DRG Medical Categories**
**Purpose:** Provides detailed medical classification for all Diagnosis Related Groups

**Key Columns:**
- **DRG Code** - Three-digit DRG identifier (e.g., "003", "039", "194")
- **Medical Specialty** - Primary medical department (Examples: Cardiology, Orthopedics, Neurosurgery, Pulmonology)
- **Body System** - Affected body system (Examples: Circulatory System, Respiratory System, Musculoskeletal System)
- **Severity Level** - Clinical severity (Minor, Moderate, Major, Severe)
- **Procedure Type** - Medical vs Surgical classification
- **Cost Tier** - Expected cost category (Low, Medium, High, Very High)

**Analysis Use Cases:**
- Compare costs across medical specialties
- Analyze severity patterns by provider or region
- Identify high-cost procedure concentrations
- Track surgical vs medical procedure mix

**Sample Specialties Included:**
- Cardiology & Cardiac Surgery (DRGs 216-330)
- Orthopedic Surgery (DRGs 432-482)
- Pulmonology & Respiratory (DRGs 189-208)
- Gastroenterology & Digestive (DRGs 177-392)
- Neurology & Neurosurgery (DRGs 003-074)
- Urology & Nephrology (DRGs 552-743)
- Obstetrics (DRGs 683-775)
- Critical Care (DRGs 945-947)

---

### 2. **Cost Efficiency Categories**
**Purpose:** Classifies payment efficiency based on the ratio of Medicare payments to covered charges

**Key Columns:**
- **Efficiency Category ID** - Numeric identifier (1-5)
- **Efficiency Category** - Descriptive name
- **Payment to Charge Ratio Range** - Percentage bands
- **Efficiency Level** - Performance rating
- **Description** - Detailed explanation

**Categories:**
1. **Very Efficient** - <20% ratio (Medicare pays less than 20% of billed charges)
2. **Efficient** - 20-30% ratio
3. **Average Efficiency** - 30-40% ratio
4. **Below Average** - 40-50% ratio
5. **Low Efficiency** - >50% ratio

**Analysis Use Cases:**
- Identify most cost-effective providers
- Benchmark payment efficiency across regions
- Detect outliers in billing practices
- Compare efficiency by DRG or specialty

---

### 3. **Service Intensity Levels**
**Purpose:** Categorizes procedures by payment per discharge amount

**Key Columns:**
- **Intensity Level ID** - Numeric identifier (1-6)
- **Intensity Level** - Descriptive category
- **Payment Per Discharge Range** - Dollar amount ranges
- **Typical Procedures** - Examples of procedures in each tier

**Categories:**
1. **Low Intensity** - <$5,000 (Routine care, short stays)
2. **Medium-Low Intensity** - $5,000-$10,000 (Standard procedures)
3. **Medium Intensity** - $10,000-$20,000 (Significant interventions)
4. **Medium-High Intensity** - $20,000-$40,000 (Complex surgeries, ICU)
5. **High Intensity** - $40,000-$75,000 (Major surgeries, extended ICU)
6. **Very High Intensity** - >$75,000 (Complex cardiac/neuro, transplants)

**Analysis Use Cases:**
- Identify high-intensity service providers
- Track case mix complexity
- Analyze payment patterns by intensity
- Compare intensity levels across markets

---

### 4. **Provider Type Classification**
**Purpose:** Categorizes healthcare facilities by size and specialization

**Key Columns:**
- **Provider Type ID** - Numeric identifier (1-10)
- **Provider Type** - Facility classification
- **Size Category** - Bed count or capacity tier
- **Specialization Level** - General to Highly Specialized
- **Description** - Detailed facility characteristics

**Provider Types:**
1. Small Community Hospital (<100 beds, general services)
2. Medium Community Hospital (100-300 beds, diverse services)
3. Large Regional Hospital (300-500 beds, multiple specialties)
4. Major Medical Center (500+ beds, tertiary care)
5. Academic Medical Center (Teaching hospital, research)
6. Specialty Hospital - Cardiac
7. Specialty Hospital - Orthopedic
8. Specialty Hospital - Surgical
9. Critical Access Hospital (Rural, <25 beds)
10. Rehabilitation Hospital (Post-acute care)

**Analysis Use Cases:**
- Compare performance by facility type
- Analyze specialty hospital efficiency
- Track academic vs community hospital metrics
- Identify critical access hospital challenges

---

### 5. **Healthcare Market Segments**
**Purpose:** Segments markets by size and competitive dynamics

**Key Columns:**
- **Market Segment ID** - Numeric identifier (1-5)
- **Market Segment** - Market classification
- **Market Size** - Size category
- **Competition Level** - Competitive intensity
- **Description** - Market characteristics

**Market Segments:**
1. **Major Metro Markets** - Top 20 metros, high competition
2. **Mid-Size Metro Markets** - Medium metros, 2-5 major providers
3. **Small Metro Markets** - Smaller cities, 1-3 major providers
4. **Regional Centers** - Regional hubs serving rural areas
5. **Rural Markets** - Rural and frontier areas

**Analysis Use Cases:**
- Compare urban vs rural healthcare patterns
- Analyze competitive market dynamics
- Identify underserved market opportunities
- Track regional cost variations

---

## New Calculated Columns in Fact Table

The following columns were added to **Medicare Inpatient 2017** to support the new dimensions:

1. **DRG Code** - Extracted first 3 characters from DRG Definition
2. **Payment to Charge Ratio** - Total Payments / Covered Charges
3. **Payment Per Discharge** - Total Payments / Total Discharges
4. **Efficiency Category ID** - Links to Cost Efficiency Categories
5. **Intensity Level ID** - Links to Service Intensity Levels

---

## Relationships Created

All dimension tables are connected to your fact table via one-to-many relationships:

- `Medicare Inpatient 2017[DRG Code]` → `DRG Medical Categories[DRG Code]`
- `Medicare Inpatient 2017[Efficiency Category ID]` → `Cost Efficiency Categories[Efficiency Category ID]`
- `Medicare Inpatient 2017[Intensity Level ID]` → `Service Intensity Levels[Intensity Level ID]`

---

## New Measures Created

Four analytical measures were added to enhance your analysis:

1. **Payment Efficiency Ratio** - Shows what percentage of covered charges Medicare actually pays
2. **Medicare Coverage Percentage** - Shows Medicare's share of total payments
3. **Unique Providers Count** - Counts distinct providers in filtered data
4. **Unique DRGs Count** - Counts distinct DRG codes in filtered data

---

## How to Use These New Filters

### Scenario 1: Analyzing High-Cost Cardiac Procedures
**Filters to Apply:**
- DRG Medical Categories[Medical Specialty] = "Cardiac Surgery"
- Service Intensity Levels[Intensity Level] = "High Intensity" or "Very High Intensity"

**Insights:** Identify which providers specialize in complex cardiac care and their cost efficiency

### Scenario 2: Finding Efficient Orthopedic Providers
**Filters to Apply:**
- DRG Medical Categories[Medical Specialty] = "Orthopedic Surgery"
- Cost Efficiency Categories[Efficiency Level] = "Excellent" or "Good"

**Insights:** Discover most cost-effective orthopedic care providers

### Scenario 3: Rural Healthcare Access Analysis
**Filters to Apply:**
- Healthcare Market Segments[Market Segment] = "Rural Markets"
- Provider Type Classification[Provider Type] = "Critical Access Hospital"

**Insights:** Understand rural healthcare challenges and costs

### Scenario 4: Severity-Based Analysis
**Filters to Apply:**
- DRG Medical Categories[Severity Level] = "Severe" or "Major"
- DRG Medical Categories[Body System] = [Select specific system]

**Insights:** Analyze outcomes and costs for severe cases by body system

### Scenario 5: Specialty Hospital Performance
**Filters to Apply:**
- Provider Type Classification[Specialization Level] = "Highly Specialized"
- DRG Medical Categories[Procedure Type] = "Surgical"

**Insights:** Compare specialty hospital efficiency vs general hospitals

---

## Recommended Visualizations

### 1. **Medical Specialty Dashboard**
- **Matrix:** Medical Specialty × Cost Tier with Total Payments
- **Bar Chart:** Total Discharges by Medical Specialty
- **Scatter Plot:** Payment per Discharge vs Efficiency Ratio by Specialty

### 2. **Provider Performance Dashboard**
- **Table:** Provider Name with Efficiency Category, Intensity Level, Total Payments
- **KPI Cards:** Avg Payment per Discharge, Payment Efficiency Ratio, Total Discharges
- **Map:** Geographic distribution colored by Efficiency Category

### 3. **DRG Analysis Dashboard**
- **Treemap:** DRG Categories by Total Payments (size) and Efficiency (color)
- **Column Chart:** Top 10 DRGs by Payment Volume
- **Table:** DRG Details with Severity, Cost Tier, Body System

### 4. **Market Analysis Dashboard**
- **Donut Chart:** Market Segment distribution by Total Payments
- **Line Chart:** Payment Efficiency Trend by Market Segment
- **Column Chart:** Provider Type distribution by Market Segment

---

## Advanced Analysis Tips

### Creating Custom Groupings
You can create additional groupings by combining dimensions:
- High-Cost Specialties = Cardiac Surgery + Neurosurgery + Orthopedic Surgery
- Routine Care = Low + Medium-Low Intensity Levels
- Complex Care = High + Very High Intensity Levels

### Comparative Analysis
Use these dimensions to create insightful comparisons:
- Academic Medical Centers vs Community Hospitals for cardiac care
- Surgical vs Medical procedures by efficiency
- Urban vs Rural market cost patterns
- Severity levels across different specialties

### Outlier Detection
Identify unusual patterns:
- Providers with Very High Intensity but Low Efficiency
- DRGs with significantly higher costs in certain regions
- Specialties with unexpected payment-to-charge ratios

---

## Next Steps for Model Enhancement

Consider these additional enhancements:

1. **Time Intelligence**
   - Add a Date table for year-over-year analysis
   - Create fiscal year and quarter calculations

2. **Provider Performance Metrics**
   - Calculate provider market share by specialty
   - Create benchmarking measures (vs state/national average)

3. **Geographic Enhancements**
   - Add state/region hierarchies
   - Create metropolitan statistical area (MSA) groupings

4. **Patient Outcome Metrics**
   - If available, add readmission rates
   - Include patient satisfaction scores

5. **Cost Variance Analysis**
   - Calculate standard deviations
   - Create upper/lower control limits for outlier detection

---

## Support and Questions

This enhanced model provides a comprehensive framework for analyzing Medicare inpatient costs across multiple dimensions. The combination of medical, financial, operational, and market perspectives enables deep insights into healthcare cost patterns and provider performance.

**Model Structure:**
- 6 Original + 5 New Dimension Tables = 11 Total Tables
- Multiple hierarchies for drill-down analysis
- Pre-calculated efficiency and intensity metrics
- Comprehensive medical specialty classifications

**Ready for Analysis:** All tables and relationships are configured and ready to use in your reports!
