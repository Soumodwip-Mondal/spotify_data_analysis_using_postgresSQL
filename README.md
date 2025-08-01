# Spotify data analysis using postgresSQL
In this project  we will analysize the spotify data.
The dataset is avialavel in kaggel || [Link](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)<br>
![Spotify_Logo](https://github.com/Soumodwip-Mondal/spotify_data_analysis_using_postgresSQL/blob/main/spotify_logo.jpg)
## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and medium to hard), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.
## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

```sql code
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
--view the data
SELECT * FROM musicstats;

## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

#### Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
  
#### Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.

### 5. Query Optimization
In advanced stages, the focus shifts to improving query performance. Some optimization strategies include:
- **Indexing**: Adding indexes on frequently queried columns.
- **Query Execution Plan**: Using `EXPLAIN ANALYZE` to review and refine query performance.
  
---

## Problems

### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams.
2. List all albums along with their respective artists.
3. Get the total number of comments for tracks where `licensed = TRUE`.
4. Find all tracks that belong to the album type `single`.
5. Count the total number of tracks by each artist.

### Medium Level
6. Calculate the average danceability of tracks in each album.
7. Find the top 5 tracks with the highest energy values.
8. List all tracks along with their views and likes where `official_video = TRUE`.
9. For each album, calculate the total views of all associated tracks.
10. Retrieve the track names that have been streamed on Spotify more than YouTube.

### Medium to Hard Level
11. Find the top 3 most-viewed tracks for each artist using window functions.
12. Write a query to find tracks where the liveness score is above the average.
13. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
--1.
SELECT track,stream
FROM spotify
WHERE stream>1000000000;

--2
SELECT artist,album
FROM spotify

--3.
SELECT COUNT(comments) AS comment_cnt
FROM spotify
WHERE licensed = TRUE;

--4.
SELECT track
FROM spotify
WHERE album_type='single';

--5.
SELECT COUNT(track) AS track_count, artist
FROM spotify
GROUP BY artist;

--6.
SELECT album ,AVG(danceability) AS avg_danceability 
FROM spotify
GROUP BY album;

--7.
GROUP BY album;
SELECT track,energy 
FROM spotify
ORDER BY energy DESC
LIMIT 5;

--7.
SELECT track, views, likes
FROM spotify
WHERE official_video = TRUE;

--8.
SELECT album, SUM(views) AS total_views
FROM spotify
GROUP BY album;

--9.

SELECT artist,COUNT(track) AS tack_count
FROM spotify
GROUP BY artist;

--10.
SELECT track 
FROM spotify
WHERE most_playedon ='Youtube';

--11.
SELECT track
FROM (SELECT artist, track, ROW_NUMBER() OVER(PARTITION BY artist ORDER BY views DESC) AS rank_index
      FROM spotify) AS ranked
WHERE rank_index <= 3;

--12.
SELECT track, artist, liveness
FROM spotify
WHERE liveness >(
SELECT AVG(liveness) AS avg_liveness
FROM spotify
);

--13.
WITH CTE AS(
SELECT album, MAX(energy) AS MAX_VAL,MIN(energy) AS MIN_VAL
FROM spotify
GROUP BY album
)
SELECT album, MAX_VAL-MIN_VAL AS DFFERENCE
FROM CTE;
```

Here’s an updated section for your **Spotify Advanced SQL Project and Query Optimization** README, focusing on the query optimization task you performed. You can include the specific screenshots and graphs as described.

---

## Query Optimization Technique 

To improve query performance, we carried out the following optimization process:

- **Initial Query Performance Analysis Using `EXPLAIN`**
    - We began by analyzing the performance of a query using the `EXPLAIN` function.
    - The query retrieved tracks based on the `artist` column, and the performance metrics were as follows:
        - Execution time (E.T.): **7 ms**
        - Planning time (P.T.): **0.17 ms**
    - Below is the **screenshot** of the `EXPLAIN` result before optimization:
    - ![Before using index](https://github.com/Soumodwip-Mondal/spotify_data_analysis_using_postgresSQL/blob/main/spotify_explain_before_index.png)

- **Index Creation on the `artist` Column**
    - To optimize the query performance, we created an index on the `artist` column. This ensures faster retrieval of rows where the artist is queried.
    - **SQL command** for creating the index:
      ```sql
      CREATE INDEX idx_artist ON spotify_tracks(artist);
      ```

- **Performance Analysis After Index Creation**
    - After creating the index, we ran the same query again and observed significant improvements in performance:
        - Execution time (E.T.): **0.153 ms**
        - Planning time (P.T.): **0.152 ms**
    - Below is the **screenshot** of the `EXPLAIN` result after index creation:
    - ![After using index](https://github.com/Soumodwip-Mondal/spotify_data_analysis_using_postgresSQL/blob/main/spotify_explain_after_index.png)

  - **Graphical Performance Comparison**
    - A graph illustrating the comparison between the initial query execution time and the optimized query execution time after index creation.
    - **Graph view** shows the significant drop in both execution and planning times:
    - ![Performance Graph](https://github.com/Soumodwip-Mondal/spotify_data_analysis_using_postgresSQL/blob/main/spotify_graphical%20view%201.png)
    - ![Performance Graph](https://github.com/Soumodwip-Mondal/spotify_data_analysis_using_postgresSQL/blob/main/spotify_graphical%20view%202.png)
    - ![Performance Graph](https://github.com/Soumodwip-Mondal/spotify_data_analysis_using_postgresSQL/blob/main/spotify_graphical%20view%203.png)
