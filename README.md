# ga4-ecommerce-sql-analysis
Exploratory SQL analysis of 100k+ GA4 e-commerce events using Google BigQuery.

### **Query 1: Daily Traffic**
* **Business Context:** Analyzed raw GA4 daily events to calculate unique visitors vs. overall interaction volume across a 7-day period.

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
```
<img width="959" height="476" alt="Screenshot 2026-11-08 194338" src="https://github.com/user-attachments/assets/f86b1a1f-1f96-45ec-9dad-6868e1b9cc54" />

Key Takeaway: Daily unique visitors climbed steadily from 2,160 users on 2021-01-01 to a peak of 5,486 users on 2021-01-06, driving over 56,000 total events on peak days.

### **Query 2: Top 5 Recorded User Events**
* **Business Context:** Identified the top 5 most frequent user interactions to understand primary site engagement drivers.

```sql
SELECT 
  event_name,
  COUNT(*) AS total_occurrences,
  COUNT(DISTINCT user_pseudo_id) AS unique_users
FROM 
  `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
WHERE 
  _TABLE_SUFFIX BETWEEN '20210101' AND '20210107'
GROUP BY 
  event_name
ORDER BY 
  total_occurrences DESC
LIMIT 5;
```
<img width="959" height="475" alt="Screenshot 2026-11-08 194354" src="https://github.com/user-attachments/assets/e79751f5-40e3-420a-af36-fa5c6417bd90" />

Key Takeaway: Routine navigation and session initialization events dominated user activity, indicating healthy ongoing engagement with the platform interface.
