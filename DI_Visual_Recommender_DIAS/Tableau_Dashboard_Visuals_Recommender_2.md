_____________________________________________
## *Author*: AAVA
## *Created on*:   
## *Description*: Updated Tableau Dashboard Visuals Recommender for Service Reliability Support Data with comprehensive visual recommendations aligned to actual data structure
## *Version*: 2
## *Updated on*: 
_____________________________________________

# Tableau Dashboard Visuals Recommender
## Service Reliability Support Data Analytics System

## Executive Summary

This document provides comprehensive recommendations for designing Tableau dashboards for the Service Reliability Support Data Analytics System. The recommendations are based on the Gold layer data model containing 1 fact table (FACT_SUPPORT_ACTIVITY) and 3 dimension tables (DIM_DATE, DIM_SUPPORT_CATEGORY, DIM_USER), supporting comprehensive support analytics including ticket resolution, customer satisfaction, and operational efficiency metrics.

---

## 1. Visual Recommendations

### **1.1 Support Operations Dashboard**

#### **1.1.1 Support Ticket Volume Trend**
- **Data Element:** Daily/Weekly/Monthly Support Activity Volume
- **Recommended Visual:** Line Chart with dual axis
- **Data Fields:** 
  - Date (DIM_DATE.DATE_VALUE)
  - Ticket Count (COUNT(FACT_SUPPORT_ACTIVITY.SUPPORT_ACTIVITY_ID))
  - Resolution Status (FACT_SUPPORT_ACTIVITY.RESOLUTION_STATUS)
- **Query/Tableau Calculation:** 
  ```
  Daily Tickets: COUNT([Support Activity Id])
  Weekly Tickets: WINDOW_SUM(COUNT([Support Activity Id]), -6, 0)
  Monthly Tickets: WINDOW_SUM(COUNT([Support Activity Id]), -29, 0)
  Ticket Trend: (COUNT([Support Activity Id]) - LOOKUP(COUNT([Support Activity Id]), -7)) / LOOKUP(COUNT([Support Activity Id]), -7)
  ```
- **Interactivity:** Date range filter, Resolution status filter, Support category filter
- **Justification:** Line charts effectively show volume trends over time, dual axis allows comparison of different metrics
- **Optimization Tips:** Use extract with incremental refresh, add context filter for date range, index on DATE_KEY and SUPPORT_ACTIVITY_ID

#### **1.1.2 Resolution Time Analysis by Category**
- **Data Element:** Average Resolution Time by Support Category
- **Recommended Visual:** Horizontal Bar Chart with reference lines
- **Data Fields:** 
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - Support Subcategory (DIM_SUPPORT_CATEGORY.SUPPORT_SUBCATEGORY)
  - Resolution Time (AVG(FACT_SUPPORT_ACTIVITY.RESOLUTION_TIME_HOURS))
  - SLA Target (DIM_SUPPORT_CATEGORY.SLA_TARGET_HOURS)
- **Query/Tableau Calculation:** 
  ```
  Average Resolution Time: AVG([Resolution Time Hours])
  SLA Variance: AVG([Resolution Time Hours]) - AVG([Sla Target Hours])
  SLA Compliance Rate: COUNT(IF [Sla Met] = TRUE THEN 1 END) / COUNT([Support Activity Id])
  ```
- **Interactivity:** Support category filter, Priority level filter, Department responsible filter
- **Justification:** Horizontal bars accommodate long category names and enable easy comparison with SLA targets
- **Optimization Tips:** Add reference lines for SLA targets, color code by compliance status, sort by resolution time

#### **1.1.3 Customer Satisfaction Score Distribution**
- **Data Element:** Customer Satisfaction Analysis
- **Recommended Visual:** Histogram with box plot overlay
- **Data Fields:** 
  - Customer Satisfaction Score (FACT_SUPPORT_ACTIVITY.CUSTOMER_SATISFACTION_SCORE)
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - Resolution Status (FACT_SUPPORT_ACTIVITY.RESOLUTION_STATUS)
- **Query/Tableau Calculation:** 
  ```
  Average Satisfaction: AVG([Customer Satisfaction Score])
  Satisfaction Distribution: COUNT([Support Activity Id])
  High Satisfaction Rate: COUNT(IF [Customer Satisfaction Score] >= 4 THEN 1 END) / COUNT([Support Activity Id])
  ```
- **Interactivity:** Score range filter, Support category filter, Time period parameter
- **Justification:** Histogram shows distribution patterns, box plot reveals outliers and quartiles
- **Optimization Tips:** Create bins for satisfaction scores, use dual axis for different chart types

