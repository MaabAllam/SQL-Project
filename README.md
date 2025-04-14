🔍 Art Gallery Data Analysis Using SQL
In this project, I conducted a comprehensive SQL-based data analysis of a large art gallery dataset to uncover key insights about artists, artworks, pricing, and museum operations. The dataset included multiple interconnected tables such as artists, artworks, museums, canvas sizes, prices, and subjects.

💼 Key Objectives:
Identify the most prolific artists and popular artistic styles.

Analyze pricing strategies, including average sale prices and discount trends.

Examine museum operational data such as opening hours and artwork distributions.

Explore canvas size usage and subject matter frequency across artworks.

🧠 Skills Applied:
SQL Joins, Grouping, Aggregations, and Window Functions

Data Cleaning and Exploration

Descriptive Statistics using SQL

Query Optimization and Logic Design

📈 Highlighted Insights:
Most represented artist and their artwork count.

Museums with the highest artwork holdings.

Most commonly used canvas sizes and subject themes.

Pricing distribution and artworks sold at discounts.

Average artist lifespan and cross-museum artwork display.

This project demonstrates my ability to work with real-world relational datasets, extract meaningful insights, and translate raw data into actionable conclusions using SQL.

💡 Tools Used: SQL (MySQL), Excel (for data inspection)
-- Artists & Artworks
-- 1 Which artist has the most artworks in the dataset?
SELECT 
    a.full_name AS artist_name,
    COUNT(w.work_id) AS artwork_count
FROM 
    work w
JOIN 
    artist a ON w.artist_id = a.artist_id
GROUP BY 
    a.full_name
ORDER BY 
    artwork_count DESC
LIMIT 1;
-- Explanation:
-- JOIN combines the work and artist tables using artist_id.

-- COUNT(w.work_id) counts how many artworks each artist has.

-- GROUP BY a.full_name groups the results by artist.

-- ORDER BY artwork_count DESC sorts artists by the number of artworks in descending order.

-- LIMIT 1 gives you the artist with the most artworks.

-- 2 What are the different artistic styles represented, and how many works belong to each style?
select distinct style from artist;
SELECT 
    style,
    COUNT(work_id) AS work_count
FROM 
    work
WHERE style IS NOT NULL AND TRIM(style) != ''    
GROUP BY 
    style
ORDER BY 
    work_count DESC;
    
  -- style: the column that holds the artistic style (e.g., Impressionism, Baroque, Cubism).

-- COUNT(work_id): counts how many works are associated with each style.

-- GROUP BY style: groups the results by each style type.

-- ORDER BY work_count DESC: sorts it from most to least popular. 

-- 3  What is the average lifespan of artists in the dataset?
select artist_id,
avg(death - birth)
 from artist
 Group by 
 artist_id;
 -- Subtract birth year from death year for each artist.

-- Filter out rows where birth or death is missing.

-- Calculate the average lifespan across all valid rows.

-- 4 Which artists have artworks displayed in multiple museums?
select * from work ; 
select * from artist ;
SELECT 
    a.full_name AS artist_name,
    COUNT(w.work_id) AS artwork_count,
    count(distinct w.museum_id) As museums
FROM 
    work w
JOIN 
    artist a ON w.artist_id = a.artist_id
    where w.museum_id is not null
GROUP BY 
    a.full_name , a.artist_id
 having 
 count(distinct w.museum_id) > 1
ORDER BY 
    museums DESC;
  --  All artists who have their works displayed in more than one museum.
 -- Sorted by the number of museums.
-- COUNT(DISTINCT w.museum_id) → counts unique museums.
-- HAVING COUNT(DISTINCT w.museum_id) > 1 → filters only artists with works in more than one museum. 

-- Museums
-- 5  Which museum has the most artworks in the dataset?  
SELECT 
    m.name AS museum_name,
    COUNT(w.work_id) AS artwork_count
FROM 
    work w
JOIN 
    museum m ON w.museum_id = m.museum_id
GROUP BY 
    m.name
ORDER BY 
    artwork_count DESC
    limit 1 ;
