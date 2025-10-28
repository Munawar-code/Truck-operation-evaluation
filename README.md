# Truck-operation-evaluation
Overview
This Power BI dashboard provides a comprehensive analysis system for evaluating fleet trucks and making data-driven decisions on whether to Keep, Sell, or Inspect each vehicle. The dashboard integrates financial, operational, maintenance, and resale data to generate automated recommendations.
Purpose: Evaluate truck performance across multiple dimensions to determine optimal fleet management decisions while maximizing profitability and minimizing risk.

Dashboard Objectives
The dashboard answers critical business questions:

Are we getting value from this truck?
Will keeping it cost more than it's worth?
Is there enough data to decide, or does it need inspection?
How much have we gained/lost per truck?
What's the predicted performance for the next X weeks?


Dashboard Structure
Single Page Dashboard: "Truck Analysis Dashboard"
Contains 23 visualizations including:

9 KPI Cards - Key performance metrics
5 Data Tables - Detailed truck-level information
3 Slicers - Interactive filters
3 Text Boxes - Instructions and context
1 Clustered Column Chart - Visual performance comparison
2 Additional Visuals - Supporting analytics


Data Sources (6 Tables)
The dashboard integrates data from six key sources, as specified in the original requirements:
1. truck_finance
Purpose: Financial obligations and ownership details
Key Fields:

Buyout Price - Current buyout cost
Purchase Value - Original acquisition price
Remaining Balance - Outstanding debt
Monthly Cost - Regular payment amount
Ownership Category - Lease Purchase / Owned / Other
Truck Status - Active/Inactive status

Decision Impact: Determines if we owe more than the truck is worth

2. repairs (MaintenancePO_truck)
Purpose: Maintenance history and repair costs
Key Metrics:

Total Shop Visits - Frequency of repairs
Avg Cost Per Visit - Average repair expense
Avg Days Between Visits - Reliability indicator
CPM (Cost Per Mile) - Efficiency metric
Miles Since Last Repair - Current reliability
Mile Bracket - Categorization by odometer range

Decision Impact: High repair costs + frequent visits = potential sell candidate

3. distanceTraveled (Vehicle Distance Traveled - Daily Data)
Purpose: Daily usage patterns and activity tracking
Key Metrics:

Daily Miles (Clean) - Daily mileage tracking
Miles Last 10 Weeks - Recent activity summary
Active Days (Clean) - Days in operation
Movement Consistency - Usage pattern stability
Consistency Status - Categorical consistency rating
unit_id - Truck identifier

Decision Impact: Low mileage + inconsistent use = inspect or sell

4. truckData (Truck Odometer Data - Weekly Snapshots)
Purpose: Weekly odometer readings tied to payroll periods
Key Metrics:

Total Payroll Miles - Mileage accumulated during pay periods
Mileage Difference - Week-over-week changes
Mileage Status - Validation flag (matches expected vs actual)
pay_start date hierarchy (Year, Quarter, Month, Day)
unit_id - Truck identifier

Decision Impact: Cross-validates mileage data for accuracy

5. StubData (Payroll/Usage Records)
Purpose: Revenue generation and assignment history
Key Metrics:

Total Truck Earnings - Revenue generated
Truck Assignment Count - How many times assigned
Truck Regularity - Assignment frequency consistency
Truck - Truck identifier

Decision Impact: Low earnings + rare assignments = underperforming asset

6. tp_listings (Truck Paper - Resale Values)
Purpose: Market value estimation and sale scenarios
Key Metrics:

Estimated Resale Value - Current market value
Gain or Loss - Calculated profit/loss if sold now
Sale Outcome - Net result categorization
truck_brand, truck_model, truck_year - Vehicle specifications
id - Listing identifier

Decision Impact: If resale value > remaining balance = consider selling

Key Calculated Measures & Logic
The dashboard includes several calculated fields that drive the recommendation engine:
Financial Metrics

Gain or Loss: Estimated Resale Value - Remaining Balance
Sale Outcome: Categorizes as Profit, Loss, or Break-Even

Operational Metrics

CPM (Cost Per Mile): Total Repair Costs / Total Miles
Movement Consistency: Analyzes variance in daily mileage
Truck Regularity: Assignment pattern stability score

Cross-Validation

Mileage Status: Compares distanceTraveled daily data vs truckData weekly snapshots
Mileage Difference: Validates data integrity across sources


Recommendation Logic
The dashboard applies multi-factor decision criteria:
KEEP Recommendations
Criteria:

Low to moderate mileage
Consistent usage patterns
Repair costs within acceptable range
Currently generating positive revenue
Resale value lower than remaining loan balance (would lose money selling)
Regular driver assignments

Logic: "Truck is performing well and selling would create a financial loss"

SELL Recommendations
Criteria:

High mileage (approaching end of useful life)
High repair costs relative to mileage (CPM elevated)
Low or inconsistent usage
Resale value higher than remaining balance (can profit from sale)
Minimal recent earnings
Approaching major maintenance milestones

Logic: "Truck is costing more than it generates, and we can exit profitably"

🟡 INSPECT (Borderline) Recommendations
Criteria:

Metrics are close to thresholds
Recent drop in usage without clear reason
Mileage discrepancies between data sources
Moderate repair costs but declining reliability
Inconsistent assignment patterns
Missing or incomplete data

Logic: "Need manual review to make confident decision"

Automated Data Processing
Data Update Workflow