#### **1.1.4 First Contact Resolution Performance**
- **Data Element:** FCR Rate by Category and Agent
- **Recommended Visual:** Highlight Table (Heat Map style)
- **Data Fields:** 
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - User Role (DIM_USER.USER_ROLE)
  - FCR Flag (FACT_SUPPORT_ACTIVITY.FIRST_CONTACT_RESOLUTION_FLAG)
  - Ticket Count (COUNT(FACT_SUPPORT_ACTIVITY.SUPPORT_ACTIVITY_ID))
- **Query/Tableau Calculation:** 
  ```
  FCR Rate: COUNT(IF [First Contact Resolution Flag] = TRUE THEN 1 END) / COUNT([Support Activity Id])
  FCR Target: 0.75 (Parameter)
  FCR Performance: [FCR Rate] - [FCR Target]
  ```
- **Interactivity:** Category filter, User role filter, Date range parameter
- **Justification:** Heat map effectively shows performance patterns across multiple dimensions
- **Optimization Tips:** Use mark type as Square, apply color gradient based on FCR rate, show text labels

### **1.2 Operational Efficiency Dashboard**

#### **1.2.1 SLA Compliance Tracking**
- **Data Element:** SLA Performance Metrics
- **Recommended Visual:** Bullet Graph
- **Data Fields:** 
  - SLA Met (FACT_SUPPORT_ACTIVITY.SLA_MET)
  - SLA Target Hours (DIM_SUPPORT_CATEGORY.SLA_TARGET_HOURS)
  - Actual Resolution Time (FACT_SUPPORT_ACTIVITY.RESOLUTION_TIME_HOURS)
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
- **Query/Tableau Calculation:** 
  ```
  SLA Compliance Rate: COUNT(IF [Sla Met] = TRUE THEN 1 END) / COUNT([Support Activity Id])
  SLA Target Rate: 0.95 (Parameter)
  SLA Breach Hours: SUM([Sla Breach Hours])
  ```
- **Interactivity:** Category filter, Priority level filter, Department filter
- **Justification:** Bullet graphs show actual vs target performance with context ranges
- **Optimization Tips:** Use parameters for targets, add conditional formatting, synchronize axes

#### **1.2.2 Escalation and Reassignment Analysis**
- **Data Element:** Ticket Escalation Patterns
- **Recommended Visual:** Stacked Bar Chart
- **Data Fields:** 
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - Escalation Count (FACT_SUPPORT_ACTIVITY.ESCALATION_COUNT)
  - Reassignment Count (FACT_SUPPORT_ACTIVITY.REASSIGNMENT_COUNT)
  - Priority Level (FACT_SUPPORT_ACTIVITY.PRIORITY_LEVEL)
- **Query/Tableau Calculation:** 
  ```
  Escalation Rate: AVG([Escalation Count])
  Reassignment Rate: AVG([Reassignment Count])
  Complex Ticket Rate: COUNT(IF [Escalation Count] > 0 OR [Reassignment Count] > 0 THEN 1 END) / COUNT([Support Activity Id])
  ```
- **Interactivity:** Priority filter, Category filter, Date range filter
- **Justification:** Stacked bars show both volume and composition of escalations/reassignments
- **Optimization Tips:** Use different colors for escalations vs reassignments, add percentage labels

#### **1.2.3 Cost Analysis by Resolution Method**
- **Data Element:** Support Cost Efficiency
- **Recommended Visual:** Treemap
- **Data Fields:** 
  - Resolution Method (FACT_SUPPORT_ACTIVITY.RESOLUTION_METHOD)
  - Cost to Resolve (SUM(FACT_SUPPORT_ACTIVITY.COST_TO_RESOLVE))
  - Ticket Count (COUNT(FACT_SUPPORT_ACTIVITY.SUPPORT_ACTIVITY_ID))
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
- **Query/Tableau Calculation:** 
  ```
  Total Cost: SUM([Cost To Resolve])
  Average Cost per Ticket: SUM([Cost To Resolve]) / COUNT([Support Activity Id])
  Cost Efficiency Ratio: SUM([Cost To Resolve]) / SUM([Resolution Time Hours])
  ```
- **Interactivity:** Resolution method filter, Category filter, Cost range parameter
- **Justification:** Treemap shows both size (cost) and hierarchy (method/category) effectively
- **Optimization Tips:** Size by total cost, color by average cost per ticket, limit to top methods

