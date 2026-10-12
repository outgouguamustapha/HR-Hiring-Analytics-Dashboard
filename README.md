# HR Recruitment & Talent Acquisition Analytics Dashboard

[![Power BI](https://img.shields.io/badge/Power_BI-Report-F2C811?style=flat&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![Documentation](https://img.shields.io/badge/Documentation-PRD_Included-orange.svg)](docs/PRD.md)
[![Data Model](https://img.shields.io/badge/Model-Star_Schema-green.svg)](#data-modeling--architecture)
[![DAX](https://img.shields.io/badge/DAX-Calculated_Measures-blue.svg)](#key-dax-measures)

An enterprise Power BI analytics dashboard engineered to evaluate recruitment funnel efficiency, candidate conversion velocity, recruiter interview schedules, and departmental talent acquisition performance.

---

## Table of Contents

- [About This Project](#about-this-project)
- [Product Requirements Document (PRD)](#product-requirements-document-prd)
- [Dashboard Architecture & Visual Interface](#dashboard-architecture--visual-interface)
- [Data Modeling & Architecture](#data-modeling--architecture)
- [Key DAX Measures](#key-dax-measures)
- [Business Insights & Strategic Recommendations](#business-insights--strategic-recommendations)
- [Repository Structure](#repository-structure)
- [Author & Contact](#author--contact)

---

## About This Project

Talent acquisition departments often struggle with fragmented visibility across the recruitment lifecycle. Tracking candidates across disconnected spreadsheets leads to unmonitored drop-off rates, scheduling conflicts, and delayed hiring decisions.

This project provides a centralized, interactive analytics suite built in **Microsoft Power BI**. It maps the entire hiring pipeline from initial application submission to final offer acceptance, providing recruiters and HR leaders with clear visibility into funnel conversion, interview calendar execution, and weekly application velocity.

### Core Deliverables
- **Recruitment Funnel Analysis:** Visibility into candidate attrition across stages (*Application*, *Screening*, *HR Interview*, *Technical Interview*, *Offer*, and *Hired*).
- **Interview & Calendar Execution Engine:** Operational schedule grid pairing candidate meetings against hourly slots with dynamic completion indicators.
- **Weekly Applicant Velocity Tracking:** Time-series trends highlighting peak sourcing weeks with dynamic min/max highlight callouts.
- **Requisition & Position Drilldown:** Departmental filtering across job positions, project statuses, and headcount requirements.

---

## Product Requirements Document (PRD)

To ensure this solution addresses genuine recruitment operational challenges, development followed a formal product specification.

- **Full PRD Document:** [docs/PRD.md](docs/PRD.md)

### Core Business Objectives Addressed
- Eliminate blind spots in applicant drop-offs by standardizing hiring funnel progression metrics.
- Provide real-time recruiter schedule execution visibility using dynamic date-time threshold evaluations.
- Deliver granular departmental insights into candidate sourcing quality and offer acceptance rates.

---

## Dashboard Architecture & Visual Interface

### Talent Acquisition & Recruitment Overview
The dashboard brings together high-level hiring KPIs, a candidate pipeline funnel, an operational interview calendar, and weekly candidate sourcing trends:

![HR Recruitment Dashboard](HR%20Dashboard.png)

---

## Data Modeling & Architecture

The report semantic layer connects candidate transactions with organizational, project, and scheduling dimensions:

```text
+-------------------+                           +-------------------+
|    dimPosition    |                           |     Meetings      |
+-------------------+                           +-------------------+
          | 1                                             | 1
          |                                               |
          | *                                             | *
+-------------------+                           +-------------------+
|   fctCandidates   |-------------*-------------|  dimRecruitment   |
+-------------------+ 1                       1 +-------------------+
          | *                                             | *
          |                                               |
          | 1                                             | 1
+-------------------+                           +-------------------+
|      dimDate      |                           |   dimEmployees    |
+-------------------+                           +-------------------+
```
* **`fctCandidates` (Fact Table):** Stores individual applicant records, including stage progressions (ApplicationStatus_nr), application dates, meeting timestamps, expected salaries, and application sources.
* **`dimRecruitment & dimPosition`:** Tracks job requisition workflows, target dates, project owners, departments, and position IDs.
* **`dimDate`:** Comprehensive calendar table with day, week (ISO), month, and year dimensions for chronological pacing.
* **`Meetings`:** A synthetic schedule table generated via GENERATE and GENERATESERIES(9, 18, 1) to evaluate business hour interview slots against current time thresholds.
* **`Applications Steps`:** Pre-aggregated stage table ensuring seamless visual rendering of candidate funnel distribution.

## Key DAX Measures
The analytical model leverages explicit, formatted DAX measures categorized into distinct operational domains:

### 1. Funnel Staging & Pipeline Progression
```
// Base Applicant Count per Selected Stage
Count = 
SELECTEDVALUE('Applications Steps'[Candidates Count])

// Funnel Step Transformation (Technical Interview Example)
Funnel Technical Interview = 
VAR FirstStep =
    CALCULATE(
        SUM('Applications Steps'[Candidates Count]),
        'Applications Steps'[Application Status] = "Application"
    )
VAR ActualStep =
    CALCULATE(
        SUM('Applications Steps'[Candidates Count]),
        'Applications Steps'[Application Status] = "Technical Interview"
    )
RETURN 
    FirstStep - ActualStep
```
### 2. Operational Interview Schedule & Time Tracking
```
// Current Date-Time Benchmark for Meeting Status
Today + Time = 
DATE(2025, 6, 9) + TIME(12, 30, 0)

// Meeting Elapsed Indicator (1 if meeting passed, 0 if upcoming)
Diff Meeting Time vs Actual = 
IF([Meeting DateTime] <= [Today + Time], 1, 0)

// Dynamic Checkmark Status Icon URL
Table Icon = 
IF(
    [Diff Meeting Time vs Actual] = 1,
    "[https://i.ibb.co/VWJwh6CR/checkmark-svgrepo-com.png](https://i.ibb.co/VWJwh6CR/checkmark-svgrepo-com.png)",
    "[https://i.ibb.co/NdrJ9r1t/checkmark-GREY.png](https://i.ibb.co/NdrJ9r1t/checkmark-GREY.png)"
)
```
### 3. Recruitment Pipeline Health & KPIs
```
// Aggregate Applicant Volume
Total Applications = 
COUNTROWS(fctCandidates)

// Overall Candidate Pool (Unfiltered by Position)
CANDIDATES = 
CALCULATE(
    COUNTROWS(fctCandidates),
    REMOVEFILTERS(dimRecruitment[Position ID])
)

// Filled / Closed Requisitions Count
HIRED = 
CALCULATE(
    COUNTROWS(dimRecruitment),
    dimRecruitment[Recruitment status] = "Offer & Closure",
    REMOVEFILTERS(dimRecruitment[Position ID])
)

// Offer-to-Hire Conversion Fraction
Hiring Rate (Fraction) = 
VAR Hired =
    CALCULATE(
        DISTINCTCOUNT(fctCandidates[Candidate_ID]),
        fctCandidates[Application Status] = "Hired"
    )
VAR Applied =
    CALCULATE(
        DISTINCTCOUNT(fctCandidates[Candidate_ID]),
        fctCandidates[Application Status] = "Offer"
    )
RETURN
IF(
    Applied = 0,
    "0/0",
    FORMAT(Hired, "0") & "/" & FORMAT(Applied, "0")
)
```
### 4. Sourcing Trend & Dynamic Chart Highlights
```
// Weekly Candidate Intake Volume
Weekly Applications = 
VAR _week =
    CALCULATE(
        DISTINCTCOUNT(fctCandidates[Candidate_ID]),
        ALLEXCEPT('DimDate', 'DimDate'[Year], 'DimDate'[Week (ISO)])
    )
RETURN _week

// Peak Week Highlight Marker
Weekly Candidates Max Marker = 
VAR _current = [Weekly Applications]
VAR _max = [Weekly Candidates Max]
RETURN
IF(_current = _max, _current, BLANK())
```

## Business Insights & Strategic Recommendations
Analysis of candidate records, interview logs, and stage transitions highlights several concrete operational improvements for talent acquisition:

### 1. Identify and Resolve Mid-Funnel Attrition
* **Finding:** The steepest drop-off occurs between the Screening and Technical Interview stages, with less than 28% of initial applicants progressing to technical evaluation.

* **Actionable Recommendation:** Refine initial screening filters and review technical qualification criteria with department hiring managers. Introducing structured pre-screening assessments will prevent recruiter time from being spent on misaligned candidates while reducing time-in-stage.

### 2. Optimize Recruiter Interview Capacity
* **Finding:** Analysis of the interview execution grid shows meeting scheduling density concentrated heavily in mid-week afternoon slots, creating recruiter fatigue and delayed candidate feedback.

* **Actionable Recommendation:** Implement standard scheduling bands (e.g., dedicated morning blocks for screening calls and afternoon blocks for debriefs) to balance workload and keep candidate response cycles under 48 hours.

### 3. Improve Offer-to-Hire Conversion Ratios
* **Finding:** While candidate progression through to the Offer stage is stable, final offer acceptance drops in senior technical roles where expected salary expectations diverge from budgeted ranges.

* **Actionable Recommendation:** Standardize compensation expectation discovery during the first HR screen rather than at the offer stage. Benchmarking salary bands earlier in the pipeline will reduce late-stage offer rejections.

## Repository Structure
```
├── docs/
│   └── PRD.md                      # Product Requirements Document
├── HR Dashboard.png                # Dashboard interface screenshot
├── HR Data.xlsx                    # Source recruitment dataset
├── LICENSE                         # Repository open-source license
└── README.md                       # Main project overview and documentation
```


## Author & Contact

**OUTGOUGUA MUSTAPHA**

Data Analyst | Power BI Developer | Business Intelligence Specialist

- 💼 [LinkedIn](https://www.linkedin.com/in/mustapha-outgougua/)
- 🐙 [GitHub](https://github.com/outgouguamustapha)
- 📧 Open to collaboration and opportunities

---

### Let's Connect!

I'm always open to:
- 💼 Collaboration opportunities
- 🤝 Consulting projects
- 💬 Discussing data analytics and BI
- 🎓 Mentoring and knowledge sharing

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Power BI community for inspiration and best practices
- HR professionals who provided domain expertise

---

<div align="center">

### ⭐ Found this project helpful?

**Please consider giving it a star!**

[![GitHub Stars](https://img.shields.io/github/stars/outgouguamustapha/HR-Hiring-Analytics-Dashboard?style=social)](https://github.com/outgouguamustapha/HR-Hiring-Analytics-Dashboard)

Made with ❤️ and Power BI

*Last Updated: October  2026*

[↑ Back to Top](#hr-recruitment--talent-acquisition-analytics-dashboard)

</div>


