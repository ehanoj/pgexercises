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