#### **1.2.4 Agent Performance Metrics**
- **Data Element:** Individual Agent Performance
- **Recommended Visual:** Scatter Plot
- **Data Fields:** 
  - User Name (DIM_USER.USER_NAME)
  - Average Resolution Time (AVG(FACT_SUPPORT_ACTIVITY.RESOLUTION_TIME_HOURS))
  - Customer Satisfaction (AVG(FACT_SUPPORT_ACTIVITY.CUSTOMER_SATISFACTION_SCORE))
  - Ticket Volume (COUNT(FACT_SUPPORT_ACTIVITY.SUPPORT_ACTIVITY_ID))
- **Query/Tableau Calculation:** 
  ```
  Agent Efficiency Score: ([Average Satisfaction] * 2 + (1/[Average Resolution Time]) * 10) / 3
  Performance Quadrant: 
    IF [Average Satisfaction] > 4 AND [Average Resolution Time] < 24 THEN "High Performer"
    ELSEIF [Average Satisfaction] > 4 THEN "Quality Focused"
    ELSEIF [Average Resolution Time] < 24 THEN "Speed Focused"
    ELSE "Needs Improvement" END
  ```
- **Interactivity:** User role filter, Department filter, Performance quadrant filter
- **Justification:** Scatter plot reveals correlation between satisfaction and resolution time
- **Optimization Tips:** Use size encoding for ticket volume, color by performance quadrant

### **1.3 Customer Experience Dashboard**

#### **1.3.1 Customer Wait Time Analysis**
- **Data Element:** Customer Wait Time Distribution
- **Recommended Visual:** Box and Whisker Plot
- **Data Fields:** 
  - Customer Wait Time (FACT_SUPPORT_ACTIVITY.CUSTOMER_WAIT_TIME_HOURS)
  - Priority Level (FACT_SUPPORT_ACTIVITY.PRIORITY_LEVEL)
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
- **Query/Tableau Calculation:** 
  ```
  Average Wait Time: AVG([Customer Wait Time Hours])
  Wait Time Percentiles: PERCENTILE([Customer Wait Time Hours], 0.95)
  Wait Time SLA: 2 (Parameter for high priority), 8 (Parameter for normal priority)
  ```
- **Interactivity:** Priority filter, Category filter, Time period parameter
- **Justification:** Box plots show distribution and identify outliers in wait times
- **Optimization Tips:** Add reference lines for SLA targets, filter extreme outliers

#### **1.3.2 Ticket Reopening Analysis**
- **Data Element:** Ticket Quality and Reopening Patterns
- **Recommended Visual:** Area Chart
- **Data Fields:** 
  - Date (DIM_DATE.DATE_VALUE)
  - Reopened Count (FACT_SUPPORT_ACTIVITY.REOPENED_COUNT)
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - Root Cause Category (FACT_SUPPORT_ACTIVITY.ROOT_CAUSE_CATEGORY)
- **Query/Tableau Calculation:** 
  ```
  Reopening Rate: COUNT(IF [Reopened Count] > 0 THEN 1 END) / COUNT([Support Activity Id])
  Quality Score: 1 - [Reopening Rate]
  Trend Analysis: WINDOW_AVG([Reopening Rate], -6, 0)
  ```
- **Interactivity:** Category filter, Root cause filter, Date range filter
- **Justification:** Area chart shows trends in reopening patterns over time
- **Optimization Tips:** Stack by root cause category, add trend line

