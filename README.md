# Netflix Movies and TV Shows Data Analysis using SQL
![Netflix Logo](https://github.com/AmanKumar1902/Netflix_SQL_Project/blob/e193b25a7d44f3354be0c252a76fd62d5a300fe0/Screenshot%202025-02-15%20153809.png)
## Overiew
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
* Analyze the distribution of content types (movies vs TV shows).
* Identify the most common ratings for movies and TV shows.
* List and analyze content based on release years, countries, and durations.
* Explore and categorize content based on specific criteria and keywords.
## Dataset
The data for this project is sourced from the Kaggle dataset:
* Dataset Link: [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)
## Schema
'''sql
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
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
'''
Business Problems and Solutions
1. Count the Number of Movies vs TV Shows
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
Objective: Determine the distribution of content types on Netflix.

2. Find the Most Common Rating for Movies and TV Shows
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
Objective: Identify the most frequently occurring rating for each type of content.

3. List All Movies Released in a Specific Year (e.g., 2020)
SELECT * 
FROM netflix
WHERE release_year = 2020;
Objective: Retrieve all movies released in a specific year.

4. Find the Top 5 Countries with the Most Content on Netflix
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
Objective: Identify the top 5 countries with the highest number of content items.

5. Identify the Longest Movie
SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
Objective: Find the movie with the longest duration.

6. Find Content Added in the Last 5 Years
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
Objective: Retrieve content added to Netflix in the last 5 years.

7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
Objective: List all content directed by 'Rajiv Chilaka'.

8. List All TV Shows with More Than 5 Seasons
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
Objective: Identify TV shows with more than 5 seasons.

9. Count the Number of Content Items in Each Genre
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
Objective: Count the number of content items in each genre.

10.Find each year and the average numbers of content release in India on netflix.
return top 5 year with highest avg content release!

SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
Objective: Calculate and rank years by the average number of content releases by India.

11. List All Movies that are Documentaries
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
Objective: Retrieve all movies classified as documentaries.

12. Find All Content Without a Director
SELECT * 
FROM netflix
WHERE director IS NULL;
Objective: List content that does not have a director.

13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
Objective: Count the number of movies featuring 'Salman Khan' in the last 10 years.

14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.

15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

Author - Zero Analyst
This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

Stay Updated and Join the Community
For more content on SQL, data analysis, and other data-related topics, make sure to follow me on social media and join our community:

YouTube: Subscribe to my channel for tutorials and insights
Instagram: Follow me for daily tips and updates
LinkedIn: Connect with me professionally
Discord: Join our community to learn and grow together
Thank you for your support, and I look forward to connecting with you!
--Netflix Project
Create table netflix
(
	show_id	varchar(10),
	type varchar(20),	
	title varchar(150),	
	director varchar(250),	
	casts	varchar(1000),
	country	varchar(150),
	date_added	varchar(50),
	release_year int,
	rating varchar(10),
	duration varchar(15),
	listed_in varchar(100),	
	description varchar(250)
	
);
select* from netflix;

select count(*) as totalcount
from netflix;


select distinct(type) 
from netflix;

--15 Business Problem
--1. Count the number of Movies vs TV Shows
select type,count(*) as total_content
from netflix
Group by type

--2. Find the most common rating for movies and TV shows
--Methode-1 using cte
 with cte as(select 
type,
rating,
count(*),
Rank()over(partition by type order by count(*) desc)as ranking
from netflix
group by 1,2)
select type,rating from cte
where ranking=1
--Methode-2 using Sub-Query
select type,
rating
from
(select 
type,
rating,
count(*),
Rank()over(partition by type order by count(*) desc)as ranking
from netflix
group by 1,2)as t1
where ranking=1

--3. List all movies released in a specific year (e.g., 2020)
select *
from netflix
where release_year=2020 and type='Movie'


--4. Find the top 5 countries with the most content on Netflix
--Methode-1 using cte
with cte as
(select show_id,
  UNNEST(string_to_array(country,','))as new_country
from netflix  )

select new_country,count(show_id)as total_count
from cte
group by new_country
order by total_count Desc 
limit 5
--Methode-2 using Sub-Query

select
  UNNEST(string_to_array(country,','))as new_country,
count(show_id)as total_count
from netflix
group by new_country
order by total_count Desc
limit 5

--5. Identify the longest movie
select * from netflix
where 
type='Movie'
AND
Duration=(select Max(duration) from netflix )

--6. Find content added in the last 5 years
select *
from netflix
where
To_Date(date_added,'Month DD,YYYY')>=current_date-interval'5 years'

--7. Find all the movies/TV shows by director 'Rajiv Chilaka'!
select * 
from netflix
where director ILike'%Rajiv Chilaka%'
AND
type='Movie'

--8. List all TV shows with more than 5 seasons

select *
from netflix 
where
type='TV Show'
AND 
Split_Part(duration,' ', 1)::integer>5

--9. Count the number of content items in each genre
select
UNNEST(String_to_Array(listed_in,','))as genre,
count(show_id)
from netflix
group by 1
order by 2 desc

--10.Find each year and the average numbers of content release in India on netflix
--return top 5 year with highest avg content release!.
select
     Extract (year from To_Date(date_added,'Month DD,YYYY')) as years,
	 count(*) as yearly_content,
	 Round(count(*)::numeric/(select count(*) from netflix where country='India')::numeric*100,2 ) as avg_content_per_year
from netflix	 
where
country='India'
group by 1		 
order by 3 desc
limit 5
--11.List all movies that are documentaries
select * from netflix 
where listed_in Ilike '%documentaries%'

--12. Find all content without a director
select *
from netflix
where director is null

--13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
select * 
from netflix
where casts Ilike '%Salman Khan%'
AND
release_year >=Extract(Year from Current_Date)-10

--14. Find the top 10 actors who have appeared in the highest number of movies produced in India.
select 
UNNEST(string_to_array(casts,','))as actors,
count(*)as total_content
from netflix
where country Ilike '%india%'
group by 1
order by 2 desc
limit 10

--15.
--Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
--the description field. Label content containing these keywords as 'Bad' and all other 
--content as 'Good'. Count how many items fall into each category.
 with cte as
 (select * ,
CASE
When description Ilike '%kill%' OR
     Description Ilike '%violence%' THEN 'BAD_Content'
Else 'Good Content'	
End Category
from netflix
)
 select 
 category,
 count(*) as total_content
 from cte
 group by 1







