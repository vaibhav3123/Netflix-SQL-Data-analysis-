# 📺 Netflix SQL Project

## 📌 Overview
This project is a structured SQL-based analysis of Netflix's vast content library, including movies and TV shows. The primary goal is to extract meaningful insights regarding content distribution, ratings, release patterns, and genre trends. By leveraging SQL queries, we uncover valuable patterns that help understand Netflix's content strategy and trends.

---

## 🎯 Objectives
- Analyze the distribution of content types (Movies vs. TV Shows).
- Identify the most common ratings for movies and TV shows.
- Explore and categorize content based on release years, countries, and durations.
- Examine the most frequent genres and their popularity trends.
- Identify top directors and actors featured on Netflix.
- Understand the pattern of content additions over the years.

---

## 📊 Dataset Information
- **Source:** Kaggle ([Netflix Movies & TV Shows Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows))
- **Size:** ~7,000+ records
- **Features:** Includes show ID, title, type, director, cast, country, release year, rating, duration, and genre.

---

## 🏗 Database Schema
```sql
CREATE TABLE netflix (
    show_id      VARCHAR(5) PRIMARY KEY,
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

---

## 📌 Key SQL Queries & Insights

### 1️⃣ Count the number of Movies vs TV Shows
```sql
SELECT type, COUNT(*) FROM netflix GROUP BY type;
```

### 2️⃣ Find the most common rating for movies and TV shows
```sql
WITH RatingCounts AS (
    SELECT type, rating, COUNT(*) AS rating_count FROM netflix GROUP BY type, rating
),
RankedRatings AS (
    SELECT type, rating, rating_count, RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank FROM RatingCounts
)
SELECT type, rating AS most_frequent_rating FROM RankedRatings WHERE rank = 1;
```

### 3️⃣ List all movies released in a specific year (e.g., 2020)
```sql
SELECT * FROM netflix WHERE release_year = 2020;
```

### 4️⃣ Find the top 5 countries with the most content on Netflix
```sql
SELECT * FROM (
    SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS country, COUNT(*) AS total_content FROM netflix GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```

### 5️⃣ Identify the longest movie
```sql
SELECT * FROM netflix WHERE type = 'Movie' ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
```

### 6️⃣ Find content added in the last 5 years
```sql
SELECT * FROM netflix WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

### 7️⃣ Find all the movies/TV shows by director 'Rajiv Chilaka'
```sql
SELECT * FROM (
    SELECT *, UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name FROM netflix
) WHERE director_name = 'Rajiv Chilaka';
```

### 8️⃣ List all TV shows with more than 5 seasons
```sql
SELECT * FROM netflix WHERE TYPE = 'TV Show' AND SPLIT_PART(duration, ' ', 1)::INT > 5;
```

### 9️⃣ Count the number of content items in each genre
```sql
SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre, COUNT(*) AS total_content FROM netflix GROUP BY 1;
```

### 🔟 Find each year and the average number of content releases by India on Netflix (Top 5 years)
```sql
SELECT country, release_year, COUNT(show_id) AS total_release,
    ROUND(COUNT(show_id)::numeric / (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

### 1️⃣1️⃣ List all movies that are documentaries
```sql
SELECT * FROM netflix WHERE listed_in LIKE '%Documentaries%';
```

### 1️⃣2️⃣ Find all content without a director
```sql
SELECT * FROM netflix WHERE director IS NULL;
```

### 1️⃣3️⃣ Find how many movies actor 'Salman Khan' appeared in the last 10 years
```sql
SELECT * FROM netflix WHERE casts LIKE '%Salman Khan%' AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

### 1️⃣4️⃣ Find the top 10 actors who have appeared in the highest number of movies produced in India
```sql
SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor, COUNT(*) FROM netflix WHERE country = 'India' GROUP BY 1 ORDER BY 2 DESC LIMIT 10;
```

### 1️⃣5️⃣ Categorize content based on the presence of keywords 'kill' and 'violence' in descriptions
```sql
SELECT category, TYPE, COUNT(*) AS content_count FROM (
    SELECT *,
        CASE WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad' ELSE 'Good' END AS category
    FROM netflix
) AS categorized_content GROUP BY category, TYPE ORDER BY TYPE;
```

---

## 📢 Conclusion & Future Enhancements
- Netflix primarily focuses on **Movies**, but TV shows are also a major part of its content library.
- The most common ratings suggest a balance between **family-friendly and mature content**.
- The **United States dominates** in content production, but international content is also growing.
- Future scope: **Advanced analytics using Python & Power BI** for visual trends and deeper insights.

---

## 📬 Contact & Contributions
For any questions, discussions, or improvements, feel free to reach out:
- **GitHub:** [https://github.com/vaibhav3123](https://github.com/vaibhav3123)
- **LinkedIn:** [https://www.linkedin.com/in/vaibhav-bari-915bb5202/](https://www.linkedin.com/in/vaibhav-bari-915bb5202/)
- **Email:** [bariv219@gmail.com](mailto:bariv219@gmail.com)

🚀 **Happy Analyzing!**

