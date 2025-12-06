# DAX Logic Explained - Medicare Inpatient Cost Analysis

## Purpose of This Guide

This document explains the DAX (Data Analysis Expressions) logic behind all calculated fields in the model in plain, understandable terms. Think of it as a "translator" that converts technical formulas into everyday language.

---

## 📊 MEASURES - How They Calculate

### 1. Total Covered Charges

**DAX Formula:**
```dax
SUMX(
    'Medicare Inpatient 2017',
    'Medicare Inpatient 2017'[Covered Charges] * 'Medicare Inpatient 2017'[Total Discharges]
)
```

**What It Does - Step by Step:**

1. **SUMX** = "Sum with Iteration" - walks through each row one at a time
2. **For each row** in the Medicare Inpatient 2017 table:
   - Take the [Covered Charges] value (e.g., $50,000)
   - Multiply by [Total Discharges] (e.g., 25 patients)
   - Result: $50,000 × 25 = $1,250,000 for this row
3. **Add up all the results** from every row
4. **Final answer** = Total of all these multiplications

**Why Not Just SUM([Covered Charges])?**
- The table stores "per discharge" amounts
- We need to multiply by discharge count to get totals
- SUMX handles the multiplication before summing

**Example:**
```
Row 1: $50,000 per discharge × 25 discharges = $1,250,000
Row 2: $30,000 per discharge × 40 discharges = $1,200,000
Row 3: $75,000 per discharge × 15 discharges = $1,125,000
                                      TOTAL = $3,575,000
```

---

### 2. Total Medicare Payments

**DAX Formula:**
```dax
SUMX(
    'Medicare Inpatient 2017',
    'Medicare Inpatient 2017'[Medicare Payments] * 'Medicare Inpatient 2017'[Total Discharges]
)
```

**What It Does - Step by Step:**

Same logic as Total Covered Charges, but uses Medicare Payments instead.

1. Walk through each row
2. Multiply [Medicare Payments] × [Total Discharges]
3. Add up all results

**Why This Is Different from Total Covered Charges:**
- Covered Charges = what hospital billed (high "sticker price")
- Medicare Payments = what Medicare actually paid (much lower)
- The difference shows Medicare's negotiation effectiveness

---

### 3. Total Payments (with Discharges)

**DAX Formula:**
```dax
SUMX(
    'Medicare Inpatient 2017',
    'Medicare Inpatient 2017'[Total Payments] * 'Medicare Inpatient 2017'[Total Discharges]
)
```

**What It Does - Step by Step:**

Same SUMX pattern, using Total Payments (all payers combined).

