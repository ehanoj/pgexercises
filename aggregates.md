# 4. Aggregation 
## 13 List the total hours booked per named facility
https://pgexercises.com/questions/aggregates/fachours3.html
```
select b.facid, f.name, sum(slots)*0.50 as hours 
from cd.bookings b 
left join cd.facilities f on b.facid = f.facid 
group by b.facid, f.name order by b.facid;
```

## 14 List each member's first booking after September 1st 2012
https://pgexercises.com/questions/aggregates/nbooking.html
```
select surname, firstname, b.memid, min(starttime) from cd.bookings b 
left join cd.members m on m.memid=b.memid 
where starttime > '2012-09-01' group by b.memid, surname, firstname order by memid;
```

## 15 Produce a list of member names, with each row containing the total member count 
https://pgexercises.com/questions/aggregates/countmembers.html
```
select (select count(*) from cd.members) as count, firstname, surname
from cd.members order by joindate
```

## 16 Produce a numbered list of members
https://pgexercises.com/questions/aggregates/nummembers.html
```
select row_number() OVER(), firstname, surname from cd.members;
```

## 17 Output the facility id that has the highest number of slots booked, again
https://pgexercises.com/questions/aggregates/fachours4.html
```
  select facid, s from 
  (   select facid, sum(slots) s from cd.bookings group by facid   ) 
  where s=
  (   select max(x) from
    (  	select sum(slots) x from cd.bookings group by facid    )    ) ;
```

## 18 Rank members by (roudned) hours used
https://pgexercises.com/questions/aggregates/rankmembers.html
```
SELECT firstname, surname, round(sum(slots)*.5,-1) AS hour, rank() OVER (ORDER BY round(sum(slots)*.5,-1) DESC) AS rank 
FROM cd.bookings b 
LEFT JOIN cd.members m ON b.memid=m.memid
GROUP BY firstname, surname, b.memid ORDER BY rank, surname, firstname;
```

## 19 Find the top three revenue generating facilities
https://pgexercises.com/questions/aggregates/facrev3.html
```
select name, rank() OVER (order by sum(cost) desc) as rank from (
  select name, 
	(
  	CASE WHEN memid=0 THEN slots*guestcost
  	ELSE slots*membercost
  	END
	) AS cost
	from cd.bookings b 
	LEFT JOIN cd.facilities f ON b.facid=f.facid
  ) 
group by name
order by rank
limit 3;
```

## 20 Classify facilities by value
https://pgexercises.com/questions/aggregates/classify.html
```
select name, (case rev when 1 then 'high' when 2 then 'average' else 'low' END) as revenue 
FROM(
	select name, ntile(3) OVER (order by sum(cost) desc) as rev from
	(
  		select name, 
			(
		  	CASE WHEN memid=0 THEN slots*guestcost
		  	ELSE slots*membercost
		  	END
			) AS cost
			from cd.bookings b 
			LEFT JOIN cd.facilities f ON b.facid=f.facid
	) 
	group by name
    order by rev, name
);
```

## 21 Calculate the payback time for each facility
https://pgexercises.com/questions/aggregates/payback.html
```
select name,  
	(
	initialoutlay/
	 	(
		 sum(
  			CASE WHEN memid=0 THEN slots*guestcost
  			ELSE slots*membercost
  			END
			)/3-monthlymaintenance
		)
	)
from cd.bookings b 
LEFT JOIN cd.facilities f ON b.facid=f.facid
group by name, monthlymaintenance, initialoutlay
order by name;
```

## 22 Calculate a rolling average of total revenue
https://pgexercises.com/questions/aggregates/rollingavg.html
my own:
```
select ad, avg(c) from
(
	select a.date as ad, b.date as bd, avg(b.cost) as c from 
	(
		select distinct DATE(starttime) as date
		from cd.bookings
	) a,
	(
		select DATE(starttime) date,  
	 	sum(
  			CASE WHEN memid=0 THEN slots*guestcost
  			ELSE slots*membercost
  			END
			) AS cost
		from cd.bookings b 
		LEFT JOIN cd.facilities f ON b.facid=f.facid
		group by DATE(starttime)
		order by DATE(starttime)
	) b
	WHERE a.date >= b.date AND a.date-14 <= b.date
	group by a.date, b.date
	order by a.date, b.date
)
where extract(MONTH from ad) = 8
group by ad;
```

inspired by help:
```
select g.date, 
	(
		select sum(
			CASE WHEN memid=0 THEN slots*guestcost
  			ELSE slots*membercost
  			END
			) AS revenue
		from cd.bookings b 
		LEFT JOIN cd.facilities f ON b.facid=f.facid
		WHERE g.date >= DATE(starttime) AND g.date-14 <= DATE(starttime)	
	)/15 as rev
from
	(
	select 	cast(generate_series(timestamp '2012-08-01',
			'2012-08-31','1 day') as date) as date
	) as g
```