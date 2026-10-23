# Netflix Content Analysis using Advanced SQL

An end-to-end SQL analysis of the Netflix titles dataset (8,807 records), answering 17 business questions on content strategy, regional trends, genres and data quality. The project moves from basic filtering to window functions, CTEs and JSON-based data transformation.

## Project Overview

| | |
|---|---|
| **Objective** | Extract insights on Netflix's catalogue: what content exists, where it comes from, and how it has grown over time |
| **Database** | MySQL 8.0+ |
| **Dataset** | `netflix_titles.csv`: 8,807 rows, 12 columns |
| **Deliverable** | `netflix_advanced_sql.sql`: 17 documented queries |

## Dataset Columns

`show_id` · `type` · `title` · `director` · `cast` · `country` · `date_added` · `release_year` · `rating` · `duration` · `listed_in` · `description`

## Business Questions Answered

**Content and catalogue**
- Which TV shows have 5 or more seasons?
- Which movie has the longest runtime?
- How can movies be categorised as Short, Medium or Long?
- What are the top genres by average release year?

**Regional analysis**
- Which Indian movies belong to the Comedies category?
- What are the 5 oldest movies released in India?
- In which years were more than 50 Indian movies released?
- What is the most recent movie for each country?
- How do countries rank by number of titles?

**Talent analysis**
- Who are the top 5 directors by number of movies?
- How do director counts change after splitting multi-director titles into individual rows?

**Growth trends**
- In which year did Netflix add the most content?
- How does content added each year compare with the previous year?
- What is the latest movie for each release year?

**Data quality**
- How many missing values are there in the key columns?

## SQL Skills Demonstrated

| Category | Techniques |
|---|---|
| **Querying** | `SELECT`, `WHERE`, `LIKE`, `ORDER BY`, `LIMIT` |
| **Aggregation** | `GROUP BY`, `HAVING`, `COUNT`, `AVG`, `MAX`, conditional `SUM` |
| **Window functions** | `RANK`, `DENSE_RANK`, `ROW_NUMBER`, `LAG` |
| **Advanced SQL** | CTEs, subqueries, `CASE WHEN`, `JSON_TABLE` |
| **Data cleaning** | `TRIM`, `NULL` and blank-value handling, `STR_TO_DATE`, `CAST`, `SUBSTRING_INDEX` |

## Featured Query

**Top 5 directors, handling titles with multiple directors.** Many titles list several directors in one cell, so this query splits them into separate rows before counting.

```sql
WITH director_data AS (
    SELECT
        n.show_id,
        n.release_year,
        TRIM(d.director_name) AS director
    FROM netflix_titles n
    JOIN JSON_TABLE(
        CONCAT('["', REPLACE(REPLACE(n.director, '"', '\\"'), ', ', '","'), '"]'),
        '$[*]' COLUMNS (director_name VARCHAR(255) PATH '$')
    ) AS d
    WHERE n.type = 'Movie'
      AND n.director IS NOT NULL
      AND TRIM(n.director) <> ''
      AND LOWER(TRIM(n.director)) <> 'not given'
)
SELECT
    director,
    COUNT(DISTINCT show_id) AS total_movies,
    MAX(release_year)       AS latest_release_year
FROM director_data
GROUP BY director
ORDER BY total_movies DESC
LIMIT 5;
```

## Data Cleaning Approach

- Treated both `NULL` and blank strings as missing values.
- Excluded placeholder values such as "Not Given" from director analysis.
- Converted text fields (`date_added`, `duration`) into dates and numbers for analysis.
- Split multi-value columns (directors) into one row per value to avoid miscounting.

## How to Run

1. Create the database: `CREATE DATABASE netflix;`
2. Import `netflix_titles.csv` into a table named `netflix_titles`.
3. Open `netflix_advanced_sql.sql` in MySQL Workbench or the MySQL CLI and run the queries.

## Repository Structure

```
├── netflix_advanced_sql.sql   # All 17 queries, numbered and commented
├── netflix_titles.csv         # Source dataset
└── README.md
```

## Contact

**GitHub:** [vedavalliguntuku](https://github.com/vedavalliguntuku)
