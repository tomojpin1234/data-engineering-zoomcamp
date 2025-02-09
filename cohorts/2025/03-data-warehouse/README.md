## Prerequisities 

```SQL
-- Creating external table referring to gcp path
CREATE OR REPLACE EXTERNAL TABLE `poetic-centaur-449918-m9.zoomcamp.external_yellow_tripdata`
OPTIONS (
  format = 'PARQUET',
  uris = [
    'gs://poetic-centaur-449918-m9-demo-bucket-test/yellow_tripdata_2024-01.parquet',
    'gs://poetic-centaur-449918-m9-demo-bucket-test/yellow_tripdata_2024-02.parquet',
    'gs://poetic-centaur-449918-m9-demo-bucket-test/yellow_tripdata_2024-03.parquet',
    'gs://poetic-centaur-449918-m9-demo-bucket-test/yellow_tripdata_2024-04.parquet',
    'gs://poetic-centaur-449918-m9-demo-bucket-test/yellow_tripdata_2024-05.parquet',
    'gs://poetic-centaur-449918-m9-demo-bucket-test/yellow_tripdata_2024-06.parquet'
  ]
);

-- Create regular table
CREATE OR REPLACE TABLE `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata`
AS
SELECT * FROM `poetic-centaur-449918-m9.zoomcamp.external_yellow_tripdata`;

-- Create materialized table
CREATE MATERIALIZED VIEW `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_mv`
AS
SELECT * FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata`;
```

## Question 1. What is count of records for the 2024 Yellow Taxi Data?

```SQL
SELECT count(1) from poetic-centaur-449918-m9.zoomcamp.external_yellow_tripdata;
```

Answer is `20,332,093`

## Question 2. What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

```SQL
-- External 0 B
SELECT DISTINCT(PULocationID)
FROM `poetic-centaur-449918-m9.zoomcamp.external_yellow_tripdata`;

-- Regular 155.12 MB
SELECT DISTINCT(PULocationID)
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata`;

-- Materialized 155.12 MB
SELECT DISTINCT(PULocationID)
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_mv`
```

Answer is `0 MB for the External Table and 155.12 MB for the Materialized Table`

## Question 3. 

```SQL
SELECT PULocationID 
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata`;

SELECT PULocationID, DOLocationID 
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata`;
```

Anser is `BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.
`

## Question 4. How many records have a fare_amount of 0?

```SQL
SELECT count(*) AS fare_count
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata`
WHERE fare_amount=0;
```

Anser is `8,333`

## Question 5. 

```SQL
-- 1
-- Partition by tpep_dropoff_datetime and Cluster on VendorID
CREATE OR REPLACE TABLE `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_part_and_clus`
PARTITION BY DATE(tpep_dropoff_datetime)  -- Partition by tpep_dropoff_datetime
CLUSTER BY VendorID  -- Cluster by VendorID for efficient ordering and filtering
AS
SELECT * FROM `poetic-centaur-449918-m9.zoomcamp.external_yellow_tripdata`;

-- 10 s
SELECT *
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_part_and_clus`
WHERE
  tpep_dropoff_datetime BETWEEN '2024-01-01' AND '2024-12-31'
ORDER BY
  VendorID;

-- 2
-- Cluster on by tpep_dropoff_datetime and Cluster on VendorID
CREATE OR REPLACE TABLE `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_clus`
CLUSTER BY tpep_dropoff_datetime, VendorID
AS
SELECT * FROM `poetic-centaur-449918-m9.zoomcamp.external_yellow_tripdata`;

-- 9 s
SELECT *
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_clus`
WHERE
  tpep_dropoff_datetime BETWEEN '2024-01-01' AND '2024-12-31'
ORDER BY
  VendorID;

-- 3
-- Cluster on tpep_dropoff_datetime Partition by VendorID
-- Not possible

-- 4
-- Partition by tpep_dropoff_datetime and Partition by VendorID
-- Not possible
```
Answer is `Cluster on by tpep_dropoff_datetime and Cluster on VendorID`

## Question 6. Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 5 and note the estimated bytes processed. What are these values?


```SQL
SELECT DISTINCT(VendorID)
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_mv`
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';
--  This query will process 310.24 MB when run. 

SELECT DISTINCT(VendorID)
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_part_and_clus`
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';
--  This query will process 26.84 MB when run. 
```

Answer is `310.24 MB for non-partitioned table and 26.84 MB for the partitioned table`


## Question 7: Where is the data stored in the External Table you created?

Answer is `GCP Bucket`

## Question 8: It is best practice in Big Query to always cluster your data

Answer is `False

## Question 9: No Points: Write a SELECT count(*) query FROM the materialized table you created. How many bytes does it estimate will be read? Why?

```SQL
SELECT count(*)
FROM `poetic-centaur-449918-m9.zoomcamp.yellow_tripdata_mv`;
--  This query will process 0 B when run.
```

Anser is `When querying a materialized view, BigQuery does not scan the underlying source data`