Import - Six source tables are refreshed from data connections
Transform - Power Query cleans and standardizes data
Calculate - DAX measures compute KPIs and derived metrics
Cross-Validate - Mileage data is verified across sources
Score - Each truck is evaluated against decision criteria
Recommend - Automated Keep/Sell/Inspect labels are generated
Visualize - Dashboard updates with latest recommendations

Automation Features

Self-Updating Tables: All visuals refresh when data is updated
Dynamic Calculations: Measures recalculate automatically
Conditional Formatting: Visual indicators (red/yellow/green) based on thresholds
Date Intelligence: Time-based analysis using date hierarchies

Overview Review

Check the KPI cards at the top for fleet-wide metrics
Review overall Buyout Price, Total Earnings, and Resale Values

Use Slicers for Filtering

Filter by Ownership Category (Lease Purchase vs Owned)
Filter by Truck Status (Active/Inactive)
Filter by specific Mile Brackets or date ranges

Analyze Individual Trucks

Review the detailed tables showing truck-by-truck breakdowns
Look at the Gain or Loss column to see financial impact
Check Movement Consistency and Shop Visits columns

Compare Performance

Use the clustered column chart to visually compare trucks
Identify outliers (very high costs or very low usage)

Make Decisions

Review trucks flagged for SELL - validate resale timing
Investigate trucks marked INSPECT - gather additional context
Confirm KEEP trucks are being utilized properly


Technical Implementation Details
Data Model Relationships
The dashboard uses a star schema with relationships between:

truck_finance ↔ repairs (via unit_id)
truck_finance ↔ distanceTraveled (via unit_id)
truck_finance ↔ truckData (via unit_id)
truck_finance ↔ StubData (via Truck identifier)
truck_finance ↔ tp_listings (via truck specifications)

Key DAX Measures (Examples)
DAXTotal Shop Visits = COUNTROWS(repairs)

Miles Last 10 Weeks = 
CALCULATE(
    SUM(distanceTraveled[distance]),
    DATESINPERIOD(distanceTraveled[Date], MAX(distanceTraveled[Date]), -10, WEEK)
)

Gain or Loss = 
[Estimated Resale Value] - [Remaining Balance]

Sale Outcome = 
SWITCH(
    TRUE(),
    [Gain or Loss] > 0, "Profit",
    [Gain or Loss] < 0, "Loss",
    "Break-Even"
)
Visualization Best Practices

Cards: Used for single-value KPIs with conditional formatting
Tables: Display detailed records with multiple sorting options
Slicers: Enable dynamic filtering across all visuals
Column Charts: Compare metrics across multiple trucks visually


Key Performance Indicators (KPIs)
The dashboard tracks these critical metrics:
Financial KPIs

Total Buyout Price (Lease Purchase vehicles)
Total Purchase Value
Total Remaining Balance
Net Gain/Loss Potential

Operational KPIs

Total Truck Earnings
Truck Assignment Count
Active Days vs Inactive Days
Average Daily Miles

Maintenance KPIs

Total Shop Visits
Average Cost Per Visit
Average Days Between Visits
Cost Per Mile (CPM)

Market Value KPIs

Estimated Resale Value (aggregated)
Gain or Loss on Sale
Sale Outcome Distribution


Data Quality Validation
The dashboard includes built-in data quality checks:
 Mileage Validation

Compares distanceTraveled (daily) vs truckData (weekly)
Flags discrepancies with Mileage Status indicator
Alerts when data sources don't align

Consistency Checks

Monitors usage patterns for sudden changes
Identifies trucks with irregular assignment patterns
Detects missing data points

Cross-Reference Validation

Verifies truck identifiers across all six tables
Ensures financial data aligns with operational data
Validates repair costs against mileage accumulation


Future Enhancements & Recommendations
Potential Improvements

Predictive Analytics - Add machine learning models to forecast:

Future repair costs based on mileage trends
Revenue projections for next 12 weeks
Optimal sale timing predictions


Benchmarking - Compare trucks against:

Fleet averages
Industry standards by make/model
Historical performance baselines


Alert System - Automated notifications for:

Trucks crossing critical thresholds
Maintenance due dates
Optimal resale windows


Driver Performance - Integrate driver data:

Correlate driver behavior with repair costs
Track driver-specific truck utilization
Identify training opportunities


Cost Forecasting - Add scenario planning:

"What if we sell 5 trucks this month?"
Break-even analysis by time period
Replacement cost modeling

When to Update Data

Daily: Import new distanceTraveled records
Weekly: Refresh truckData odometer snapshots
Bi-Weekly: Update StubData payroll information
Monthly: Refresh repairs and truck_finance tables
Quarterly: Update tp_listings resale values

Troubleshooting
IssueSolutionMileage mismatchCheck both distanceTraveled and truckData for gapsMissing recommendationsVerify all six tables have current dataIncorrect financialsConfirm truck_finance balances are updatedResale value errorsValidate tp_listings has entries for all active trucks

Summary
This dashboard transforms six disparate data sources into a unified decision-making tool. It automates the evaluation process that previously required manual analysis, providing clear, data-backed recommendations for fleet management.
Key Benefits:

Speed: Instant analysis vs hours of manual work
Accuracy: Objective, multi-factor decision criteria
Profitability: Identifies optimal buy/sell timing
Automation: Self-updating with new data imports
Transparency: Clear logic for every recommendation

Decision Framework:

Not subjective guesses, but calculated recommendations
Based on financial reality + operational performance + market conditions
Designed to maximize fleet value while minimizing risk
