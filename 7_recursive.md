# 7. Recursive
## 1. Find the upward recommendation chain for member ID 27
https://pgexercises.com/questions/recursive/getupward.html
```
WITH recursive mmb(rec) AS (
						SELECT recommendedby FROM cd.members WHERE memid=27
						UNION ALL
						SELECT recommendedby FROM mmb
						INNER JOIN cd.members ON memid=rec
						)
SELECT  rec, firstname, surname FROM mmb 
	inner join  cd.members m on rec = memid
ORDER BY rec DESC;
```

## 2. Find the downward recommendation chain for member ID 1
https://pgexercises.com/questions/recursive/getdownward.html
```
WITH recursive mmb(mid) AS (
						SELECT memid FROM cd.members WHERE recommendedby=1
						UNION ALL
						SELECT memid FROM mmb
						INNER JOIN cd.members ON recommendedby=mid
						)
SELECT  mid, firstname, surname FROM mmb 
	inner join  cd.members m on mid = memid
ORDER BY mid;
```

## 3. Produce a CTE that can return the upward recommendation chain for any member 
https://pgexercises.com/questions/recursive/getupwardall.html
```
WITH recursive mmb(mid, rec) AS (
						SELECT memid, recommendedby FROM cd.members WHERE memid in (12,22) 
						UNION ALL
						SELECT mid, recommendedby FROM mmb
						INNER JOIN cd.members ON memid=rec
						)
SELECT  mid, rec, firstname, surname FROM mmb 
	inner join  cd.members m on rec = memid
ORDER BY mid, rec DESC;
```
