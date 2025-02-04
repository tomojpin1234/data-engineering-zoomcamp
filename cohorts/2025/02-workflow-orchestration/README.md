## Question 1. Within the execution for Yellow Taxi data for the year 2020 and month 12: what is the uncompressed file size (i.e. the output file yellow_tripdata_2020-12.csv of the extract task)?

Answer is `128.3 MB`

## Question 2. What is the rendered value of the variable file when the inputs taxi is set to green, year is set to 2020, and month is set to 04 during execution?

```
- id: extract
    type: io.kestra.plugin.scripts.shell.Commands
    outputFiles:
      - "*.csv"
    taskRunner:
      type: io.kestra.plugin.core.runner.Process
    commands:
      - wget -qO- https://github.com/DataTalksClub/nyc-tlc-data/releases/download/{{inputs.taxi}}/{{render(vars.file)}}.gz | gunzip > {{render(vars.file)}}
      - echo {{render(vars.file)}}
```

Answer is `green_tripdata_2020-04.csv`

## Question 3. How many rows are there for the Yellow Taxi data for all CSV files in the year 2020?

```SQL
SELECT COUNT(*) 
FROM yellow_tripdata 
WHERE tpep_pickup_datetime >= '2020-01-01' 
  AND tpep_pickup_datetime < '2021-01-01';
```

Anser is `24,648,499`

## Question 4. How many rows are there for the Green Taxi data for all CSV files in the year 2020?


Anser is `1,734,051`

## Question 5. How many rows are there for the Yellow Taxi data for the March 2021 CSV file?

Answer is "1,925,152"

## Question 6. How would you configure the timezone to New York in a Schedule trigger?

Answer is `Add a timezone property set to America/New_York in the Schedule trigger configuration`

