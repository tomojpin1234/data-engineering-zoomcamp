## Question 1. Understanding docker first run


```sh
docker run -it python:3.12.8 bash
```

Once inside the container:

```sh
pip --version
```

Answer is `24.3.1`

## Question 2. Understanding Docker networking and docker-compose


Answer is `db:5432`

## Question 3

### Up to 1 mile


```sql
SELECT COUNT(*) AS trip_count
FROM ny_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01 00:00:00'
  AND lpep_pickup_datetime < '2019-11-01 00:00:00'
  AND lpep_dropoff_datetime >= '2019-10-01 00:00:00'
  AND lpep_dropoff_datetime < '2019-11-01 00:00:00'
  AND trip_distance <= 1;
```

Anser is `104802`

### In between 1 (exclusive) and 3 miles (inclusive),

```sql
SELECT COUNT(*) AS trip_count
FROM ny_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01 00:00:00'
  AND lpep_pickup_datetime < '2019-11-01 00:00:00'
  AND lpep_dropoff_datetime >= '2019-10-01 00:00:00'
  AND lpep_dropoff_datetime < '2019-11-01 00:00:00'
  AND trip_distance > 1 AND trip_distance <= 3;
```

Anser is `198924`

### In between 3 (exclusive) and 7 miles (inclusive),

```sql
SELECT COUNT(*) AS trip_count
FROM ny_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01 00:00:00'
  AND lpep_pickup_datetime < '2019-11-01 00:00:00'
  AND lpep_dropoff_datetime >= '2019-10-01 00:00:00'
  AND lpep_dropoff_datetime < '2019-11-01 00:00:00'
  AND trip_distance > 3 AND trip_distance <= 7;
```

Anser is `109603`


### In between 7 (exclusive) and 10 miles (inclusive),

```sql
SELECT COUNT(*) AS trip_count
FROM ny_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01 00:00:00'
  AND lpep_pickup_datetime < '2019-11-01 00:00:00'
  AND lpep_dropoff_datetime >= '2019-10-01 00:00:00'
  AND lpep_dropoff_datetime < '2019-11-01 00:00:00'
  AND trip_distance > 7 AND trip_distance <= 10;
```

Anser is `27678`

### Over 10 miles

```sql
SELECT COUNT(*) AS trip_count
FROM ny_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01 00:00:00'
  AND lpep_pickup_datetime < '2019-11-01 00:00:00'
  AND lpep_dropoff_datetime >= '2019-10-01 00:00:00'
  AND lpep_dropoff_datetime < '2019-11-01 00:00:00'
  AND trip_distance > 10;
```

Anser is `35189`

Final answer is `104,802; 198,924; 109,603; 27,678; 35,189`

## Question 4. Longest trip for each day

```sql
select 
	CAST(lpep_pickup_datetime AS DATE) AS day,	
	MAX(trip_distance) AS trip_distance
from 
	ny_taxi_data
group by 
    day
order by 
    trip_distance DESC
LIMIT 
    1;
```

Anser is `2019-10-31`

## Question 5. Three biggest pickup zones

```sql
SELECT
  t."PULocationID" AS location,
  z."Zone",
  z."Borough",
  SUM(t.total_amount) AS total_amount
FROM
  ny_taxi_data t
JOIN
  zones z ON t."PULocationID" = z."LocationID"
WHERE
  t.lpep_pickup_datetime::date = '2019-10-18'
GROUP BY
  t."PULocationID", z."Zone", z."Borough"
HAVING
  SUM(t.total_amount) > 13000
ORDER BY
  total_amount DESC;
```

Answer is "East Harlem North", "East Harlem South", "Morningside Heights"

## Question 6. Largest tip

```
SELECT 
	tip_amount,
	t."PULocationID",
	t."DOLocationID",
	z."Zone"
FROM 
	ny_taxi_data t
JOIN
  zones z ON t."DOLocationID" = z."LocationID"
WHERE 
  t."PULocationID" = 74
  and t."lpep_pickup_datetime" >= '2019-10-01 00:00:00'
  AND t."lpep_pickup_datetime" < '2019-11-01 00:00:00'
ORDER BY tip_amount DESC
LIMIT 1
```

Answer is `JFK Airport`

## Question 7. Terraform Workflow

Question 7. Terraform Workflow

Answer is `terraform init, terraform apply -auto-approve, terraform destroy`

