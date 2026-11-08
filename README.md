# ga4-ecommerce-sql-analysis
Exploratory SQL analysis of 100k+ GA4 e-commerce events using Google BigQuery.
Business Context: Analyzed raw GA4 daily events to calculate unique visitors vs. overall interaction volume across a 7-day period.
```sql
SELECT 
  event_date,
  COUNT(DISTINCT user_pseudo_id) AS total_users,
  COUNT(*) AS total_events
FROM 
  `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
WHERE 
  _TABLE_SUFFIX BETWEEN '20210101' AND '20210107'
GROUP BY 
  event_date
ORDER BY 
  event_date ASC;
<img width="958" height="473" alt="Screenshot 2026-11-08 185359" src="https://github.com/user-attachments/assets/824e10cb-f4d4-4df3-8176-69efa96d7cc2" />
Key Takeaway: Daily unique visitors climbed steadily from 2,160 users on 2021-01-01 to a peak of 5,486 users on 2021-01-06, driving over 56,000 total events on peak days.
