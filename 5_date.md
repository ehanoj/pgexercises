# 5. Working with Timestamps 
## 1 Produce a timestamp for 1 a.m. on the 31st of August 2012
https://pgexercises.com/questions/date/timestamp.html
```
select '2012-08-31 01:00:00'::timestamp;
select cast('2012-08-31 01:00:00' AS timestamp);
```

## 2 Subtract timestamps from each other
https://pgexercises.com/questions/date/interval.html
```
select cast('2012-08-31 01:00:00' AS timestamp) - cast('2012-07-30 01:00:00' AS timestamp);
```

## 3 Generate a list of all the dates in October 2012
https://pgexercises.com/questions/date/series.html
```
select generate_series(timestamp '2012-10-01', '2012-10-31','1 day') as date;
```

## 4 Get the day of the month from a timestamp
https://pgexercises.com/questions/date/extract.html
```
select extract(days from date '2012-08-31');
```

## 5  Work out the number of seconds between timestamps
https://pgexercises.com/questions/date/interval2.html
```
select round(extract(epoch from (cast('2012-09-02 00:00:00' as timestamp) - cast('2012-08-31 01:00:00' as timestamp)))) ;
```

# 6 Work out the number of days in each month of 2012
https://pgexercises.com/questions/date/daysinmonth.html
```
select
extract(month FROM d) as mo,
count(d) || ' days' as len
FROM generate_series(timestamp '2012-01-01', '2012-12-31','1 day') as d
group by mo
order by mo;
```

# 7 Work out the number of days remaining in the month
https://pgexercises.com/questions/date/daysremaining.html
```
select (date_trunc('month',ts.te) + interval '1 month') - date_trunc('day',ts.te) 
from (select '2012-02-11 01:00:00'::timestamp as te) ts;
```

# 8 Work out the end time of bookings
https://pgexercises.com/questions/date/endtimes.html
```
select starttime,  (starttime + (slots*interval '30 mins')) as endtime 
from cd.bookings order by endtime desc, starttime desc limit 10;
```
