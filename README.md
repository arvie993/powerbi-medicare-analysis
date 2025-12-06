# Medicare Inpatient Cost Analysis - Power BI Project

[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![Medicare Data](https://img.shields.io/badge/Data-CMS%20Medicare-blue?style=for-the-badge)](https://www.cms.gov/)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

A comprehensive Power BI semantic model analyzing Medicare inpatient hospital costs across the United States for 2017. This enterprise-grade solution enables healthcare administrators, analysts, and policy makers to perform sophisticated cost analysis, identify efficiency opportunities, and understand geographic and clinical cost patterns.

## 📊 Project Overview

### Key Statistics
- **196,325** unique provider-procedure combinations
- **3,182** healthcare providers across all 50 states
- **563** distinct DRG (Diagnosis Related Groups) procedures
- **23** medical specialties from Cardiology to Rehabilitation
- **$515.7M** maximum total payments analyzed

### Model Features
- ⭐ **Star Schema Design** - Optimized for performance and usability
- 📈 **11 Business Measures** - Pre-built calculations for instant insights
- 🗂️ **10 Dimension Tables** - Comprehensive filtering and categorization
- 🔗 **8 Active Relationships** - Properly modeled data connections
- 📝 **100% Documentation** - Every object fully documented
- ⚡ **Performance Optimized** - 10-15% size reduction with data type optimization

## 🎯 Use Cases

This model enables analysis across multiple dimensions:

### Cost Analysis
- Compare hospital charges vs. actual payments
- Identify cost-effective providers and procedures
- Analyze payment efficiency ratios
- Track Medicare spending patterns

### Geographic Analysis
- State, region, and division-level comparisons
- Urban vs. rural cost differences
- Regional market analysis
- Hospital referral region (HRR) patterns

### Clinical Analysis
- Medical specialty cost comparisons
- Surgical vs. medical procedure costs
- Severity level impact on costs
- DRG category performance

### Operational Analysis
- Volume-outcome relationships
- Service intensity levels
- Provider type comparisons
- Discharge volume patterns

## 📁 Repository Structure

```
powerbi-medicare-analysis/
├── README.md                          # This file
├── docs/
│   ├── technical/
│   │   ├── Model_Documentation_Complete.md      # Complete semantic model documentation
│   │   ├── DAX_Logic_Explained.md              # Technical DAX reference
│   │   ├── DataType_Optimization_Analysis.md   # Data type optimization guide
│   │   └── Naming_Convention_Analysis.md       # Object naming standards
│   ├── user-guides/
│   │   ├── Quick_Start_Guide.md                # Getting started guide
│   │   ├── PowerBI_Visualization_Guide.md      # Visualization best practices
│   │   └── Quick_Reference_Card.txt            # Printable reference card
│   └── analysis-reports/
│       ├── Column_Visibility_Report.md          # Field list optimization
│       ├── DataType_Optimization_Summary.txt   # Optimization results
│       └── Medicare_Model_Enhancement_Guide.md # Enhancement documentation
├── data/
│   └── reference/
│       └── DRG_Reference_Table.csv             # DRG classification reference
├── CHANGELOG.md                        # Version history
└── LICENSE                            # MIT License

```

## 🚀 Quick Start

### Prerequisites
- Power BI Desktop (latest version recommended)
- Access to CMS Medicare Inpatient data (2017)
- Basic understanding of healthcare terminology

### Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/[your-username]/powerbi-medicare-analysis.git
   cd powerbi-medicare-analysis
   ```

2. **Review the documentation**
   - Start with [`docs/user-guides/Quick_Start_Guide.md`](docs/user-guides/Quick_Start_Guide.md)
   - Review the complete model documentation in [`docs/technical/Model_Documentation_Complete.md`](docs/technical/Model_Documentation_Complete.md)

3. **Open in Power BI Desktop**
   - Connect to your Medicare Inpatient 2017 data source
   - Import the model structure using the documentation
   - Configure data source settings

4. **Explore the model**
   - Use pre-built measures for analysis
   - Create visualizations following the guide
   - Filter by dimensions for targeted insights

## 📖 Documentation

### Core Documentation

| Document | Description | Audience |
|----------|-------------|----------|
| [Complete Model Documentation](docs/technical/Model_Documentation_Complete.md) | Comprehensive semantic model documentation with data dictionary | All users |
| [Quick Start Guide](docs/user-guides/Quick_Start_Guide.md) | Getting started with the model | Business users |
| [DAX Logic Explained](docs/technical/DAX_Logic_Explained.md) | Technical DAX reference for all measures | Developers |
| [Visualization Guide](docs/user-guides/PowerBI_Visualization_Guide.md) | Best practices for creating reports | Analysts |

### Technical Documentation

- **Data Type Optimization**: Analysis and implementation of optimal data types (10-15% size reduction)
- **Naming Conventions**: Standardization analysis and recommendations
- **Column Visibility**: Field list optimization for improved user experience
- **Model Enhancement**: Documentation of dimensional model improvements

### Reference Materials

- **DRG Reference Table**: Complete classification of all 563 DRG procedures
- **Quick Reference Card**: Printable one-page reference for common tasks
- **Measure Catalog**: All 11 business measures with examples

## 🏗️ Model Architecture

### Star Schema Design

```
                    Fact Table (Center)
                Medicare Inpatient 2017
                    [196,325 rows]
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    Providers         DRG Dimension    Geography
   [3,182 rows]       [563 DRGs]      [52 states]
        │
        └── Payment Range, Volume Tiers, Efficiency Categories, etc.
```

### Key Components

**Fact Table**: Medicare Inpatient 2017
- 196,325 rows (one per Provider-DRG combination)
- 13 columns including measures and foreign keys
- 5 calculated columns for dimensional analysis

**Dimension Tables**: 10 tables supporting analysis
- Core: Providers, DRG Dimension, Geography
- Classification: Medical Categories, Payment Range, Volume Tiers
- Analytics: Efficiency Categories, Intensity Levels
- Reference: Provider Types, Market Segments

**Relationships**: 8 active many-to-one relationships
- Single-direction filtering (optimized for performance)
- Proper cardinality for accurate calculations

**Measures**: 11 business calculations
- Payment totals and averages
- Efficiency ratios
- Coverage percentages
- Distinct counts

## 🔍 Key Features

### Data Quality
- ✅ 100% referential integrity - all relationships valid
- ✅ No orphaned records - all foreign keys match
- ✅ ZIP code preservation - leading zeros maintained
- ✅ Currency precision - exact calculations with Decimal data type
- ✅ Comprehensive validation - business rules enforced

### Performance Optimization
- ⚡ Data type optimization: 10-15% size reduction
- ⚡ Proper indexing through relationships
- ⚡ Efficient DAX measures (SUMX, DIVIDE)
- ⚡ Hidden technical columns
- ⚡ Optimized for VertiPaq compression

### User Experience
- 🎯 Clean field lists - technical columns hidden
- 🎯 Business-friendly names - clear, consistent naming
- 🎯 Inline documentation - every field has description
- 🎯 Organized measures - grouped in display folders
- 🎯 Intuitive filtering - logical dimension hierarchies

## 📊 Sample Insights

### What You Can Discover

**Cost Efficiency**
- Identify providers with <20% payment-to-charge ratios (highly efficient)
- Compare Medicare spending across similar procedures
- Find cost savings opportunities by geographic region

**Geographic Patterns**
- Northeast states show higher average costs
- Rural areas have different service mix than urban
- Regional market dynamics impact pricing

**Clinical Insights**
- Cardiac procedures show high cost variability
- High-volume providers often more efficient
- Severity level significantly impacts costs

**Volume Analysis**
- Hospitals with 200-500 discharges show optimal efficiency
- Very low volume (<25) correlates with higher costs
- Specialty hospitals excel in specific DRG categories

## 🛠️ Technical Details

### Data Source
- **Origin**: CMS Inpatient Prospective Payment System (IPPS)
- **Year**: 2017
- **Format**: Excel (.xlsx)
- **Grain**: Provider-DRG combination
- **Update Frequency**: Annual (historical dataset)

### Technology Stack
- **Platform**: Microsoft Power BI Desktop
- **Storage Mode**: Import
- **DAX Engine**: VertiPaq
- **Query Language**: DAX (Data Analysis Expressions)
- **ETL**: Power Query (M language)

### Model Specifications
- **Tables**: 11 (1 fact, 10 dimensions)
- **Columns**: 61 (46 visible, 15 hidden)
- **Measures**: 11 calculated measures
- **Relationships**: 8 active relationships
- **Compatibility Level**: 1550+ recommended

## 📈 Version History

### Version 4.0 (December 2025)
- ✨ Data type optimization (6 columns optimized)
- ✨ Complete model documentation (50KB guide)
- ✨ Enhanced data dictionary with all tables
- ✨ Power Query documentation
- 🐛 Fixed ZIP code data loss (Int64 → Text)
- 🐛 Fixed currency rounding errors (Double → Decimal)

### Version 3.0 (December 2025)
- ✨ Column visibility optimization (15 columns hidden)
- ✨ Comprehensive documentation (83 objects)
- ✨ DAX logic documentation
- 📚 Created 4 user documentation files

### Version 2.0 (October 2023)
- ✨ Added 5 dimension tables
- ✨ Created 120+ DRG classifications
- ✨ Built 5 calculated columns
- 🔗 Established 8 relationships

### Version 1.0 (October 2023)
- 🎉 Initial model creation
- 📊 Basic fact and dimension tables
- 📈 Core measures implemented

See [CHANGELOG.md](CHANGELOG.md) for complete version history.

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

### Areas for Contribution
- 📊 Additional visualizations and dashboard templates
- 📖 Enhanced documentation and tutorials
- 🔧 Performance optimization techniques
- 🆕 Additional dimension tables (quality metrics, outcomes)
- 🌐 Multi-year analysis extensions

### How to Contribute
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Data Source**: Centers for Medicare & Medicaid Services (CMS)
- **Dataset**: Medicare Inpatient Prospective Payment System (IPPS) Provider Summary
- **Analysis Period**: 2017 Fiscal Year
- **Public Data**: All data used is publicly available from CMS

## 📞 Support

### Getting Help
- 📖 Check the [documentation](docs/)
- 🔍 Search existing [issues](../../issues)
- 💬 Open a new [issue](../../issues/new) for bugs or questions

### Resources
- [Power BI Documentation](https://docs.microsoft.com/power-bi/)
- [DAX Guide](https://dax.guide/)
- [CMS Data](https://www.cms.gov/data-research)

## 🎓 Learning Resources

### Recommended Reading
1. Start with the [Quick Start Guide](docs/user-guides/Quick_Start_Guide.md)
2. Review the [Complete Model Documentation](docs/technical/Model_Documentation_Complete.md)
3. Study the [DAX Logic Explained](docs/technical/DAX_Logic_Explained.md)
4. Practice with the [Visualization Guide](docs/user-guides/PowerBI_Visualization_Guide.md)

### Key Concepts
- **DRG (Diagnosis Related Groups)**: Medicare's system for classifying hospital cases
- **Payment-to-Charge Ratio**: Efficiency metric (lower is more efficient)
- **Service Intensity**: Complexity level based on average costs
- **HRR (Hospital Referral Region)**: Geographic market areas for tertiary care

---

## ⭐ Star This Repository

If you find this project useful, please consider giving it a star! It helps others discover the project and motivates continued development.

---

**Built with ❤️ for healthcare analytics professionals**

*Last Updated: December 2025*
