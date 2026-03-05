_____________________________________________
## *Author*: AAVA
## *Created on*:   
## *Description*: Tableau Dashboard Visuals Recommender for Zoom Platform Analytics System with comprehensive visual recommendations for usage, support, and revenue analytics
## *Version*: 1
## *Updated on*: 
_____________________________________________

# Tableau Dashboard Visuals Recommender
## Zoom Platform Analytics System

## Executive Summary

This document provides comprehensive recommendations for designing Tableau dashboards for the Zoom Platform Analytics System. The recommendations are based on the Gold layer data model containing 6 dimension tables and 4 fact tables, supporting three primary business reporting areas: Platform Usage & Adoption, Service Reliability & Support, and Revenue & License Analysis.

---

## 1. Visual Recommendations

### **1.1 Platform Usage & Adoption Dashboard**

#### **1.1.1 Daily/Weekly/Monthly Active Users Trend**
- **Data Element:** Active Users Over Time
- **Recommended Visual:** Line Chart with dual axis
- **Data Fields:** 
  - Date (GO_DIM_DATE.DATE_VALUE)
  - DAU Count (COUNT(DISTINCT GO_FACT_MEETING_ACTIVITY.HOST_USER_DIM_ID))
  - WAU Count (7-day rolling window)
  - MAU Count (30-day rolling window)
- **Query/Tableau Calculation:** 
  ```
  DAU: COUNT(DISTINCT [Host User Dim Id])
  WAU: WINDOW_COUNT(DISTINCT [Host User Dim Id], -6, 0)
  MAU: WINDOW_COUNT(DISTINCT [Host User Dim Id], -29, 0)
  ```
- **Interactivity:** Date range filter, Plan type filter, Geographic region filter
- **Justification:** Line charts effectively show trends over time, dual axis allows comparison of different time periods
- **Optimization Tips:** Use extract with incremental refresh, add context filter for date range, index on DATE_VALUE and HOST_USER_DIM_ID

#### **1.1.2 Total Meeting Minutes Analysis**
- **Data Element:** Meeting Minutes by Time Period and Plan Type
- **Recommended Visual:** Stacked Bar Chart
- **Data Fields:** 
  - Date (GO_DIM_DATE.MONTH_NAME)
  - Plan Type (GO_DIM_USER.PLAN_TYPE)
  - Total Minutes (SUM(GO_FACT_MEETING_ACTIVITY.ACTUAL_DURATION_MINUTES))
- **Query/Tableau Calculation:** 
  ```
  Total Meeting Minutes: SUM([Actual Duration Minutes])
  Average Meeting Duration: AVG([Actual Duration Minutes])
  ```
- **Interactivity:** Plan type filter, Date hierarchy drill-down (Year > Quarter > Month)
- **Justification:** Stacked bars show both total volume and composition by plan type
- **Optimization Tips:** Pre-aggregate at monthly level, use context filter for plan type

#### **1.1.3 Feature Adoption Rate Analysis**
- **Data Element:** Feature Usage Distribution and Adoption
- **Recommended Visual:** Highlight Table (Heat Map style)
- **Data Fields:** 
  - Feature Name (GO_DIM_FEATURE.FEATURE_NAME)
  - Feature Category (GO_DIM_FEATURE.FEATURE_CATEGORY)
  - Usage Count (SUM(GO_FACT_FEATURE_USAGE.USAGE_COUNT))
  - Adoption Rate (calculated field)
- **Query/Tableau Calculation:** 
  ```
  Adoption Rate: COUNT(DISTINCT [User Dim Id]) / TOTAL(COUNT(DISTINCT [User Dim Id]))
  Feature Penetration: COUNT(DISTINCT [User Dim Id]) / [Total Active Users]
  ```
- **Interactivity:** Feature category filter, User segment filter, Time period parameter
- **Justification:** Heat map effectively shows patterns across multiple dimensions
- **Optimization Tips:** Use mark type as Square, apply color gradient, limit to top 20 features

#### **1.1.4 New User Sign-ups Trend**
- **Data Element:** User Registration Trends
- **Recommended Visual:** Area Chart with reference line
- **Data Fields:** 
  - Registration Date (GO_DIM_USER.REGISTRATION_DATE)
  - New Users Count (COUNT(GO_DIM_USER.USER_DIM_ID))
  - Plan Type (GO_DIM_USER.PLAN_TYPE)
- **Query/Tableau Calculation:** 
  ```
  New Users: COUNT([User Dim Id])
  Growth Rate: (COUNT([User Dim Id]) - LOOKUP(COUNT([User Dim Id]), -1)) / LOOKUP(COUNT([User Dim Id]), -1)
  ```
