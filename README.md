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
``` sql
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
```
## Business Problem and Solutions
### 1. Count the number of Movies vs TV Shows
```sql
select type,count(*) as total_content
from netflix
Group by type
```
### 2. Find the most common rating for movies and TV shows
* Methode-1 using cte
```sql
 with cte as(select 
type,
rating,
count(*),
Rank()over(partition by type order by count(*) desc)as ranking
from netflix
group by 1,2)
select type,rating from cte
where ranking=1
```
* Methode-2 using Sub-Query
```sql
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
```
### 3. List all movies released in a specific year (e.g., 2020)
```sql
select *
from netflix
where release_year=2020 and type='Movie'
```
### 4. Find the top 5 countries with the most content on Netflix
* Methode-1 using cte
```sql
with cte as
(select show_id,
  UNNEST(string_to_array(country,','))as new_country
from netflix  )

select new_country,count(show_id)as total_count
from cte
group by new_country
order by total_count Desc 
limit 5
```
* Methode-2 using Sub-Query
  ```sql
  select
  UNNEST(string_to_array(country,','))as new_country,
   count(show_id)as total_count
   from netflix
  group by new_country
  order by total_count Desc
 limit 5
```
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







