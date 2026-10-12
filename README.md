# Retail Sales Analytics Dashboard
An interactive retail sales analytics dashboard built in Power BI to track billing, treatment costs, insurance coverage, and departmental performance. Utilizes Power Query, DAX, and data visualization techniques to deliver actionable insights for healthcare decision-making.

<img width="1418" height="790" alt="image" src="https://github.com/user-attachments/assets/82f542b5-ae78-4712-becc-2a21507b7d1f" />

## 📊 Overview

This Power BI project provides an integrated analytics solution for healthcare provider organizations to track:
- **Financial metrics** including total billing amounts, treatment costs, and patient payments
- **Operational data** across different medical departments and procedures
- **Cost breakdown** analysis by service type (Emergency, Inpatient, Outpatient)
- **Billing patterns** by diagnosis and medical procedures

The dashboard is built with **Power BI Desktop** using the modern **CY25SU12 theme** for a professional, contemporary visual appearance.

## 📊 Key Metrics

### Primary Financial Indicators

| Metric | Value | Unit |
|--------|-------|------|
| **Total Billing Amount** | £3.36M | GBP |
| **Medicine Cost** | £546.04K | GBP |
| **Treatment Cost** | £2.63M | GBP |
| **Room Charges** | £179.63K | GBP |
| **Insurance Cost** | £2.23M | GBP |
| **Out-of-Pocket Expenses** | £1.13M | GBP |

### Average Per-Visit Metrics

- **Avg Billing per Visit**: £674.86
- **Avg Medication Cost**: £109.21
- **Avg Treatment Cost**: £526.08
- **Avg Room Charges**: £36.12
- **Avg Insurance Coverage**: £456.04
- **Avg Out-of-Pocket**: £227.26

## 📈 Dashboard Components

### Billing by Procedure
Breakdown of billing amounts across key medical procedures:
- **X-Ray**: £1,053,529 (31.39%)
- **CT Scan**: £805,508 (24.00%)
- **MRI Scan**: £600,739 (17.90%)
- **Ultrasound**: £481,347 (14.34%)
- **Blood Test**: £414,952 (12.36%)

### Billing by Diagnosis & Service Type
Comprehensive analysis across diagnoses with service type distribution (Emergency, Inpatient, Outpatient):
- **Hypertension** - 22.26% Emergency, 23.83% Inpatient, 53.92% Outpatient
- **Appendicitis** - 21.66% Emergency, 22.21% Inpatient, 56.13% Outpatient
- **Asthma** - 29.71% Emergency, 29.62% Inpatient, 40.66% Outpatient
- **Fracture** - 29.62% Emergency, 28.54% Inpatient, 41.84% Outpatient
- **Migraine** - 27.51% Emergency, 27.17% Inpatient, 45.32% Outpatient

### Department Distribution
Billing breakdown by medical departments:
- **Cardiology**: 25.24%
- **Orthopedics**: 24.23%
- **General Surgery**: 23.34%
- **Neurology**: 14.25%
- **Pediatrics**: 12.95%

## 🗂️ Data Model

The Power BI data model includes 10 core tables:

1. **Visits** (Fact) - Patient visit transactions and billing
2. **Patients** (Dimension) - Patient demographics
3. **Providers** (Dimension) - Healthcare provider details
4. **Departments** (Dimension) - Medical departments
5. **Diagnoses** (Dimension) - Medical conditions
6. **Procedures** (Dimension) - Medical procedures
7. **Insurance** (Dimension) - Insurance providers
8. **Cities** (Dimension) - Geographic locations
9. **DateTable** (Time) - Temporal data
10. **Calculations** - Helper calculations

**Relationships**: Auto-detected star schema with Visits as central fact table.

## 🔧 Technical Specifications

- **Format**: Power BI Desktop (.pbix)
- **Power BI Version**: 2026.03
- **Theme**: CY25SU12 Modern Corporate
- **Color Palette**: 40+ professional colors
- **Font**: Segoe UI

## ✨ Features

✅ Interactive visualizations with cross-filtering  
✅ Dynamic DAX calculations for financial metrics  
✅ Multi-dimensional analysis by procedure, diagnosis, and department  
✅ Service type breakdown (Emergency, Inpatient, Outpatient)  
✅ Department performance comparison  
✅ Responsive design for various screen sizes  
✅ Professional KPI cards for quick insights  

## 🚀 Getting Started

### Prerequisites
- Microsoft Power BI Desktop (2026.03 or later)
- Power BI Pro or Premium account (for publishing to Power BI Service)

### Opening the Project
1. Launch Power BI Desktop
2. Click **File** → **Open**
3. Select `healthcare_dashboard.pbix`
4. Refresh data connections when prompted

### Data Connection
1. Update data sources in **Home** → **Transform data** → **Data source settings**
2. Enter your SQL Server/database credentials
3. Configure automatic refresh schedule in Power BI Service (if publishing)

### Customization
- **Edit Visualizations**: Report view → Select any chart/card and modify using Format panel
- **Update Calculations**: Model view → Right-click table → New measure
- **Modify Data**: Home → Transform data → Power Query Editor
- **Change Theme**: Design tab → Themes → Customize current theme

## 📋 Use Cases

- **Healthcare Administrators**: Monitor financial health and billing metrics
- **Department Managers**: Track costs and procedure profitability
- **Financial Controllers**: Analyze revenue streams and cost optimization
- **Clinical Leadership**: Understand service distribution across care models
- **Billing Teams**: Identify patterns and insurance coverage analysis

## 📝 Notes

- All monetary values are in British Pounds (GBP)
- Percentages represent proportional distribution within categories
- Average values calculated from complete dataset
- Service types: Emergency, Inpatient, and Outpatient

## 📄 License

This project is provided as-is for healthcare analytics use. Ensure compliance with HIPAA, GDPR, and local data protection regulations.

---

**Created**: March 21, 2026  
**Power BI Version**: 2026.03  
**Theme**: CY25SU12 Modern Corporate
