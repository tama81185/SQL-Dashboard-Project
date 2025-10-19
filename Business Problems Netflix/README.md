#  Netflix SQL Analysis

## 10 Business Problems & Solutions

```sql
-- 1. Count the number of Movies vs TV Shows
SELECT type, COUNT(*) FROM netflix_titles GROUP BY 1;

-- 2. Find the most common rating for movies and TV shows
WITH RatingCounts AS (
    SELECT type, rating, COUNT(*) AS rating_count
    FROM netflix_titles
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type, rating, rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT type, rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;


