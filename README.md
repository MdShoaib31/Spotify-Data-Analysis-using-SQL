# Spotify Advanced SQL Project
Project Category: Advanced - <a href="https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset">Click Here to get Dataset</a>

![spotify_logo](https://github.com/user-attachments/assets/e49f56cb-7b3a-42fc-9fe3-e1dd55d5a1d3)

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using SQL. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query perfomance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.
```
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```
## Project Exploration
Before diving into SQL, it's important to understand the dataset thoroughly. The dataset constains attributes such as:
- ```Artist``` : The perfomer of the track.
- ```Track``` : The name of the song.
- ```Album``` : The album to which the track belongs.
- ```Album_type``` : The type of album (e.g., single or album)
- various metrics such as ```Danceability```, ```Energy```, ```Loudness```, ```Tempo```, and more.
## Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.
**Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
**Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
**Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.
# 15 Practice Questions
**Easy Level**
1. Retrieve the names of all tracks that have more than 1 billion streams.
```
select track, stream
from spotify
where stream > 1000000000;
```
2. List all albums along with their respective artists.
```
select distinct album, artist
from spotify
order by 1;
```
3. Get the total number of comments for tracks where ```licensed = TRUE```.
```
select sum(comments) as total_comments
from spotify
where licensed = 'true';
```
4. Find all tracks that belong to the album type ```single```.
```
select *
from spotify
where album_type = 'single';
```
5. Count the total number of tracks by each artist.
```
select  artist, count(track) as total_tracks
from spotify
group by artist
order by total_tracks asc;
```
**Medium Level**

6. Calculate the total number of tracks by each artist.
```
select album, avg(danceability) as avg_danceability
from spotify
group by album;
```
7. Find the top 5 tracks with the highest energy values.
```
select track, max(energy) as highest_energy
from spotify
group by track
order by highest_energy desc limit 5;
```
8. LIst all tracks along with their views and likes where ```official_video = TRUE```.
```
select track,sum(views) as total_views,sum(likes) as total_likes
from spotify
where official_video = 'true'
group by track
order by total_views desc limit 5;
```
9. For each album, calculate the total views of all asspciated tracks.
```
select album,track, sum(views) as total_views
from spotify
group by album,track
order by total_views desc;
```
10. REtrieve the track names that have been streamed on Spotify more than YouTube.
```
select * from
(select 
	track,
	coalesce(sum(case when most_played_on = 'Youtube' then stream end),0) as streamed_on_youtube,
	coalesce(sum(case when most_played_on = 'Spotify' then stream end),0) as streamed_on_spotify
from spotify
group by 1
) as t1
where 
	streamed_on_spotify > streamed_on_youtube
	and
	streamed_on_youtube <> 0;
```
**Advanced Level**

11. Find the top 3 most-viewed tracks for each artist using window functions.
```
with Artist_ranking 
as
(select
	artist,
	track,
	sum(views) as total_views,    
	dense_rank() over(partition by artist order by sum(views) desc) as rank
from spotify
group by artist,track
order by artist,total_views desc
)
select * from Artist_ranking
where rank <= 3;
```
12. Write a query to find trakcs where the liveness score is above the average.
```
select track,artist,liveness 
from spotify
where liveness > (select avg(liveness) from spotify);
```
13. Use a ```WITH``` clause to calculate the difference between the highest and lowest energy values for tracks in each album.
```
with cte
as
(select album,
		max(energy) as highest_energy,
		min(energy) as lowest_energy
from spotify
group by album)
select album,
		highest_energy - lowest_energy as energy_diff
from cte
order by energy_diff desc;
```
14. Find tracks where the energy-to-liveness ratio is greater than 1.2 .
```
select track,
		energy,
		liveness,
		(energy/liveness) as energy_to_liveness_ratio
from spotify
where (energy/liveness) > 1.2
order by energy_to_liveness_ratio desc;
```
15. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window function.
```
select artist,
		track,
		views,
		likes,
		sum(likes) over (order by views desc) as cumulative_sum_of_likes
from spotify;
```
# Technology Stack
- Database: PostgreSQL
- SQL Queries: DDL, DML, Aggregations, joins, Subqueries, Window Functions
- Tools: pgAdmin 4, PostgreSQL