**Key Distinction:**
- **Medicare Payments** = Medicare's portion only
- **Total Payments** = Medicare + patient copays + other insurance
- Total Payments > Medicare Payments (includes everyone's contribution)

---

### 4. Average Payment per Discharge

**DAX Formula:**
```dax
DIVIDE(
    [Total Payments(with Discharges)],
    SUM('Medicare Inpatient 2017'[Total Discharges])
)
```

**What It Does - Step by Step:**

1. **Numerator (top):** 
   - Calculate [Total Payments(with Discharges)] (uses SUMX as above)
   - This is the total dollar amount across all filtered data

2. **Denominator (bottom):**
   - SUM all [Total Discharges] 
   - This is the total patient count across all filtered data

3. **DIVIDE:**
   - Safely divides numerator by denominator
   - Returns BLANK if denominator is zero (avoids #DIV/0! error)

4. **Result:** Average cost per patient

**Example:**
```
Scenario: Looking at 3 hospitals
Hospital A: $100,000 payments, 10 discharges
Hospital B: $200,000 payments, 20 discharges  
Hospital C: $150,000 payments, 15 discharges

Total Payments: $100K + $200K + $150K = $450,000
Total Discharges: 10 + 20 + 15 = 45
Average: $450,000 ÷ 45 = $10,000 per discharge
```

**Why Use DIVIDE Instead of /**
- **/** gives error if dividing by zero
- **DIVIDE** returns BLANK if zero (safer, cleaner)
- Best practice in DAX

---

### 5. Average Medicare Payment per Discharge

**DAX Formula:**
```dax
DIVIDE(
    [Total Medicare Payments],
    SUM('Medicare Inpatient 2017'[Total Discharges])
)
```

**What It Does - Step by Step:**

Same as "Average Payment per Discharge" but uses Medicare Payments only.

**Result:** Average Medicare cost per patient (excludes copays/other insurance)

---

### 6. Percentage Covered by Medicare

**DAX Formula:**
```dax
DIVIDE(
    [Total Medicare Payments],
    [Total Covered Charges]
)
```

**What It Does - Step by Step:**

1. **Calculate numerator:** Total Medicare Payments (what Medicare paid)
2. **Calculate denominator:** Total Covered Charges (what was billed)
3. **Divide:** Medicare Payments ÷ Covered Charges
4. **Result:** Percentage (decimal - format as %)

**Interpretation:**
- Result of 0.30 = 30%
- Means Medicare paid 30 cents for every dollar billed
- Lower percentage = better Medicare negotiation

**Example:**
```
Hospital bills: $1,000,000 (Covered Charges)
Medicare pays: $300,000 (Medicare Payments)
Percentage: $300,000 ÷ $1,000,000 = 0.30 = 30%

This means 70% discount from billed amount!
```

---

### 7. Payment Efficiency Ratio

**DAX Formula:**
```dax
DIVIDE(
    [Total Payments(with Discharges)],
    [Total Covered Charges]
)
```

**What It Does - Step by Step:**

1. **Calculate numerator:** Total Payments from all sources
2. **Calculate denominator:** Total Covered Charges billed
3. **Divide:** Total Payments ÷ Covered Charges
4. **Result:** Efficiency ratio (lower = more efficient)

**Key Difference from "Percentage Covered by Medicare":**
- This uses TOTAL payments (all payers)
- That one uses only Medicare payments
- This gives complete efficiency picture

---

### 8. Medicare Coverage Percentage

**DAX Formula:**
```dax
DIVIDE(
    [Total Medicare Payments],
    [Total Payments(with Discharges)]
)
```

**What It Does - Step by Step:**

1. **Calculate numerator:** Medicare's payments
2. **Calculate denominator:** All payments (Medicare + others)
3. **Divide:** Medicare ÷ Total
4. **Result:** Medicare's share of total cost

**Example:**
```
Medicare pays: $80,000
Other payers: $20,000 (copays, other insurance)
Total: $100,000

Medicare Coverage: $80,000 ÷ $100,000 = 0.80 = 80%

Medicare bears 80% of cost burden
```

---

### 9. Unique Providers Count

**DAX Formula:**
```dax
DISTINCTCOUNT('Medicare Inpatient 2017'[Provider Id])
```

**What It Does - Step by Step:**

1. **Look at all rows** in current filter context
2. **Find all unique Provider IDs** (ignoring duplicates)
3. **Count them**

**Example:**
```
Row 1: Provider ID = 12345
Row 2: Provider ID = 12345 (duplicate - don't count again)
Row 3: Provider ID = 67890
Row 4: Provider ID = 11111
Row 5: Provider ID = 67890 (duplicate - don't count again)

Result: 3 unique providers (12345, 67890, 11111)
```

---

### 10. Unique DRGs Count

**DAX Formula:**
```dax
DISTINCTCOUNT('Medicare Inpatient 2017'[DRG Definition])
```

**What It Does - Step by Step:**

Same as Unique Providers Count, but counts unique DRG procedures instead.

**Use Case:**
- Shows service diversity
- More DRGs = broader service range
- Helps identify specialty vs general hospitals

---

## 🔢 CALCULATED COLUMNS - How They Work

### 1. DRG Code

**DAX Formula:**
```dax
LEFT('Medicare Inpatient 2017'[DRG Definition], 3)
```

**What It Does - Step by Step:**

1. **Take the DRG Definition column** (e.g., "003 - ECMO OR TRACH W MV >96 HRS")
2. **LEFT function** extracts characters from the left (start)
3. **Extract 3 characters** from the beginning
4. **Result:** "003"

**Why We Need This:**
- DRG Definition is long text (e.g., "003 - ECMO OR TRACH...")
- We just need the 3-digit code for relationships
- Cleaner and more efficient

**Example:**
```
Input: "194 - SIMPLE PNEUMONIA & PLEURISY W CC"
LEFT(input, 3) extracts first 3 characters
Output: "194"
```

---

### 2. Payment to Charge Ratio

**DAX Formula:**
```dax
DIVIDE(
    'Medicare Inpatient 2017'[Total Payments],
    'Medicare Inpatient 2017'[Covered Charges]
)
```

**What It Does - Step by Step:**

1. **For THIS ROW** (row context in calculated column):
   - Get [Total Payments] value
   - Get [Covered Charges] value
2. **Divide:** Payments ÷ Charges
3. **Result:** Ratio for this specific provider-DRG combo
4. **Repeat** for every row in the table

**Key Difference from Measure:**
- **Calculated Column** = stored value per row
- **Measure** = calculated on-the-fly across filtered data
- Column uses more storage but faster for filtering

**Example:**
```
Row 1: 
  Total Payments: $25,000
  Covered Charges: $100,000
  Ratio: $25,000 ÷ $100,000 = 0.25 (25%)

Row 2:
  Total Payments: $15,000
  Covered Charges: $50,000  
  Ratio: $15,000 ÷ $50,000 = 0.30 (30%)
```

---

### 3. Payment Per Discharge

**DAX Formula:**
```dax
DIVIDE(
    'Medicare Inpatient 2017'[Total Payments],
    'Medicare Inpatient 2017'[Total Discharges]
)
```

**What It Does - Step by Step:**

1. **For THIS ROW:**
   - Get [Total Payments] (already per-discharge amount)
   - Get [Total Discharges] count
2. **Divide:** Payments ÷ Discharges
3. **Result:** Average cost per patient for this row

**Wait, Isn't Total Payments Already Per-Discharge?**
- YES! The source data stores per-discharge amounts
- But this makes it explicit and easier to sort/filter
- Also used for categorization into Intensity Levels

---

### 4. Efficiency Category ID

**DAX Formula:**
```dax
VAR Ratio = 'Medicare Inpatient 2017'[Payment to Charge Ratio]
RETURN
    SWITCH(
        TRUE(),
        Ratio < 0.20, 1,  -- Very Efficient
        Ratio < 0.30, 2,  -- Efficient
        Ratio < 0.40, 3,  -- Average
        Ratio < 0.50, 4,  -- Below Average
        5                 -- Low Efficiency
    )
```

**What It Does - Step by Step:**

1. **VAR Ratio =** Store the payment-to-charge ratio in a variable
   - Makes formula easier to read
   - Avoids recalculating same thing multiple times

2. **SWITCH(TRUE(), ...)** = Evaluates conditions top-to-bottom
   - First TRUE condition wins
   - Returns corresponding value

3. **Conditions evaluated:**
   - Is Ratio < 0.20? → Return 1 (Very Efficient)
   - Is Ratio < 0.30? → Return 2 (Efficient)
   - Is Ratio < 0.40? → Return 3 (Average)
   - Is Ratio < 0.50? → Return 4 (Below Average)
   - Otherwise → Return 5 (Low Efficiency)

**Example:**
```
Row 1: Ratio = 0.18 (18%)
  0.18 < 0.20? YES! → Return 1 (Very Efficient)

Row 2: Ratio = 0.25 (25%)
  0.25 < 0.20? NO
  0.25 < 0.30? YES! → Return 2 (Efficient)

Row 3: Ratio = 0.55 (55%)
  0.55 < 0.20? NO
  0.55 < 0.30? NO
  0.55 < 0.40? NO
  0.55 < 0.50? NO
  → Return 5 (Low Efficiency)
```

---

### 5. Intensity Level ID

**DAX Formula:**
```dax
VAR AvgPayment = 'Medicare Inpatient 2017'[Payment Per Discharge]
RETURN
    SWITCH(
        TRUE(),
        AvgPayment < 5000, 1,      -- Low
        AvgPayment < 10000, 2,     -- Medium-Low
        AvgPayment < 20000, 3,     -- Medium
        AvgPayment < 40000, 4,     -- Medium-High
        AvgPayment < 75000, 5,     -- High
        6                          -- Very High
    )
```

**What It Does - Step by Step:**

Same SWITCH(TRUE()) pattern as Efficiency Category ID, but categorizes by payment amount:

1. **Store payment per discharge** in variable
2. **Evaluate conditions** top-to-bottom:
   - < $5,000 → Level 1 (Low Intensity)
   - < $10,000 → Level 2 (Medium-Low)
   - < $20,000 → Level 3 (Medium)
   - < $40,000 → Level 4 (Medium-High)
   - < $75,000 → Level 5 (High)
   - Otherwise → Level 6 (Very High)

**Example:**
```
Row 1: Payment Per Discharge = $3,500
  $3,500 < $5,000? YES! → Return 1 (Low Intensity)

Row 2: Payment Per Discharge = $12,000
  $12,000 < $5,000? NO
  $12,000 < $10,000? NO
  $12,000 < $20,000? YES! → Return 3 (Medium)

Row 3: Payment Per Discharge = $100,000
  All conditions fail → Return 6 (Very High)
```

---

## 🎓 DAX Concepts Explained

### SUMX vs SUM

**SUM:**
- Simple addition of a column
- `SUM([Column])` = Add up all values

**SUMX:**
- Iteration with custom calculation per row
- `SUMX(Table, [Column1] * [Column2])` = Calculate per row, then sum
- More powerful but slightly slower

**When to Use Each:**
- **SUM** when you just need to add a column
- **SUMX** when you need row-by-row calculations first

---

### DIVIDE vs /

**/ (Division Operator):**
```dax
[Total] / [Count]  -- ERROR if Count = 0
```

**DIVIDE Function:**
```dax
DIVIDE([Total], [Count])  -- Returns BLANK if Count = 0
```

**Best Practice:**
- Always use DIVIDE in measures
- Safer (no errors)
- Can specify alternate value: `DIVIDE([Total], [Count], 0)`

---

### VAR and RETURN

**Purpose:** 
- Store intermediate calculations
- Make formulas readable
- Improve performance (calculate once, use many times)

**Syntax:**
```dax
VAR VariableName = [Calculation]
VAR AnotherVariable = [Another Calculation]
RETURN
    [Final Calculation Using Variables]
```

**Example:**
```dax
VAR TotalSales = SUM([Sales])
VAR TotalCost = SUM([Cost])
RETURN
    TotalSales - TotalCost  -- Profit
```

---

### SWITCH(TRUE(), ...)

**Purpose:**
- Evaluate multiple conditions
- Return first match
- Cleaner than nested IFs

**Pattern:**
```dax
SWITCH(
    TRUE(),
    [Condition1], [Value1],
    [Condition2], [Value2],
    [Condition3], [Value3],
    [DefaultValue]
)
```

**Equivalent IF:**
```dax
IF(
    [Condition1], [Value1],
    IF(
        [Condition2], [Value2],
        IF(
            [Condition3], [Value3],
            [DefaultValue]
        )
    )
)
```

SWITCH is much cleaner!

---

### LEFT Function

**Purpose:** Extract characters from left (beginning) of text

**Syntax:**
```dax
LEFT([TextColumn], NumberOfCharacters)
```

**Examples:**
```dax
LEFT("ABCDEF", 3) = "ABC"
LEFT("12345", 2) = "12"
LEFT("Hello World", 5) = "Hello"
```

---

### DISTINCTCOUNT

**Purpose:** Count unique values (ignore duplicates)

**Syntax:**
```dax
DISTINCTCOUNT([Column])
```

**Example:**
```
Column values: A, B, A, C, B, A, D
COUNT = 7 (counts all)
DISTINCTCOUNT = 4 (A, B, C, D - only unique)
```

---

## 💡 Performance Tips

### Calculated Columns vs Measures

**Calculated Columns:**
- ✅ Stored in model (takes space)
- ✅ Calculated once at refresh
- ✅ Can be used in slicers/filters
- ✅ Fast for grouping
- ❌ Takes more memory
- ❌ Recalculates on model refresh

**Measures:**
- ✅ Not stored (saves space)
- ✅ Calculated on-demand
- ✅ Dynamic with filters
- ✅ Better for aggregations
- ❌ Can't use in slicers directly
- ❌ Recalculates on every query

**Rule of Thumb:**
- Use **Measures** for aggregations (SUM, AVERAGE, COUNT)
- Use **Columns** when you need to filter/group by the value

---

### When to Use Variables

**Always Use Variables For:**
- Values used multiple times
- Complex sub-calculations
- Readability

**Example - Bad:**
```dax
DIVIDE(
    SUMX(Table, [Col1] * [Col2]),
    SUMX(Table, [Col1] * [Col2]) + SUMX(Table, [Col3] * [Col4])
)
```

**Example - Good:**
```dax
VAR Numerator = SUMX(Table, [Col1] * [Col2])
VAR Denominator = Numerator + SUMX(Table, [Col3] * [Col4])
RETURN
    DIVIDE(Numerator, Denominator)
```

---

## 🎯 Common Patterns

### Pattern 1: Weighted Average

**Multiply first, then divide:**
```dax
DIVIDE(
    SUMX(Table, [Value] * [Weight]),
    SUM(Table[Weight])
)
```

Used in: Average Payment per Discharge

---

### Pattern 2: Percentage

**Part divided by whole:**
```dax
DIVIDE(
    [Part],
    [Whole]
)
```

Used in: Percentage Covered by Medicare, Payment Efficiency Ratio

---

### Pattern 3: Categorization

**SWITCH with conditions:**
```dax
VAR Value = [Some Calculation]
RETURN
    SWITCH(
        TRUE(),
        Value < Threshold1, Category1,
        Value < Threshold2, Category2,
        DefaultCategory
    )
```

Used in: Efficiency Category ID, Intensity Level ID

---

## 📚 Reference Table

| Function | Purpose | Example |
|----------|---------|---------|
| SUM | Add column values | `SUM([Sales])` |
| SUMX | Iterate and sum | `SUMX(Table, [A] * [B])` |
| DIVIDE | Safe division | `DIVIDE([A], [B])` |
| LEFT | Extract from left | `LEFT([Text], 3)` |
| SWITCH | Multiple conditions | `SWITCH(TRUE(), ...)` |
| VAR | Store variable | `VAR X = [Calc]` |
| DISTINCTCOUNT | Count unique | `DISTINCTCOUNT([ID])` |

---

## 🎓 Learning Resources

**To Learn More About DAX:**
1. SQLBI.com - Excellent DAX tutorials
2. DAX.Guide - Function reference
3. Microsoft Learn - Official documentation
4. DAX Patterns - Common solutions

**Practice Tips:**
1. Start simple - understand SUM and DIVIDE first
2. Use variables - makes debugging easier
3. Test incrementally - build complex formulas step by step
4. Check results - verify calculations make sense

---

**Remember:** DAX is powerful but logical. Break complex formulas into steps, use variables, and test as you go!

---

*End of DAX Logic Guide*
