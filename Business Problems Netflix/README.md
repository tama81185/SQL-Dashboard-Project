#  Netflix SQL Analysis

## 10 Business Problems & Solutions

```sql
-- 1. Count the number of Movies vs TV Shows
SELECT type, COUNT(*) 
FROM netflix_titles 
GROUP BY 1;

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

-- 3. List all movies released in a specific year (e.g., 2020)
SELECT * 
FROM netflix_titles
WHERE release_year = 2020;

-- 4. Find the top 5 countries with the most content on Netflix
SELECT country_name, COUNT(*) AS total_content
FROM (
    SELECT TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(country, ',', numbers.n), ',', -1)) AS country_name
    FROM netflix_titles
    JOIN (
        SELECT 1 AS n UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4
        UNION ALL SELECT 5 UNION ALL SELECT 6 UNION ALL SELECT 7 UNION ALL SELECT 8
        UNION ALL SELECT 9 UNION ALL SELECT 10
    ) AS numbers
    ON CHAR_LENGTH(country) - CHAR_LENGTH(REPLACE(country, ',', '')) >= numbers.n - 1
) AS countries
WHERE country_name <> ''
GROUP BY country_name
ORDER BY total_content DESC
LIMIT 5;

-- 5. Identify the longest movie
SELECT *
FROM netflix_titles
WHERE type = 'Movie'
ORDER BY CAST(SUBSTRING_INDEX(duration, ' ', 1) AS UNSIGNED) DESC
LIMIT 1;

-- 6. Find content added in the last 5 years
SELECT *
FROM netflix_titles
WHERE STR_TO_DATE(date_added, '%M %d, %Y') >= DATE_SUB(CURDATE(), INTERVAL 5 YEAR);

-- 7. Find all the movies/TV shows by director 'Rajiv Chilaka'
SELECT *
FROM netflix_titles
WHERE LOWER(director) LIKE '%rajiv chilaka%';

-- 8. Find all content without a director
SELECT * 
FROM netflix_titles
WHERE director IS NULL;

-- 9. List all movies that are documentaries
SELECT * 
FROM netflix_titles
WHERE listed_in LIKE '%Documentaries%';

-- 10. List all TV shows with more than 5 seasons
SELECT *
FROM netflix_titles
WHERE type = 'TV Show'
  AND CAST(SUBSTRING_INDEX(duration, ' ', 1) AS UNSIGNED) > 5;
