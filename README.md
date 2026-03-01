Marketing Cross-Channel Performance Analysis – 2024
Project Overview
This project unifies raw advertising data from three major platforms — Facebook, Google, and TikTok — into a single analytics pipeline using BigQuery and SQL, and visualizes cross-channel performance through a Power BI dashboard.

Problem Statement
Marketing teams often struggle to compare performance across platforms because each platform exports data in a different format with different column names and structures. This project solves that by building a unified data model that enables apples-to-apples comparison across all three platforms.

Tools Used

BigQuery — Cloud database for data storage and transformation
SQL — Data cleaning, transformation, and KPI calculations
Power BI — Dashboard and visualization


Data Sources
FilePlatform01_facebook_ads.csvFacebook Ads02_google_ads.csvGoogle Ads03_tiktok_ads.csvTikTok Ads

Project Workflow
Step 1 – Data Ingestion
Loaded all 3 CSV files into BigQuery as separate tables.
Step 2 – Data Unification
Combined all 3 platform datasets into a single unified table using UNION ALL, standardizing column names (e.g., Google's costs column renamed to spend) and adding a platform identifier column.
Step 3 – Data Quality Checks
Performed the following validations on the unified table:

✅ Checked for missing/null values across all columns
✅ Checked for negative values (impressions, clicks, spend, conversions)
✅ Checked for duplicate rows
✅ Validated date range coverage across all platforms
✅ Validated logical constraints (clicks ≤ impressions, conversions ≤ clicks)
✅ Checked for zero denominators to prevent divide-by-zero errors in KPI calculations
✅ Checked for spend with zero impressions (delivery consistency)
✅ Screened for statistical outliers using 3-sigma method on spend

Result: No data quality issues found. Dataset is clean.
Step 4 – KPI Calculations
Created a final KPI table with the following calculated metrics:
KPIFormulaCTR (Click Through Rate)Clicks / ImpressionsCPC (Cost Per Click)Spend / ClicksCPA (Cost Per Acquisition)Spend / ConversionsConversion RateConversions / Clicks
Used SAFE_DIVIDE() to handle any potential divide-by-zero cases safely.
Step 5 – Dashboard
Built a one-page Power BI dashboard connected to the BigQuery KPI table.

Key Insights

🥇 TikTok drove the highest conversions (6.8K) despite not being the top spender
🥈 Google delivered the best balance of spend vs conversions (4.2K)
🥉 Facebook had the lowest total conversions (2.4K)
💰 Total spend: $130.24K across all platforms
📈 Total conversions: 13K at an average CPA of $9.75
🏆 Top campaign: Influencer_Collab with 2,653 conversions at $9.92 CPA
