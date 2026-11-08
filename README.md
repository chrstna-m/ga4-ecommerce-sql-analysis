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

### **Query 3: Most Viewed Pages (Page Engagement)**
* **Business Context:** Extracted nested `page_title` parameters from raw event arrays to isolate the top 5 most viewed site pages.

```sql
SELECT 
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'page_title') AS page_title,
  COUNT(*) AS page_views
FROM 
  `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
WHERE 
  event_name = 'page_view'
  AND _TABLE_SUFFIX BETWEEN '20210101' AND '20210107'
GROUP BY 
  page_title
ORDER BY 
  page_views DESC
LIMIT 5;
```
<img width="959" height="473" alt="Screenshot 2026-11-08 195712" src="https://github.com/user-attachments/assets/e0197e6d-4981-448d-ae3f-5c90d721ba6a" />

Key Takeaway: Home and main product catalog pages drove the vast majority of pageviews, highlighting the core entry points for store traffic.

### **Query 4: Audience Segmentation by Device Category**
* **Business Context:** Analyzed user hardware properties (`device.category`) to determine mobile vs. desktop visitor distribution across the platform.

```sql
SELECT 
  device.category AS device_type,
  COUNT(DISTINCT user_pseudo_id) AS user_count,
  COUNT(*) AS total_events
FROM 
  `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
WHERE 
  _TABLE_SUFFIX BETWEEN '20210101' AND '20210107'
GROUP BY 
  device_type
ORDER BY 
  user_count DESC;
```
<img width="959" height="476" alt="image" src="https://github.com/user-attachments/assets/4fc7f828-fd12-4c04-866d-c5abaae935db" />

Key Takeaway: Desktop users accounted for the highest traffic share, signaling the primary target hardware platform for web optimization and UX enhancements.

### **Query 5: New vs. Returning User Acquisition Funnel**
* **Business Context:** Constructed a Common Table Expression (CTE) aggregating `first_visit` and `first_open` signals to isolate newly acquired visitors from returning traffic.

```sql
WITH UserActivity AS (
  SELECT 
    user_pseudo_id,
    MAX(IF(event_name IN ('first_visit', 'first_open'), 1, 0)) AS is_new_user
  FROM 
    `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
  WHERE 
    _TABLE_SUFFIX BETWEEN '20210101' AND '20210107'
  GROUP BY 
    user_pseudo_id
)
SELECT 
  COUNT(*) AS total_active_users,
  SUM(is_new_user) AS new_users,
  COUNT(*) - SUM(is_new_user) AS returning_users
FROM 
  UserActivity;
```
<img width="959" height="476" alt="Screenshot 2026-11-08 201103" src="https://github.com/user-attachments/assets/4f6fc437-5e83-4ee1-8185-8dcc7ca79b63" />

Key Takeaway: Newly acquired users accounted for the vast majority of active weekly traffic, demonstrating effective top-of-funnel customer acquisition campaigns.