-- Join work and museum tables.
-- Count how many artworks each museum has.
-- Sort from most to least.
-- Return the top one using LIMIT 1. 
-- 6 What are the opening and closing times of each museum? Which museum has the longest open hours?
select * from museum_hours;
select * from museum;
SELECT 
    m.name AS museum_name,
    mh.open,
    mh.close,
    TIMESTAMPDIFF(
        MINUTE,
        STR_TO_DATE(mh.open, '%h:%i: %p'),
        STR_TO_DATE(mh.close, '%h:%i: %p')
    ) / 60 AS open_hours
FROM 
    museum m
JOIN 
    museum_hours mh ON m.museum_id = mh.museum_id
WHERE 
    mh.open IS NOT NULL AND mh.close IS NOT NULL
ORDER BY 
    open_hours DESC
    limit 5;
    
-- STR_TO_DATE(mh.open, '%h:%i: %p') turns '9:30: AM' into a real TIME.
-- TIMESTAMPDIFF(MINUTE, ..., ...) / 60 calculates the difference in hours, including minutes as decimals (e.g. 7.5 hours).
-- Filters out any NULLs.
-- Orders by longest open hours.
-- 7 Are there any museums that are closed on certain days?
select * from museum_hours;
SELECT 
    m.name AS museum_name,
    mh.day
FROM 
    museum m
JOIN 
    museum_hours mh ON m.museum_id = mh.museum_id
WHERE 
    mh.open IS NULL OR mh.close IS NULL
ORDER BY 
    m.name, mh.day;
--  It joins the museum and hours tables.
-- Filters for records where open or close is NULL — meaning the museum is closed on that day.
-- Displays the museum name and the day it’s closed.   
-- Artwork Analysis
--  8 What are the most common canvas sizes used?
 SELECT 
    cs.label AS canvas_size,
    COUNT(ps.size_id) AS usage_count
FROM 
    canvas_size cs
JOIN 
    product_size ps ON ps.size_id = cs.size_id
GROUP BY 
    cs.label
ORDER BY 
    usage_count DESC
LIMIT 1;
-- 9 Which artwork has the highest sale price? How does it compare to its regular price?
select product_size.regular_price,
product_size.sale_price,
(product_size.sale_price - product_size.regular_price) as price_difference,
work.name
from work
join 
product_size on product_size.work_id =  work.work_id
order by
product_size.sale_price
limit 1;
-- negative sign (Sold for more than usual)
-- Orders artworks by sale_price from highest to lowest
-- Shows only the top result with the largest sale_price
-- Calculates the difference between sale and regular price
-- 10 Which subjects (e.g., Still-Life, Portraits) are most commonly found in the dataset?

   SELECT 
    s.subject AS subject_name,
    COUNT(ps.work_id) AS artwork_count
FROM 
    product_size ps
JOIN 
    subject s ON ps.work_id = s.work_id
GROUP BY 
    s.subject
ORDER BY 
    artwork_count DESC
    limit 1;
   --  Which subjects (like Portraits, Still-Life, etc.) are most represented across all artworks.
-- Helps spot trends like popular themes artists focused on.
-- Sales & Pricing
-- 11  What is the average sale price of artworks per artistic style? 
SELECT 
    w.style AS artistic_style,
    AVG(ps.sale_price) AS average_sale_price
FROM 
    product_size ps
JOIN 
    work w ON ps.work_id = w.work_id
GROUP BY 
    w.style
ORDER BY 
    average_sale_price DESC;
-- product_size: contains sale_price and work_id
-- work: contains style and work_id
-- We're joining them by work_id, grouping by style, and taking the average sale price.
-- 12 How many artworks are currently sold at a discount?
SELECT 
    COUNT(*) AS discounted_artwork_count
FROM 
    product_size
WHERE 
    sale_price < regular_price;
-- We’re checking where sale_price is less than regular_price.
-- Then we count those rows to get the number of discounted artworks.
-- 13 What is the price distribution of artworks by size?
SELECT 
    cs.label AS canvas_size,
    COUNT(ps.work_id) AS artwork_count,
    AVG(ps.sale_price) AS avg_sale_price,
    MIN(ps.sale_price) AS min_sale_price,
    MAX(ps.sale_price) AS max_sale_price
FROM 
    product_size ps
JOIN 
    canvas_size cs ON ps.size_id = cs.size_id
GROUP BY 
    cs.label
ORDER BY 
    avg_sale_price DESC;
--  Which canvas sizes are most popular (via artwork_count)
-- How pricing differs by size (via average, min, and max prices)
