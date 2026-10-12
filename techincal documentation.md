# Retail Sales Dashboard - Technical Documentation

## 📖 Table of Contents
1. [Architecture Overview](#architecture-overview)
2. [Data Model Design](#data-model-design)
3. [Power Query Transformations](#power-query-transformations)
4. [DAX Calculations](#dax-calculations)
5. [Visualization Configuration](#visualization-configuration)
6. [Performance Optimization](#performance-optimization)
7. [Troubleshooting Guide](#troubleshooting-guide)

---

## Architecture Overview

### Project Structure
```
retailsales dashboard.pbix
├── Data Model (10 tables)
├── Report Pages (1 primary dashboard)
├── Calculations (DAX formulas)
├── Visualizations (6+ custom charts)
└── Theme (CY25SU12 Modern Theme)
```

### Component Interaction Flow
```
Data Sources
    ↓
Power Query (Transform)
    ↓
Data Model (Relationships)
    ↓
DAX Calculations (Measures)
    ↓
Visualizations (Charts & Cards)
    ↓
Interactive Dashboard
```

---

## Data Model Design

### Table Definitions

#### 1. **Visits Table** (Fact Table)
Central fact table containing all patient visit transactions.

**Key Columns:**
- `VisitID` - Unique identifier for each visit
- `PatientID` - Reference to patient dimension
- `ProviderID` - Reference to provider dimension
- `CityID` - Geographic reference
- `DepartmentID` - Department reference
- `DiagnosisID` - Diagnosis reference
- `ProcedureID` - Procedure reference
- `VisitDate` - Transaction date (Date table reference)
- `BillingAmount` - Total amount billed
- `InsuranceCost` - Insurance portion
- `OutOfPocketCost` - Patient's out-of-pocket amount
- `ServiceType` - Emergency/Inpatient/Outpatient classification

**Cardinality**: High (main metric table)

#### 2. **Patients Table** (Dimension)
Patient demographic and administrative information.

**Key Columns:**
- `PatientID` - Primary key
- `PatientName` - Full name
- `DateOfBirth` - For demographic analysis
- `Gender` - Patient gender
- `ContactInfo` - Phone/email

**Cardinality**: One-to-Many with Visits

#### 3. **Providers Table** (Dimension)
Healthcare provider details.

**Key Columns:**
- `ProviderID` - Primary key
- `ProviderName` - Provider full name
- `Specialty` - Medical specialty
- `License` - Medical license number
- `YearsOfExperience` - Provider tenure

**Cardinality**: One-to-Many with Visits

#### 4. **Departments Table** (Dimension)
Medical departments and specialties.

**Key Columns:**
- `DepartmentID` - Primary key
- `DepartmentName` - Department name (e.g., Cardiology)
- `Head` - Department head name
- `BudgetAllocation` - Annual budget
- `Location` - Physical location code

**Cardinality**: One-to-Many with Visits

#### 5. **Diagnoses Table** (Dimension)
Medical conditions and diagnoses tracked.

**Key Columns:**
- `DiagnosisID` - Primary key
- `DiagnosisName` - Condition name (e.g., Hypertension)
- `ICD10Code` - International Classification of Diseases code
- `Severity` - Severity level
- `Category` - Clinical category

**Current Values**: Hypertension, Appendicitis, Asthma, Fracture, Migraine

**Cardinality**: One-to-Many with Visits

#### 6. **Procedures Table** (Dimension)
Medical procedures and their classifications.

**Key Columns:**
- `ProcedureID` - Primary key
- `ProcedureCode` - Standard procedure code
- `ProcedureName` - Procedure name
- `Category` - Procedure category
- `AverageCost` - Expected cost

**Current Values**: X-Ray, CT Scan, MRI Scan, Ultrasound, Blood Test

**Cardinality**: One-to-Many with Visits

#### 7. **Insurance Table** (Dimension)
Insurance providers and coverage details.

**Key Columns:**
- `InsuranceID` - Primary key
- `InsuranceProvider` - Provider name
- `CoverageType` - Type of coverage
- `CoveragePercentage` - % of costs covered
- `MaxClaimAmount` - Maximum claimable amount

**Cardinality**: One-to-Many with Visits

#### 8. **Cities Table** (Dimension)
Geographic locations for facility distribution.

**Key Columns:**
- `CityID` - Primary key
- `CityName` - City name
- `State/Region` - Geographic region
- `Country` - Country code
- `Facility` - Associated facility

**Cardinality**: One-to-Many with Visits

#### 9. **DateTable** (Time Dimension)
Temporal dimension for time-series analysis.

**Key Columns:**
- `Date` - Primary key (full date)
- `Year` - Extract year
- `Quarter` - Extract quarter
- `Month` - Extract month
- `Week` - Week number
- `DayOfWeek` - Day name
- `IsWeekend` - Boolean flag

**Cardinality**: One-to-Many (implicit)

#### 10. **Calculations Table** (Helper)
Helper table for complex DAX calculations and intermediate results.

---

## Power Query Transformations

### Transformation Steps

#### Step 1: Data Extraction
```
Source → SQL Server / Data Warehouse
    Apply: Filter valid records only
    Remove: Incomplete or null transactions
```

#### Step 2: Data Type Validation
```
Ensure Correct Types:
- Numeric columns → Decimal/Currency
- Date columns → Date type
- Text columns → Text type
- Boolean → True/False
```

#### Step 3: Cleansing Operations
```
Remove Duplicates:
    Key fields: VisitID, PatientID, ProviderID

Handle Nulls:
    DateOfBirth → Use system default
    InsuranceCost → Set to 0 if null
    OutOfPocketCost → Calculate if missing

Standardize Text:
    All text to proper case
    Trim whitespace
    Remove special characters from IDs
```

#### Step 4: Derived Columns
```
ServiceType Classification:
    IF Emergency → "Emergency"
    ELSE IF Admission >= 2 nights → "Inpatient"
    ELSE → "Outpatient"

Cost Calculations:
    TotalBillingAmount = DirectCost + AdministrativeFee
    TotalCost = Medicine + Treatment + Room
```

---

## DAX Calculations

### Key Measures

#### Financial Metrics

```dax
// Total Billing Amount
Total Billing Amount = 
    SUM(Visits[BillingAmount])

// Average Billing per Visit
Avg Billing per Visit = 
    DIVIDE(
        [Total Billing Amount],
        COUNTA(Visits[VisitID]),
        0
    )

// Medicine Cost (Total)
Total Medicine Cost = 
    SUM(Visits[MedicineCost])

// Avg Medication per Visit
Avg Medicine Cost = 
    AVERAGEX(VALUES(Visits[VisitID]), Visits[MedicineCost])

// Treatment Cost Analysis
Total Treatment Cost = 
    SUM(Visits[TreatmentCost])

Avg Treatment Cost = 
    DIVIDE(
        [Total Treatment Cost],
        COUNTROWS(VALUES(Visits[VisitID])),
        0
    )

// Room Charges
Total Room Charges = 
    SUM(Visits[RoomCharges])

Avg Room Charges = 
    DIVIDE(
        [Total Room Charges],
        COUNTA(Visits[VisitID]),
        0
    )

// Insurance Analysis
Total Insurance Cost = 
    SUM(Visits[InsuranceCost])

Avg Insurance Coverage = 
    DIVIDE(
        [Total Insurance Cost],
        COUNTA(Visits[VisitID]),
        0
    )

// Out-of-Pocket Expenses
Total Out-of-Pocket = 
    SUM(Visits[OutOfPocketCost])

Avg Out-of-Pocket = 
    DIVIDE(
        [Total Out-of-Pocket],
        COUNTA(Visits[VisitID]),
        0
    )
```

#### Distribution Metrics

```dax
// Procedure Cost Distribution
Procedure % Share = 
    DIVIDE(
        [Total Billing Amount],
        CALCULATE([Total Billing Amount], ALL(Procedures)),
        0
    )

// Department Distribution
Department % Share = 
    DIVIDE(
        [Total Billing Amount],
        CALCULATE([Total Billing Amount], ALL(Departments)),
        0
    )

// Diagnosis Distribution
Diagnosis % Share = 
    DIVIDE(
        [Total Billing Amount],
        CALCULATE([Total Billing Amount], ALL(Diagnoses)),
        0
    )
```

#### Service Type Analysis

```dax
// Service Type Cost Distribution
Emergency Cost = 
    CALCULATE(
        [Total Billing Amount],
        Visits[ServiceType] = "Emergency"
    )

Inpatient Cost = 
    CALCULATE(
        [Total Billing Amount],
        Visits[ServiceType] = "Inpatient"
    )

Outpatient Cost = 
    CALCULATE(
        [Total Billing Amount],
        Visits[ServiceType] = "Outpatient"
    )
```

### Calculation Groups (Optional Enhancement)

```dax
// Time Intelligence Group
YEAR(Visits[VisitDate])
MONTH(Visits[VisitDate])
QUARTER(Visits[VisitDate])
```

---

## Visualization Configuration

### KPI Cards
**6 Main Cards** displaying:
1. Total Billing Amount - £3.36M
2. Medicine Cost - £546.04K
3. Treatment Cost - £2.63M
4. Room Charges - £179.63K
5. Insurance Cost - £2.23M
6. Out-of-Pocket - £1.13M

**Configuration**:
- Font: Segoe UI Semibold (Bold)
- Size: Large, easy to read
- Color: Dark text on light blue background
- Subtitles: Average metrics below main value

### Bar Chart: Billing by Procedure
**Type**: Clustered Column Chart
**Axis**:
- X-Axis: Procedure names
- Y-Axis: Billing amount (£)
**Data Labels**: Value + Percentage
**Colors**: Blue gradient palette

### Stacked Bar: Billing by Diagnosis & Service Type
**Type**: Stacked Horizontal Bar
**Axis**:
- X-Axis: Billing amount
- Y-Axis: Diagnosis names
**Legend**: Emergency | Inpatient | Outpatient
**Data Labels**: Percentage distribution

### Bar Chart: Department Distribution
**Type**: Horizontal Bar Chart
**Sorting**: By billing amount (descending)
**Top 5 Departments**: 
- Cardiology (25.24%)
- Orthopedics (24.23%)
- General Surgery (23.34%)
- Neurology (14.25%)
- Pediatrics (12.95%)

---

## Performance Optimization

### Best Practices Implemented

#### 1. **Data Model Optimization**
✓ **Star Schema Design**: Single fact table (Visits) with multiple dimensions
✓ **Relationship Configuration**: Auto-detected relationships (simplifies maintenance)
✓ **Column Selection**: Only include necessary columns in import
✓ **Data Types**: Proper type assignments reduce memory footprint

#### 2. **Query Performance**
✓ **Aggregations**: Pre-calculate common summaries
✓ **Calculated Columns**: Minimize use (computed at load time)
✓ **Measures**: Use implicit measures where possible
✓ **Filter Context**: Optimize visual filter combinations

#### 3. **DAX Optimization**
```dax
// ❌ AVOID: Inefficient
Total Cost (Slow) = 
    SUMX(VALUES(Visits[VisitID]), 
        Visits[Medicine] + Visits[Treatment] + Visits[Room])

// ✓ USE: Optimized
Total Cost (Fast) = 
    SUM(Visits[TotalCost])
```

#### 4. **Visual Design Optimization**
- **Limit Detail**: Maximum 5 dimensions per visual
- **Color Coding**: Consistent palette reduces cognitive load
- **Tooltips**: Add context to ambiguous metrics
- **Responsive Layout**: Adapts to various screen sizes

### Recommended Data Refresh Strategy
- **Frequency**: Daily (off-peak hours)
- **Incremental**: Load only new/changed records
- **Partitioning**: Split large fact tables by month/year
- **Caching**: Enable query result caching in Power BI Service

---

## Troubleshooting Guide

### Common Issues & Solutions

#### 1. **Blank Visuals**
**Symptom**: Charts show no data
**Causes**:
- Missing data in table
- Incorrect filter context
- Broken relationship
- NULL values in key column

**Solution**:
1. Check data source connectivity
2. Verify measure definition
3. Test relationship cardinality
4. Review filter panel selections

#### 2. **Slow Performance**
**Symptom**: Dashboard takes >5 seconds to load
**Causes**:
- Large data volume (>10M rows)
- Complex DAX calculations
- Too many active filters
- Unoptimized relationships

**Solution**:
1. Reduce imported data volume
2. Pre-aggregate data
3. Archive historical data
4. Add aggregation tables

#### 3. **Incorrect Calculations**
**Symptom**: Metrics don't match expected values
**Causes**:
- Filter context issues
- Incorrect function usage
- Data quality problems
- Rounding errors

**Solution**:
1. Verify DAX formula
2. Check filter panel
3. Validate data quality
4. Test with sample dataset

#### 4. **Relationship Errors**
**Symptom**: Cascading filter fails
**Causes**:
- Inactive relationships
- Wrong cardinality setting
- Missing keys in dimension

**Solution**:
1. Review Manage Relationships dialog
2. Set correct cross-filter direction
3. Verify key field values
4. Test cross-table filtering

---

## Advanced Topics

### Row-Level Security (RLS)
For sensitive healthcare data, implement RLS:
```dax
[DepartmentID] = CALCULATE(
    VALUES(Security[DepartmentID]),
    Security[UserEmail] = USERNAME()
)
```

### Dynamic Titles
Create context-aware titles:
```dax
Report Title = 
    "Healthcare Analytics - " & 
    FORMAT(TODAY(), "MMM YYYY")
```

### What-If Analysis
Enable parameter-based scenarios:
```dax
Budget Impact = 
    [Total Billing Amount] * (1 + [Budget % Change]/100)
```

---

## Maintenance Checklist

- [ ] Monthly data quality audit
- [ ] Quarterly performance review
- [ ] Annual theme/design refresh
- [ ] Bi-annual security audit
- [ ] Check for deprecated measures
- [ ] Validate all key relationships
- [ ] Test disaster recovery

---

## Support & Escalation

**For Technical Issues**:
1. Review this documentation
2. Check Power BI Service health status
3. Contact BI Development Team
4. Submit incident with screenshots

**Documentation Updates**:
- Keep this guide updated with schema changes
- Document new calculations with examples
- Maintain version history

---

**Last Updated**: March 21, 2026  
**Document Version**: 1.0  
**Maintainer**: Analytics Team
