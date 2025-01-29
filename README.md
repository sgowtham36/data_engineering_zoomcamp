
## Docker, SQL


#### 1. pip version python 3.12.8
```
docker run -it --entrypoint=bash python:3.12.8
``` 
```
pip --version
```

#### 2. Docker networking and docker-compose
Inside docker-compose, pgAdmin uses hostname or container name for host and container port of 5432 for port. so we get both results db:5432 and postgres:5432

#### 3. segmentation
```
select 
case 
	when trip_distance <= 1 then 'Up to 1'
	when trip_distance > 1 and trip_distance <= 3 then 'above 1-3'
	when trip_distance > 3 and trip_distance <= 7 then 'above 3-7'
	when trip_distance > 7 and trip_distance <= 10 then 'above 7-10'
else 
	'over 10'
end as trip_meter,
count(*) as num_of_trips
from green_taxi_trip
where cast(lpep_pickup_datetime as date) >= '2019-10-01'
	and cast(lpep_dropoff_datetime as date) < '2019-11-01'
group by trip_meter;
```
#### 4. max distance by grouping
```
select cast(lpep_pickup_datetime as date) as pickup_date,max(trip_distance) as max_distance
from green_taxi_trip
group by pickup_date order by max_distance desc
```

#### 5. zone with max amount

```
select "Zone",sum(a.total_amount) as total_amount
from (select * from green_taxi_trip where cast(lpep_pickup_datetime as date) = '2019-10-18') as a left join zone as b
on "PULocationID" = "LocationID"
group by "Zone"
having sum(a.total_amount) > 13000
order by total_amount desc limit 10
```

#### 6. max Tip amount from one zone.
```
select
	cast(lpep_pickup_datetime as date) data_pickup,
	tip_amount,
	pickzone."Zone" Zone_pick,
	dropzone."Zone" Zone_drop
from green_taxi_trip trips left join zone pickzone
	on trips."PULocationID" = pickzone."LocationID"
left join zone dropzone
	on trips."DOLocationID" = dropzone."LocationID"
where 
	cast(lpep_pickup_datetime as date) >= '2019-10-01'
	and cast(lpep_pickup_datetime as date) <= '2019-10-31'
	and pickzone."Zone" = 'East Harlem North'
order by tip_amount desc;
```