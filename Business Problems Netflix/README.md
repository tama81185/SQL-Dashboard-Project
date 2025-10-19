#  Netflix SQL Analysis

## 10 Business Problems & Solutions

```sql
-- 1. Count the number of Movies vs TV Shows
SELECT type, COUNT(*) FROM netflix_titles GROUP BY 1;

