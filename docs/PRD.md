# Product Requirements Document (PRD)
## HR Recruitment Pipeline & Hiring Analytics Dashboard

- **Document Version:** 1.0.0
- **Project Name:** HR Hiring Analytics Dashboard
- **Owner / BI Analyst:** Mustapha Outgougua
- **Target Platform:** Microsoft Power BI Desktop & Service

---

## 1. Executive Summary & Problem Statement

### 1.1 Business Context
Talent acquisition and human resources teams manage candidate pipelines across multiple job openings, departments, and recruitment channels. Without a centralized, interactive analytics layer, HR leadership faces:
- **Pipeline Blindspots:** Inability to identify at which hiring stage (Screening, HR Interview, Technical Interview, Offer) candidate attrition is most severe.
- **Schedule Management Gaps:** Difficulty tracking recruiter interview schedules, daily meeting time commitments, and interview completion rates in real time.
- **Budget & Cost Inefficiency:** Disconnect between recruitment spend, candidate acquisition channels, and actual headcount fulfillment against planned role targets.

### 1.2 Proposed Solution
Develop an enterprise-grade HR Business Intelligence dashboard in Microsoft Power BI. The solution models candidate pipelines, interview calendars, and recruitment project health, enabling Talent Acquisition Managers and HR Executives to monitor funnel velocity, interview execution, and offer acceptance rates from a single governed report.

---

## 2. Stakeholders & User Personas

| Persona | Role | Primary Objective / Pain Point |
|---|---|---|
| **Head of Talent Acquisition / HR Director** | Strategic Oversight | Monitor total candidate volume, offer acceptance rates, time-to-hire, and recruitment budget adherence. |
| **Recruitment Lead / Recruiter** | Operational Execution | Track daily/weekly interview schedules, candidate progress across stages, and stage drop-offs. |
| **Department Hiring Managers** | Role Owner | Review requisition progress for open departmental positions (e.g., Sales, Engineering) and candidate status. |

---

## 3. Goals & Success Metrics (OKRs)

### 3.1 Business Goals
- Reduce candidate pipeline stage bottlenecks by providing clear funnel conversion rates.
- Improve recruiter interview execution by tracking scheduled meeting times against actual status.
- Accelerate candidate throughput from initial application to offer closure.

### 3.2 Technical Requirements
- Establish a clean relational data model linking transactional candidate facts with position, recruitment project, employee, and calendar dimensions.
- Implement DAX measures for dynamic funnel stages, date-time calendar comparisons, weekly application trends, and status indicator logic.

---

## 4. Key Performance Indicators (KPIs) & Business Logic

| Metric Name | DAX Expression / Concept | Business Purpose | Target / Benchmark |
|---|---|---|---|
| **Total Applications** | `COUNTROWS(fctCandidates)` | Aggregate volume of applicants received across all requisitions. | Requisition demand driven |
| **Total Candidates** | `CALCULATE(COUNTROWS(fctCandidates), REMOVEFILTERS(dimRecruitment[Position ID]))` | Total applicant pool independent of specific position filters. | Volume baseline |
| **Hired Count** | `CALCULATE(COUNTROWS(dimRecruitment), dimRecruitment[Recruitment status] = "Offer & Closure", REMOVEFILTERS(dimRecruitment[Position ID]))` | Positions successfully filled and closed. | 100% of open requisitions |
| **Hiring Rate (Fraction)** | Compares candidates reaching `Hired` vs. reaching `Offer` status. | Gauge candidate offer acceptance and conversion strength. | > 80% offer acceptance |
| **Weekly Applications** | `CALCULATE(DISTINCTCOUNT(fctCandidates[Candidate_ID]), ALLEXCEPT(DimDate, DimDate[Year], DimDate[Week (ISO)]))` | Inflow velocity of new candidates across calendar weeks. | Consistent inbound pacing |

---

## 5. Functional & Dashboard Architecture

### 5.1 Architecture & Dimensional Model
- **Fact Table:** `fctCandidates` (Candidate ID, Application Date, Application Status, Position ID, Meeting DateTime, Expected Salary).
- **Dimension Tables:** 
  - `dimPosition`: Department, Position Name, Requisition Status.
  - `dimRecruitment`: Recruitment project status, target dates, project owners.
  - `dimEmployees`: Existing headcount and employee data.
  - `dimDate`: Full continuous calendar with ISO week numbering.
- **Operational / Supporting Tables:**
  - `Meetings`: Calculated Cartesian grid of calendar dates and business hours (09:00 to 18:00) for calendar schedule tracking.
  - `Applications Steps` & `Application Stages`: Normalized pipeline order and stage grouping for custom funnel visualization.

### 5.2 Visual Interface & Layout
- **Executive Header & Position Context:** Seniority level, department, office location, and requisition filters.
- **Recruitment Funnel:** Custom-engineered staged funnel tracking candidate conversion through *Application → Screening → HR Interview → Technical Interview → Offer → Hired*.
- **Interview & Schedule Tracker:** Daily/weekly interview grid tracking meeting time completion against the current timestamp.
- **Weekly Trend Area / Line Visual:** Inflow volume across calendar weeks with dynamic min/max marker callouts.
