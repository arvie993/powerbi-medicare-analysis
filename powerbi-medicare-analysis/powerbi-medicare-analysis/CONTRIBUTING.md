# Contributing to Medicare Inpatient Cost Analysis

Thank you for your interest in contributing to this project! This document provides guidelines and instructions for contributing.

## Table of Contents
- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Contribution Guidelines](#contribution-guidelines)
- [Documentation Standards](#documentation-standards)
- [Pull Request Process](#pull-request-process)

## Code of Conduct

This project adheres to a code of conduct that all contributors are expected to follow:

- Be respectful and inclusive
- Focus on what is best for the community
- Show empathy towards other community members
- Accept constructive criticism gracefully
- Focus on facts and solutions rather than opinions

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When creating a bug report, include:

- **Clear title and description**
- **Steps to reproduce** the issue
- **Expected behavior** vs actual behavior
- **Screenshots** if applicable
- **Environment details** (Power BI version, OS, etc.)

### Suggesting Enhancements

Enhancement suggestions are welcome! Please provide:

- **Clear use case** for the enhancement
- **Expected benefits** to users
- **Potential implementation approach**
- **Any relevant examples** from other projects

### Areas for Contribution

We welcome contributions in these areas:

#### 1. Documentation
- Improve existing documentation clarity
- Add more examples and use cases
- Create video tutorials or walkthroughs
- Translate documentation to other languages
- Add FAQs and troubleshooting guides

#### 2. Data Model Enhancements
- Additional dimension tables
- New calculated measures
- Performance optimizations
- Data quality improvements
- Multi-year support

#### 3. Visualizations
- Dashboard templates
- Report page layouts
- Custom visuals integration
- Mobile-optimized designs
- Accessibility improvements

#### 4. Analysis Extensions
- Quality metrics integration
- Readmission rate analysis
- Outcome metrics
- Predictive analytics
- Statistical measures

#### 5. Testing
- Data validation scripts
- Performance benchmarks
- Edge case testing
- Cross-browser testing
- Documentation accuracy

## Development Setup

### Prerequisites

1. **Power BI Desktop** - Latest version recommended
   - Download: https://powerbi.microsoft.com/desktop/

2. **Git** - For version control
   - Download: https://git-scm.com/

3. **Text Editor** - VS Code recommended
   - Download: https://code.visualstudio.com/

4. **DAX Studio** (optional) - For advanced DAX development
   - Download: https://daxstudio.org/

### Local Setup

```bash
# Clone your fork
git clone https://github.com/YOUR-USERNAME/powerbi-medicare-analysis.git
cd powerbi-medicare-analysis

# Create a feature branch
git checkout -b feature/your-feature-name

# Make your changes
# ...

# Commit your changes
git add .
git commit -m "Description of your changes"

# Push to your fork
git push origin feature/your-feature-name
```

## Contribution Guidelines

### Documentation

- Use clear, concise language
- Include examples where helpful
- Follow Markdown best practices
- Test all links
- Update table of contents when needed

### DAX Measures

When adding or modifying measures:

```dax
// ✅ GOOD: Clear name, formatted, commented
Average Cost per Admission = 
DIVIDE(
    [Total Healthcare Costs],
    [Total Admissions],
    BLANK() // Return blank if no admissions
)

// ❌ BAD: Unclear, no formatting, no comments
AvgCst = [TotCst]/[TotAdm]
```

**Best Practices:**
- Use descriptive measure names
- Format DAX with proper indentation
- Add comments explaining business logic
- Use DIVIDE instead of / for safety
- Include BLANK() handling
- Test with different filter contexts

### Data Model Changes

When modifying the data model:

1. **Document the change**
   - Update relevant documentation files
   - Add entry to CHANGELOG.md
   - Update data dictionary if needed

2. **Test thoroughly**
   - Verify all relationships work
   - Test measures with new structure
   - Check performance impact
   - Validate data accuracy

3. **Maintain compatibility**
   - Don't break existing reports if possible
   - Provide migration guide for breaking changes
   - Update version number appropriately

### File Organization

```
docs/
├── technical/          # Technical documentation for developers
├── user-guides/        # End-user guides and tutorials
└── analysis-reports/   # Analysis results and findings

data/
└── reference/          # Reference data and lookup tables

images/                 # Screenshots and diagrams
```

## Documentation Standards

### Markdown Files

- Use heading levels appropriately (# → ## → ### → ####)
- Include table of contents for long documents
- Use code blocks with language specification
- Add alt text to images for accessibility
- Keep line length reasonable (80-120 characters)

### DAX Documentation

Document each measure with:

```markdown
### Measure Name

**Purpose:** What business question does this answer?

**Business Logic:** How does it work in plain language?

**DAX Formula:**
```dax
Measure Name = 
CALCULATE(
    SUM(Table[Column]),
    FILTER(...)
)
```

**Example:** "If filtering by California, returns total costs for CA providers only."

**Use Cases:** 
- Use case 1
- Use case 2
```

### Code Comments

```dax
// Purpose: Calculate year-over-year growth percentage
// Input: Requires Year filter or slicer
// Output: Percentage change from previous year
// Example: 0.15 means 15% growth
YoY Growth % = 
VAR CurrentYear = [Total Sales]
VAR PreviousYear = 
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR('Date'[Date])
    )
RETURN
    DIVIDE(
        CurrentYear - PreviousYear,
        PreviousYear,
        BLANK() // Return blank if no previous year data
    )
```

## Pull Request Process

### Before Submitting

1. **Update documentation**
   - Update relevant docs files
   - Add entry to CHANGELOG.md
   - Update README.md if needed

2. **Test your changes**
   - Verify all measures calculate correctly
   - Check relationships work as expected
   - Test with different filter combinations
   - Validate performance

3. **Clean up**
   - Remove debug code
   - Fix formatting issues
   - Remove unnecessary files
   - Update .gitignore if needed

### Submitting

1. **Create Pull Request**
   - Use clear, descriptive title
   - Reference related issues (#123)
   - Provide detailed description
   - Include screenshots if applicable

2. **PR Description Template**

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Code refactoring

## Related Issues
Fixes #123
Relates to #456

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
- [ ] Tested locally in Power BI Desktop
- [ ] Verified all measures calculate correctly
- [ ] Checked relationships
- [ ] Updated documentation
- [ ] Added to CHANGELOG.md

## Screenshots
(If applicable)

## Additional Notes
Any other context
```

### Review Process

1. **Automated checks** will run (if configured)
2. **Maintainer review** - expect feedback within a week
3. **Revisions** - address any requested changes
4. **Approval** - once approved, PR will be merged
5. **Recognition** - you'll be added to contributors list!

## Recognition

Contributors will be recognized in:
- README.md contributors section
- Release notes
- CHANGELOG.md
- Project documentation

## Questions?

- **General questions**: Open a [discussion](../../discussions)
- **Bug reports**: Create an [issue](../../issues/new?template=bug_report.md)
- **Feature requests**: Create an [issue](../../issues/new?template=feature_request.md)

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

**Thank you for contributing to the Medicare Inpatient Cost Analysis project!** 🎉

Your contributions help healthcare professionals make better data-driven decisions and improve patient outcomes.
