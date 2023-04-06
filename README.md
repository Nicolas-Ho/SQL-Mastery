# SQL-Abilities

dataset link : https://www.kaggle.com/datasets/heesoo37/120-years-of-olympic-history-athletes-and-results

##The dataset : 120 years of Olympic history: athletes and results

This dataset contains basic bio data on athletes and medal results from Athens 1896 to Rio 2016. I felt this was an appropriate support to showcase some of my abilities with SQL. To this purpose I uploaded the dataset on BigQuery and tried to answer these following questions.


### 1.Display the 5 countries with the most gold medals in both summer and winter Olympics.

````sql
SELECT
  team,
  count(Medal) as gold_medals_count
FROM constant-system-377818.Spotify.olympics
WHERE Medal = 'Gold'
GROUP BY team
ORDER BY gold_medals_count DESC
LIMIT 5
````

**Query Result:** 




### 2.Display the 10 sports with the heaviest average weight of athletes.

````sql
SELECT
  Sport,
  avg(CAST(weight AS float64)) as average_weight
FROM constant-system-377818.Spotify.olympics
WHERE weight <>'NA'
GROUP BY Sport
ORDER BY average_weight DESC
LIMIT 10
````

**Steps**

Here we needed to cast the weight variable as a float to compute the average. On top of this we made sure not to include athletes with no weight recorded.

### 3.Rank the athletes according to the numbers of medals they won

````sql
SELECT 
  name,
  team,
  count(medal) as medal_count,
  DENSE_RANK() OVER (ORDER BY count(medal) DESC) as rank
FROM constant-system-377818.Spotify.olympics
WHERE medal IN ('Gold', 'Silver', 'Bronze')
GROUP BY name, team
ORDER BY rank
````

**Steps**

To rank the athletes the DENSE_RANK window function was used instead of a RANK() as to make the ranking clearer.

![Screenshot 2023-04-06 at 19 35 01](https://user-images.githubusercontent.com/73830924/230454593-ef0967e0-01fe-4a77-ba8e-1b45a81167b0.png)



### 4.CASE WHEN


### 5. Show the percentage evolution of medals won by France in each of the Summer Olympics ordered by year.

````sql
WITH cte AS (
  SELECT
  *,
    lag(medal_count, 1) OVER (ORDER BY year) as previous_olympics_medal_count
  FROM (
    SELECT 
      year,
      count(medal) as medal_count
    FROM constant-system-377818.Spotify.olympics
    WHERE team = 'France' AND season = 'Summer' AND medal<>'NA'
    GROUP BY team, year
    ORDER BY year) medal_table
  ORDER BY year )
SELECT
  *,
  ROUND(((medal_count - previous_olympics_medal_count)/CAST(previous_olympics_medal_count AS NUMERIC))*100) as percentage_evolution
FROM cte
````

**Steps**

First I needed to create a view which would show the result of France and medals won for each of the summer olympics. This was done in the nested subquery. I then used the LAG window function to be able to compare side by side current and previous olympics edition by year. This is achieved in the CTE.
Finally the CTE is used to calculate the percentage evolution.
