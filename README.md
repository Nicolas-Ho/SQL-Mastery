# SQL-Mastery

agg window function, ranking

1.Heaviest athletes

SELECT
  Sport,
  avg(CAST(weight AS float64)) as average_weight
FROM constant-system-377818.Spotify.olympics
WHERE weight <>'NA'
GROUP BY Sport
ORDER BY average_weight DESC
LIMIT 10

2.gold medal count

SELECT
  team,
  count(Medal) as gold_medals_count
FROM constant-system-377818.Spotify.olympics
WHERE Medal = 'Gold'
GROUP BY team
ORDER BY gold_medals_count DESC
LIMIT 5

3.rank athletes with the most medals and their info

SELECT 
  name,
  team,
  count(medal) as medal_count,
  DENSE_RANK() OVER (ORDER BY count(medal) DESC) as rank
FROM constant-system-377818.Spotify.olympics
WHERE medal IN ('Gold', 'Silver', 'Bronze')
GROUP BY name, team
ORDER BY rank

4. france summer gold medal evolution

