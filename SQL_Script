-- ============================================
-- Marketing Cross-Channel Performance Analysis
-- SQL Transformation & Data Quality Script
-- BigQuery
-- ============================================

-- ============================================
-- STEP 1: UNIFY ALL 3 PLATFORMS INTO ONE TABLE
-- ============================================
CREATE TABLE `improvado-assignment-487315.marketing_assignment.final_table` AS
SELECT 
  date,
  campaign_name,
  impressions,
  clicks,
  spend,
  conversions,
  'facebook' AS platform
FROM `improvado-assignment-487315.marketing_assignment.facebook_ads`

UNION ALL

SELECT 
  date,
  campaign_name,
  impressions,
  clicks,
  costs AS spend,  -- Google uses 'costs' instead of 'spend'
  conversions,
  'google' AS platform
FROM `improvado-assignment-487315.marketing_assignment.google_ads`

UNION ALL

SELECT 
  date,
  campaign_name,
  impressions,
  clicks,
  spend,
  conversions,
  'tiktok' AS platform
FROM `improvado-assignment-487315.marketing_assignment.tiktok_ads`;


-- ============================================
-- STEP 2: DATA QUALITY CHECKS
-- ============================================

-- 2a) Check for missing/null values
SELECT
  COUNT(*) AS total_rows,
  SUM(CASE WHEN date IS NULL THEN 1 ELSE 0 END) AS null_dates,
  SUM(CASE WHEN campaign_name IS NULL THEN 1 ELSE 0 END) AS null_campaigns,
  SUM(CASE WHEN impressions IS NULL THEN 1 ELSE 0 END) AS null_impressions,
  SUM(CASE WHEN clicks IS NULL THEN 1 ELSE 0 END) AS null_clicks,
  SUM(CASE WHEN spend IS NULL THEN 1 ELSE 0 END) AS null_spend,
  SUM(CASE WHEN conversions IS NULL THEN 1 ELSE 0 END) AS null_conversions
FROM `improvado-assignment-487315.marketing_assignment.final_table`;

-- 2b) Check for negative values
SELECT *
FROM `improvado-assignment-487315.marketing_assignment.final_table`
WHERE impressions < 0
   OR clicks < 0
   OR spend < 0
   OR conversions < 0;

-- 2c) Check for duplicate rows
SELECT
  date,
  campaign_name,
  platform,
  COUNT(*) AS duplicate_count
FROM `improvado-assignment-487315.marketing_assignment.final_table`
GROUP BY date, campaign_name, platform
HAVING COUNT(*) > 1;

-- 2d) Ensure same date coverage across all platforms
SELECT
  platform,
  MIN(date) AS start_date,
  MAX(date) AS end_date
FROM `improvado-assignment-487315.marketing_assignment.final_table`
GROUP BY platform;

-- 2e) Logical constraint: clicks should never exceed impressions
SELECT *
FROM `improvado-assignment-487315.marketing_assignment.final_table`
WHERE clicks > impressions;

-- 2f) Logical constraint: conversions should not exceed clicks
SELECT *
FROM `improvado-assignment-487315.marketing_assignment.final_table`
WHERE conversions > clicks;

-- 2g) Check for outlier spend (basic min/max/avg)
SELECT
  platform,
  MAX(spend) AS max_spend,
  MIN(spend) AS min_spend,
  AVG(spend) AS avg_spend
FROM `improvado-assignment-487315.marketing_assignment.final_table`
GROUP BY platform;

-- 2h) Statistical outlier check using 3-sigma method
SELECT
  platform,
  MAX(spend) AS max_spend,
  AVG(spend) AS avg_spend,
  STDDEV_POP(spend) AS std_dev,
  AVG(spend) + (3 * STDDEV_POP(spend)) AS upper_threshold
FROM `improvado-assignment-487315.marketing_assignment.final_table`
GROUP BY platform;

-- 2i) Check for zeros to prevent divide-by-zero in KPI calculations
SELECT
  COUNTIF(impressions = 0) AS zero_impressions,
  COUNTIF(clicks = 0) AS zero_clicks,
  COUNTIF(conversions = 0) AS zero_conversions
FROM `improvado-assignment-487315.marketing_assignment.final_table`;

-- 2j) Check for spend with no impressions (delivery inconsistency)
SELECT *
FROM `improvado-assignment-487315.marketing_assignment.final_table`
WHERE spend > 0 AND impressions = 0;


-- ============================================
-- STEP 3: CREATE KPI TABLE
-- CTR = Click Through Rate (Clicks / Impressions)
-- CPC = Cost Per Click (Spend / Clicks)
-- CPA = Cost Per Acquisition (Spend / Conversions)
-- CVR = Conversion Rate (Conversions / Clicks)
-- ============================================
CREATE OR REPLACE TABLE `improvado-assignment-487315.marketing_assignment.final_ads_kpi` AS
SELECT
  date,
  platform,
  campaign_name,
  impressions,
  clicks,
  spend,
  conversions,
  SAFE_DIVIDE(clicks, impressions) AS ctr,
  SAFE_DIVIDE(spend, clicks) AS cpc,
  SAFE_DIVIDE(spend, conversions) AS cpa,
  SAFE_DIVIDE(conversions, clicks) AS conversion_rate
FROM `improvado-assignment-487315.marketing_assignment.final_table`;


