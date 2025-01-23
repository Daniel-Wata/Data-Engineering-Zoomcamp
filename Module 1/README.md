# Homework answers:

## 1. Understanding docker first run

For this one I ran

```
docker run -it --entrypoint bash python:3.12.8
```

and then in the bash opened I ran 

```
pip --version
```

which gave me the answer 24.3.1

## 2. Understanding Docker networking and docker-compose

In this one I ran the docker-compose file in this directory and managed to make the connection through the pgadmin using both db:5432 and postgres:5432.
The pgAdmin is in the container so it connects through it's network directly

## 3. Trip Segmentation Count


``` sql
select
	case
		when gt.trip_distance <= 1 then 'Up to 1 mile'
		when gt.trip_distance > 1 and gt.trip_distance <= 3 then 'Between 1 and 3 miles'
		when gt.trip_distance > 3 and gt.trip_distance <= 7 then 'Between 3 and 7 miles'
		when gt.trip_distance > 7 and gt.trip_distance <= 10 then 'Between 7 and 10 miles'
		when gt.trip_distance > 10 then 'Over 10 miles'
	end as category,
	COUNT(*)
from
    green_tripdata gt
WHERE gt.lpep_dropoff_datetime::DATE >= '2019-10-01' and gt.lpep_dropoff_datetime::DATE < '2019-11-01'
group by 1
```

## 4. Longest trip for each day

``` sql
select
	gt.lpep_pickup_datetime::DATE,
	max(gt.trip_distance )
	from
		green_tripdata gt
group by 1
order by max(gt.trip_distance ) desc
```

## 5. Three biggest pickup zones

``` sql
select
	tzl."Zone",
	SUM(total_amount)
	from
		green_tripdata gt
left join taxi_zone_lookup tzl on tzl."LocationID"  = gt."PULocationID" 
where gt.lpep_pickup_datetime::DATE = '2019-10-18'
group by 1
having SUM(total_amount) > 13000
```

## 6. Largest tip

``` sql
select
	tzld."Zone",
	MAX(gt.tip_amount) as tip
from
		green_tripdata gt
left join taxi_zone_lookup tzl on
	tzl."LocationID" = gt."PULocationID"
left join taxi_zone_lookup tzld on
	tzld."LocationID" = gt."DOLocationID"
where
	gt.lpep_pickup_datetime::DATE >= '2019-10-01'
	and gt.lpep_pickup_datetime::DATE < '2019-11-01'
	and tzl."Zone" = 'East Harlem North'
group by
	1
order by
	MAX(gt.tip_amount) desc
```

## 7. Terraform setup

Typically you would run

init, plan, apply and destroy

but plan can be skipped and directly run apply, the auto-approve parameter will skip approval necessity, so the right answer would be
terraform init, terraform apply -auto-approve, terraform destroy