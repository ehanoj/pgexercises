# 4. Aggregation 
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
 
```
