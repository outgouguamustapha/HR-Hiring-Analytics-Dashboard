# HR-Hiring-Analytics-Dashboard

## 📌 Overview
This project presents a **Power BI HR Hiring Analytics Dashboard** designed to give a complete overview of hiring performance, project progress, and recruitment efficiency.
It provides HR managers, recruiters, and leadership teams with clear, actionable insights into:

* Hiring progress
* Candidate funnel stages
* Applications over time
* Recruiter workload
* Timeline and recruitment duration
* Source effectiveness
* Key hiring KPIs

The dashboard is built using **Power BI**, **DAX**, and **a well-structured star schema**, following best practices in business intelligence.

## 📊 Dashboard Preview

![Banner](Image/Overview2.png)

---

## 🎯 **Objectives**

The main goals of this dashboard are:

* Provide a **centralized view of hiring activities**
* Track the **hiring rate** for each position
* Identify **bottlenecks** in the recruitment pipeline
* Visualize **weekly applications trends**
* Monitor the **timeline and duration** of each hiring project
* Calculate key metrics such as:

  * Time to hire (start → planned end)
  * Total applications
  * Max/Min applications
  * Hiring rate (Hired/Offer)
  * Funnel conversion
  * Recruitment cost

This dashboard helps organizations improve **recruitment efficiency**, reduce **time to hire**, and make **data-driven HR decisions**.

---

## 🧱 **Data Model**

The project uses a **star schema** with the following tables:

### **Fact Table**

* `fctCandidates` — candidate applications, stages, statuses

### **Dimension Tables**

* `dimRecruitment` — recruitment project metadata
* `dimEmployees` — employee details (used for HR KPIs)
* `dimPosition` — job position metadata
* `DimDate` — calendar table
* Other supporting tables (costs, meetings)

### ⭐ Data Modeling Best Practices Used:

* Surrogate keys
* One-to-many relationships
* Fact/dimension separation
* Date intelligence
* Proper granularity alignment

---

## 📊 **Key Features & Visuals**

### **✨ 1. Hiring Rate KPI (Hired / Offer)**

A clean ratio indicator that shows project progress.
Example: **1/2 Hired**

### **✨ 2. Applications by Week**

Line chart showing weekly application fluctuations with:

* Max marker
* Min marker
* Total applications

### **✨ 3. Application Stage Overview**

A horizontal stage-based visual showing progress through:

* Intake & Approval
* Sourcing & Screening
* Interviews
* Offer & Closure

### **✨ 4. Hiring Funnel**

Sequential funnel visual representing conversion across recruitment stages.

### **✨ 5. Position Summary Card**

Displays:

* Hiring Manager
* Recruiter info
* Recruitment cost
* Number of applications
* Seniority, department, office

### **✨ 6. Timeline Tracking**

Shows the number of days between the start and planned end dates.

### **✨ 7. Recruitment Meetings**

Daily meeting schedule with controls for Yesterday / Today / Tomorrow.

---

## 🛠 **Tools & Technologies**

* **Power BI Desktop**
* **DAX (Data Analysis Expressions)**
* **Power Query**
* **Excel**
* **Star Schema Modeling**
* **Figma**

## 🚀 How to View

### Interactive Dashboard

Experience the live dashboard with full interactivity:

**[➡️ View Live Dashboard](https://app.powerbi.com/view?r=eyJrIjoiMjBmYTFjYWItODI3Yy00YWM2LTljNjItYmNjNjliNzA5MGY4IiwidCI6Ijg0ZDI3MGQyLTBiYzUtNGQ1NS1iZjBhLTI3NGYxYTU3NmNiZiJ9)**

---

## 🚀 **How to Use**

Explore the interactive dashboard : 

---

## 📈 **Potential Improvements**

Planned enhancements include:

* Add "Time in Stage" metrics
* Add recruiter performance benchmarking
* Add dynamic parameters for switching between roles
* Add AI-generated insights using Power BI Smart Narrative
* Add comparison vs last year

-----

## 💼 About This Project

This dashboard showcases advanced data analysis and visualization capabilities in Power BI, demonstrating:

- **Data Modeling**: Efficient data structure design for optimal performance
- **DAX Proficiency**: Complex calculations for KPIs and metrics
- **Visual Design**: Clean, intuitive, and professional dashboard layout
- **Business Intelligence**: Translation of raw data into actionable insights
- **User Experience**: Interactive elements that enhance data exploration

The project highlights skills in data transformation, business analytics, and creating executive-level reporting solutions.

---

## 👤 Author

**OUTGOUGUA MUSTAPHA**

Data Analyst | Power BI Developer | Business Intelligence Specialist

- 💼 [LinkedIn](https://www.linkedin.com/in/mustapha-outgougua/)
- 🐙 [GitHub](https://github.com/outgouguamustapha)
- 📧 Open to collaboration and opportunities

---


<div align="center">

**If you found this project insightful, please consider giving it a ⭐!**

Made with ❤️ and Power BI

</div>