#### **1.3.3 Knowledge Base Utilization**
- **Data Element:** Self-Service and Knowledge Base Usage
- **Recommended Visual:** Horizontal Bar Chart with dual axis
- **Data Fields:** 
  - Support Category (DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - KB Articles Used (AVG(FACT_SUPPORT_ACTIVITY.KNOWLEDGE_BASE_ARTICLES_USED))
  - Self Service Available (DIM_SUPPORT_CATEGORY.SELF_SERVICE_AVAILABLE)
  - Resolution Time (AVG(FACT_SUPPORT_ACTIVITY.RESOLUTION_TIME_HOURS))
- **Query/Tableau Calculation:** 
  ```
  KB Utilization Rate: AVG([Knowledge Base Articles Used])
  Self-Service Adoption: COUNT(IF [Self Service Available] = TRUE THEN 1 END) / COUNT([Support Activity Id])
  KB Effectiveness: CORR([Knowledge Base Articles Used], [Resolution Time Hours])
  ```
- **Interactivity:** Category filter, Self-service filter, KB availability filter
- **Justification:** Shows relationship between KB usage and resolution efficiency
- **Optimization Tips:** Use dual axis to show KB usage vs resolution time, synchronize axes

---

## 2. Overall Dashboard Design

### **Layout Suggestions**

#### **Dashboard 1: Support Operations Overview (Executive Summary)**
- **Top Row:** KPI cards showing Total Tickets, Average Resolution Time, SLA Compliance Rate, Customer Satisfaction
- **Middle Row:** Support Ticket Volume Trend (left 60%), Resolution Time by Category (right 40%)
- **Bottom Row:** Customer Satisfaction Distribution (left 50%), FCR Performance Heat Map (right 50%)
- **Filters Panel:** Date range, Support category, Priority level (top banner)

#### **Dashboard 2: Operational Efficiency (Management)**
- **Top Row:** SLA Compliance Bullet Graphs (3 categories side by side)
- **Middle Row:** Escalation/Reassignment Analysis (left 50%), Cost Analysis Treemap (right 50%)
- **Bottom Row:** Agent Performance Scatter Plot (full width)
- **Filters Panel:** Department, User role, Resolution method (left sidebar)

#### **Dashboard 3: Customer Experience (Quality Focus)**
- **Top Row:** Wait Time KPIs, Reopening Rate, Quality Score
- **Middle Row:** Customer Wait Time Box Plot (left 50%), Ticket Reopening Trend (right 50%)
- **Bottom Row:** Knowledge Base Utilization Analysis (full width)
- **Filters Panel:** Priority level, Root cause category, Time period (right sidebar)

### **Performance Optimization**

#### **Extract Strategy**
- **Full Extract:** Refresh daily at 1 AM for dimension tables (DIM_DATE, DIM_SUPPORT_CATEGORY, DIM_USER)
- **Incremental Extract:** Refresh every 4 hours for FACT_SUPPORT_ACTIVITY based on LOAD_DATE
- **Partitioning:** Partition fact table extract by month for better performance
- **Aggregation:** Pre-aggregate daily and weekly summaries for trending analysis

#### **Query Optimization**
- **Context Filters:** Apply date range and support category as context filters
- **Data Source Filters:** Filter out test tickets and inactive users at source level
- **LOD Calculations:** Use INCLUDE LODs for agent-level calculations, avoid FIXED where possible
- **Indexing:** Ensure indexes on DATE_KEY, SUPPORT_CATEGORY_KEY, USER_KEY, and SUPPORT_ACTIVITY_ID

#### **Dashboard Performance**
- **Sheet Caching:** Enable automatic caching for dimension-heavy visualizations
- **Filter Actions:** Use filter actions between related sheets instead of global filters
- **Data Limits:** Show top 20 categories with "Others" grouping for long lists
- **Progressive Loading:** Load summary metrics first, detailed views on demand

### **Color Scheme**
- **Primary Colors:** Professional Blue (#1f4e79), White (#FFFFFF)
- **Secondary Colors:** Medium Gray (#595959), Light Blue (#d9e2f3)
- **Status Colors:** Green (#70ad47) for good performance, Red (#c5504b) for issues, Yellow (#ffc000) for warnings
- **Neutral Colors:** Light Gray (#f2f2f2) for backgrounds, Dark Gray (#404040) for text

### **Typography**
- **Dashboard Titles:** Tableau Book Bold, 16-18pt
- **Chart Titles:** Tableau Book Bold, 12-14pt
- **Body Text:** Tableau Book Regular, 10-11pt
- **KPI Numbers:** Tableau Book Bold, 20-28pt
- **Axis Labels:** Tableau Book Regular, 9-10pt

### **Interactive Elements**

| Element Type | Implementation | Purpose | Location |
|--------------|----------------|---------|----------|
| Date Range Filter | Parameter with relative date options (Last 30 days, Last quarter, YTD) | Time period analysis | Top banner of all dashboards |
| Support Category Filter | Multi-select dropdown with "All" option | Category-specific analysis | Filter panels |
| Priority Level Filter | Single-select dropdown (High, Medium, Low, All) | Priority-based filtering | Filter panels |
| User Role Filter | Multi-select for agent roles | Role-based performance analysis | Management dashboard |
| Department Filter | Dropdown for responsible departments | Department-wise analysis | All dashboards |
| Drill-down Hierarchy | Support Category > Subcategory > Individual tickets | Detailed investigation | Category fields |
| Drill-through Action | Click on metrics to see underlying ticket details | Root cause analysis | KPI cards and charts |
| Highlight Action | Hover to highlight related data across sheets | Data exploration | All visualizations |
| Filter Action | Click to filter other sheets in dashboard | Cross-filtering analysis | All charts |
| Parameter Control | Sliders for SLA targets and thresholds | Dynamic target setting | Management dashboard |
| Set Control | Dynamic grouping of categories or agents | Custom segmentation | Performance analysis |
| URL Action | Link to ticket system or user profiles | External system integration | Ticket ID and User fields |

---

## 3. Advanced Recommendations

### **3.1 Calculated Fields Library**

```sql
-- Time Intelligence
YTD Tickets: 
IF YEAR([Date Key]) = YEAR(TODAY()) AND [Date Key] <= TODAY() 
THEN [Support Activity Id] END

-- Performance Metrics
SLA Performance Score:
([SLA Compliance Rate] * 0.4 + [FCR Rate] * 0.3 + [Customer Satisfaction Score]/5 * 0.3)

-- Efficiency Calculations
Agent Productivity:
COUNT([Support Activity Id]) / SUM([Active Work Time Hours])

-- Quality Metrics
Ticket Quality Score:
(1 - [Reopening Rate]) * ([Customer Satisfaction Score]/5) * (IF [SLA Met] THEN 1 ELSE 0.5 END)

-- Cost Efficiency
Cost per Resolution Hour:
SUM([Cost To Resolve]) / SUM([Resolution Time Hours])

-- Trend Analysis
Week over Week Change:
(COUNT([Support Activity Id]) - LOOKUP(COUNT([Support Activity Id]), -7)) / LOOKUP(COUNT([Support Activity Id]), -7)
```

### **3.2 Alert Configuration**
- **SLA Alerts:** SLA compliance rate drops below 90%
- **Volume Alerts:** Daily ticket volume increases by >25% compared to previous week
- **Satisfaction Alerts:** Average customer satisfaction drops below 3.5
- **Cost Alerts:** Average cost per ticket increases by >20% month-over-month
- **Quality Alerts:** Ticket reopening rate exceeds 15%

### **3.3 Mobile Optimization**
- **Responsive Design:** Create mobile-specific layouts focusing on key KPIs
- **Touch Interactions:** Optimize filters and actions for touch navigation
- **Simplified Views:** Single-metric focus for mobile consumption
- **Offline Capability:** Enable offline viewing for critical performance metrics

### **3.4 Data Governance**
- **Row-Level Security:** Implement user-based filtering (agents see only their tickets)
- **Column-Level Security:** Mask sensitive cost and salary information
- **Audit Trail:** Track dashboard usage and filter selections
- **Data Quality Monitoring:** Automated checks for data completeness and accuracy

---

## 4. Implementation Roadmap

### **Phase 1: Data Foundation (Week 1)**
- Set up Tableau data connections to Excel/database sources
- Create and test data relationships between fact and dimension tables
- Build core calculated fields and parameters
- Establish extract refresh schedules

### **Phase 2: Core Visualizations (Week 2)**
- Develop Support Operations Dashboard with key KPIs
- Create basic charts for volume, resolution time, and satisfaction
- Implement primary filters and basic interactivity
- Test performance with full dataset

### **Phase 3: Advanced Analytics (Week 3)**
- Build Operational Efficiency Dashboard with SLA and cost analysis
- Develop Customer Experience Dashboard with quality metrics
- Add advanced calculated fields and LOD expressions
- Implement drill-down and drill-through capabilities

### **Phase 4: Enhancement & Optimization (Week 4)**
- Add mobile-responsive layouts
- Configure alerts and subscriptions
- Performance tuning and extract optimization
- User acceptance testing and feedback incorporation

### **Phase 5: Deployment & Training (Week 5)**
- Production deployment with security settings
- User training and documentation
- Establish governance and maintenance procedures
- Monitor adoption and gather feedback

---

## 5. Success Metrics

- **User Adoption:** 85% of support managers and 60% of agents actively using dashboards within 30 days
- **Performance:** Dashboard load times < 3 seconds for 95% of views
- **Data Accuracy:** 99.5% accuracy validation against source ticket system
- **Business Impact:** 15% improvement in SLA compliance within 60 days
- **User Satisfaction:** Average rating > 4.2/5.0 in user feedback surveys
- **Self-Service:** 40% reduction in ad-hoc reporting requests

---

## 6. Maintenance and Governance

### **Regular Maintenance Tasks**
- **Daily:** Monitor extract refresh success and data quality
- **Weekly:** Review dashboard performance metrics and user feedback
- **Monthly:** Update calculated fields and add new requirements
- **Quarterly:** Performance optimization and capacity planning

### **Governance Framework**
- **Data Steward:** Designated owner for data quality and business rules
- **Technical Owner:** Responsible for Tableau server maintenance and optimization
- **Business Users:** Regular feedback sessions and requirement gathering
- **Change Management:** Formal process for dashboard modifications

---

**Output URL:** https://github.com/DIAscendion/TableauReport_Generation/tree/main/DI_Visual_Recommender_DIAS

**Pipeline ID:** 14364