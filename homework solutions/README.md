# Module 4 Homework Solutions (Analytics Engineering with dbt)

## Final Answers

1. **Question 1:** `int_trips_unioned` only
2. **Question 2:** dbt will fail the test, returning a non-zero exit code
3. **Question 3:** `12,184`
4. **Question 4:** `East Harlem North`
5. **Question 5:** `384,624`
6. **Question 6:** `43,244,693`

## dbt Commands Used

```bash
# from project directory
cd temp_repo/04-analytics-engineering/taxi_rides_ny

# install packages
dbt deps

# build models in production target
dbt build --target prod

# validate model selection behavior for Q1
dbt ls --select int_trips_unioned
```

## SQL Queries Used for Homework Checks

### Q3: Count records in `fct_monthly_zone_revenue`

```sql
SELECT COUNT(*)
FROM prod.fct_monthly_zone_revenue;
```

### Q4: Highest Green taxi revenue zone in 2020

```sql
SELECT
  pickup_zone,
  SUM(revenue_monthly_total_amount) AS total_revenue
FROM prod.fct_monthly_zone_revenue
WHERE service_type = 'Green'
  AND revenue_month >= '2020-01-01'
  AND revenue_month < '2021-01-01'
GROUP BY pickup_zone
ORDER BY total_revenue DESC
LIMIT 1;
```

### Q5: Green taxi total trips in October 2019

```sql
SELECT SUM(total_monthly_trips) AS green_oct_2019_trips
FROM prod.fct_monthly_zone_revenue
WHERE service_type = 'Green'
  AND revenue_month = '2019-10-01';
```

### Q6: Count records in `stg_fhv_tripdata` (2019, non-null dispatching base)

```sql
SELECT COUNT(*)
FROM prod.stg_fhv_tripdata;
```

If you are creating the staging model manually, the core logic is:

```sql
SELECT
  dispatching_base_num,
  pickup_datetime,
  dropOff_datetime,
  PUlocationID AS pickup_location_id,
  DOlocationID AS dropoff_location_id,
  SR_Flag AS sr_flag,
  Affiliated_base_number AS affiliated_base_number
FROM <your_fhv_source_table>
WHERE dispatching_base_num IS NOT NULL
  AND pickup_datetime >= '2019-01-01'
  AND pickup_datetime < '2020-01-01';
```