- **Interactivity:** Plan type filter, Geographic region filter, Industry sector filter
- **Justification:** Area chart shows cumulative growth with clear trend visualization
- **Optimization Tips:** Add reference line for targets, use continuous date axis

### **1.2 Service Reliability & Support Dashboard**

#### **1.2.1 Ticket Volume by Type**
- **Data Element:** Support Ticket Distribution
- **Recommended Visual:** Horizontal Bar Chart
- **Data Fields:** 
  - Support Category (GO_DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - Ticket Count (COUNT(GO_FACT_SUPPORT_METRICS.SUPPORT_METRICS_ID))
  - Priority Level (GO_DIM_SUPPORT_CATEGORY.PRIORITY_LEVEL)
- **Query/Tableau Calculation:** 
  ```
  Ticket Count: COUNT([Support Metrics Id])
  Percentage of Total: COUNT([Support Metrics Id]) / TOTAL(COUNT([Support Metrics Id]))
  ```
- **Interactivity:** Priority level filter, Date range filter, Support subcategory drill-down
- **Justification:** Horizontal bars accommodate long category names and enable easy comparison
- **Optimization Tips:** Sort by count descending, limit to top 15 categories

#### **1.2.2 Resolution Time Analysis**
- **Data Element:** Ticket Resolution Performance
- **Recommended Visual:** Box and Whisker Plot
- **Data Fields:** 
  - Support Category (GO_DIM_SUPPORT_CATEGORY.SUPPORT_CATEGORY)
  - Resolution Time (GO_FACT_SUPPORT_METRICS.RESOLUTION_TIME_HOURS)
  - SLA Target (GO_DIM_SUPPORT_CATEGORY.SLA_TARGET_HOURS)
- **Query/Tableau Calculation:** 
  ```
  Average Resolution Time: AVG([Resolution Time Hours])
  SLA Compliance Rate: COUNT(IF [Sla Met] = TRUE THEN 1 END) / COUNT([Support Metrics Id])
  ```
- **Interactivity:** Category filter, Priority level filter, Date range parameter
- **Justification:** Box plots show distribution and outliers in resolution times
- **Optimization Tips:** Add reference line for SLA targets, filter out extreme outliers

#### **1.2.3 First Contact Resolution Rate**
- **Data Element:** Support Efficiency Metrics
- **Recommended Visual:** KPI Cards with Bullet Graph
- **Data Fields:** 
  - FCR Rate (GO_FACT_SUPPORT_METRICS.FIRST_CONTACT_RESOLUTION)
  - Customer Satisfaction (AVG(GO_FACT_SUPPORT_METRICS.CUSTOMER_SATISFACTION_SCORE))
- **Query/Tableau Calculation:** 
  ```
  FCR Rate: COUNT(IF [First Contact Resolution] = TRUE THEN 1 END) / COUNT([Support Metrics Id])
  Target FCR: 0.75 (Parameter)
  ```
- **Interactivity:** Department filter, Time period parameter
- **Justification:** KPI cards provide immediate visibility to key metrics
- **Optimization Tips:** Use bullet graph to show actual vs target, add conditional formatting

### **1.3 Revenue and License Analysis Dashboard**

#### **1.3.1 Monthly Recurring Revenue Trend**
- **Data Element:** MRR Growth Analysis
- **Recommended Visual:** Line Chart with dual axis
- **Data Fields:** 
  - Transaction Date (GO_DIM_DATE.DATE_VALUE)
  - MRR Impact (SUM(GO_FACT_REVENUE_EVENTS.MRR_IMPACT))
  - License Type (GO_DIM_LICENSE.LICENSE_TYPE)
- **Query/Tableau Calculation:** 
  ```
  MRR: SUM([Mrr Impact])
  MRR Growth Rate: (SUM([Mrr Impact]) - LOOKUP(SUM([Mrr Impact]), -1)) / LOOKUP(SUM([Mrr Impact]), -1)
  ```
- **Interactivity:** License type filter, Geographic region filter, Date range parameter
- **Justification:** Line chart shows revenue trends with growth rate on secondary axis
- **Optimization Tips:** Use continuous date axis, add trend line, synchronize dual axis

#### **1.3.2 Revenue Distribution by Plan Type**
- **Data Element:** Revenue Composition Analysis
- **Recommended Visual:** Stacked Area Chart
- **Data Fields:** 
  - Transaction Date (GO_DIM_DATE.DATE_VALUE)
  - Plan Type (GO_DIM_USER.PLAN_TYPE)
  - Net Amount (SUM(GO_FACT_REVENUE_EVENTS.NET_AMOUNT))
- **Query/Tableau Calculation:** 
  ```
  Revenue: SUM([Net Amount])
  Revenue Percentage: SUM([Net Amount]) / TOTAL(SUM([Net Amount]))
  ```
- **Interactivity:** Plan type filter, Currency filter, Geographic region filter
- **Justification:** Stacked area shows both total revenue and composition over time
- **Optimization Tips:** Use percentage stacking option, limit to major plan types

#### **1.3.3 License Utilization Analysis**
- **Data Element:** License Usage and Expiration
- **Recommended Visual:** Gantt Chart
- **Data Fields:** 
  - License Type (GO_DIM_LICENSE.LICENSE_TYPE)
  - Start Date (GO_DIM_LICENSE.EFFECTIVE_START_DATE)
  - End Date (GO_DIM_LICENSE.EFFECTIVE_END_DATE)
  - User Count (COUNT(GO_DIM_USER.USER_DIM_ID))
- **Query/Tableau Calculation:** 
  ```
  License Duration: DATEDIFF('day', [Effective Start Date], [Effective End Date])
  Utilization Rate: COUNT([User Dim Id]) / [Max Participants]
  ```
- **Interactivity:** License type filter, Expiration date range filter
- **Justification:** Gantt chart shows license timelines and overlaps effectively
- **Optimization Tips:** Color code by utilization rate, add alerts for near-expiry

#### **1.3.4 Customer Lifetime Value Analysis**
- **Data Element:** CLV and Churn Risk
- **Recommended Visual:** Scatter Plot
- **Data Fields:** 
  - Customer Lifetime Value (AVG(GO_FACT_REVENUE_EVENTS.CUSTOMER_LIFETIME_VALUE))
  - Churn Risk Score (AVG(GO_FACT_REVENUE_EVENTS.CHURN_RISK_SCORE))
  - Plan Type (GO_DIM_USER.PLAN_TYPE)
  - Company Size (calculated from GO_DIM_USER.COMPANY)
- **Query/Tableau Calculation:** 
  ```
  Average CLV: AVG([Customer Lifetime Value])
  Risk Segment: IF [Churn Risk Score] > 7 THEN "High Risk" 
                ELSEIF [Churn Risk Score] > 4 THEN "Medium Risk" 
                ELSE "Low Risk" END
  ```
- **Interactivity:** Plan type filter, Risk segment filter, Company size parameter
- **Justification:** Scatter plot reveals correlation between CLV and churn risk
- **Optimization Tips:** Use size encoding for revenue, color by plan type

---

## 2. Overall Dashboard Design

### **Layout Suggestions**

#### **Dashboard 1: Platform Usage & Adoption (Executive Summary)**
- **Top Row:** KPI cards showing DAU, WAU, MAU, Total Meeting Minutes
- **Middle Row:** Active Users Trend (left 60%), Feature Adoption Heat Map (right 40%)
- **Bottom Row:** Meeting Minutes by Plan Type (left 50%), New User Sign-ups (right 50%)
- **Filters Panel:** Date range, Plan type, Geographic region (left sidebar)

#### **Dashboard 2: Service Reliability & Support (Operational)**
- **Top Row:** KPI cards for FCR Rate, Average Resolution Time, SLA Compliance
- **Middle Row:** Ticket Volume by Type (left 50%), Resolution Time Box Plot (right 50%)
- **Bottom Row:** Ticket Trend Over Time (full width)
- **Filters Panel:** Priority level, Support category, Date range (top banner)

#### **Dashboard 3: Revenue & License Analysis (Financial)**
- **Top Row:** MRR, ARR, Revenue Growth Rate KPIs
- **Middle Row:** MRR Trend (left 60%), Revenue by Plan Type (right 40%)
- **Bottom Row:** License Utilization Gantt (left 50%), CLV vs Churn Risk (right 50%)
- **Filters Panel:** License type, Geographic region, Currency (right sidebar)

### **Performance Optimization**

#### **Extract Strategy**
- **Full Extract:** Refresh daily at 2 AM for dimension tables
- **Incremental Extract:** Refresh hourly for fact tables based on LOAD_DATE
- **Partitioning:** Partition extracts by date for large fact tables
- **Aggregation:** Pre-aggregate monthly and quarterly summaries

#### **Query Optimization**
- **Context Filters:** Apply date range and plan type as context filters
- **Data Source Filters:** Filter out test accounts and inactive users at source
- **LOD Calculations:** Minimize use of FIXED LODs, prefer INCLUDE/EXCLUDE
- **Indexing:** Ensure indexes on join keys and frequently filtered columns

#### **Dashboard Performance**
- **Sheet Caching:** Enable automatic sheet caching for static visualizations
- **Filter Actions:** Use filter actions instead of global filters where possible
- **Limit Data:** Show top N results with "Others" category for long lists
- **Progressive Loading:** Load summary first, details on demand

### **Color Scheme**
- **Primary Colors:** Zoom Blue (#2D8CFF), White (#FFFFFF)
- **Secondary Colors:** Gray (#6B7280), Light Blue (#E0F2FE)
- **Accent Colors:** Green (#10B981) for positive metrics, Red (#EF4444) for alerts
- **Neutral Colors:** Light Gray (#F9FAFB) for backgrounds

### **Typography**
- **Headers:** Tableau Book Bold, 14-16pt
- **Body Text:** Tableau Book Regular, 10-12pt
- **KPI Numbers:** Tableau Book Bold, 18-24pt
- **Axis Labels:** Tableau Book Regular, 9-10pt

### **Interactive Elements**

| Element Type | Implementation | Purpose | Location |
|--------------|----------------|---------|----------|
| Date Range Filter | Parameter with relative date options | Time period selection | Top of each dashboard |
| Plan Type Filter | Multi-select dropdown | User segment analysis | Left sidebar |
| Geographic Filter | Map-based or dropdown | Regional analysis | Filter panel |
| Drill-down Hierarchy | Date: Year > Quarter > Month | Temporal analysis | Date fields |
| Drill-through Action | Click metric to see details | Detailed investigation | KPI cards |
| Highlight Action | Hover to highlight related data | Data exploration | All charts |
| URL Action | Link to external systems | Additional context | User/Company fields |
| Filter Action | Click to filter other sheets | Cross-filtering | All visualizations |
| Parameter Control | Slider for thresholds | Dynamic analysis | Calculation inputs |
| Set Control | Dynamic grouping | Custom segmentation | Dimension fields |

---

## 3. Advanced Recommendations

### **3.1 Calculated Fields Library**

```sql
-- Time Intelligence
YTD Revenue: 
IF YEAR([Transaction Date]) = YEAR(TODAY()) AND [Transaction Date] <= TODAY() 
THEN [Net Amount] END

-- Cohort Analysis
User Cohort Month: 
DATETRUNC('month', [Registration Date])

-- Growth Metrics
MoM Growth Rate: 
(SUM([Net Amount]) - LOOKUP(SUM([Net Amount]), -1)) / LOOKUP(SUM([Net Amount]), -1)

-- Segmentation
User Segment: 
IF [Customer Lifetime Value] > 10000 THEN "Enterprise"
ELSEIF [Customer Lifetime Value] > 1000 THEN "Business"
ELSE "SMB" END
```

### **3.2 Alert Configuration**
- **Revenue Alerts:** MRR decline > 5% month-over-month
- **Support Alerts:** SLA breach rate > 15%
- **Usage Alerts:** DAU decline > 10% week-over-week
- **License Alerts:** Licenses expiring within 30 days

### **3.3 Mobile Optimization**
- **Responsive Design:** Create mobile-specific layouts for key dashboards
- **Touch Interactions:** Optimize for touch-based navigation
- **Simplified Views:** Reduce complexity for mobile consumption
- **Offline Capability:** Enable offline viewing for critical metrics

### **3.4 Data Governance**
- **Row-Level Security:** Implement user-based data filtering
- **Column-Level Security:** Mask sensitive financial data
- **Audit Trail:** Track dashboard usage and data access
- **Data Lineage:** Document data source to visualization mapping

---

## 4. Implementation Roadmap

### **Phase 1: Foundation (Weeks 1-2)**
- Set up data connections and basic extracts
- Create core calculated fields and parameters
- Build KPI cards and basic visualizations

### **Phase 2: Core Dashboards (Weeks 3-4)**
- Develop Platform Usage & Adoption dashboard
- Implement Service Reliability & Support dashboard
- Create Revenue & License Analysis dashboard

### **Phase 3: Enhancement (Weeks 5-6)**
- Add advanced interactivity and drill-down capabilities
- Implement mobile-responsive layouts
- Configure alerts and subscriptions

### **Phase 4: Optimization (Weeks 7-8)**
- Performance tuning and extract optimization
- User acceptance testing and feedback incorporation
- Documentation and training materials

---

## 5. Success Metrics

- **Adoption Rate:** 80% of target users actively using dashboards within 30 days
- **Performance:** Dashboard load times < 5 seconds for 95% of views
- **Accuracy:** Data validation with 99.9% accuracy compared to source systems
- **User Satisfaction:** Average rating > 4.0/5.0 in user feedback surveys

---

**Output URL:** https://github.com/DIAscendion/TableauReport_Generation/tree/main/DI_Visual_Recommender_DIAS

**Pipeline ID:** 14364