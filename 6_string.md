# 6. String
## 1. Format the names of members
https://pgexercises.com/questions/string/concat.html
```
select surname ||', '|| firstname from cd.members;
```
## 2. Find facilities by a name prefix
https://pgexercises.com/questions/string/like.html
```
select * from cd.facilities where name like 'Tennis%';
```
## 3. Perform a case-insensitive search
https://pgexercises.com/questions/string/case.html
```
select * from cd.facilities where name ilike 'tennis%';
```
## 4. Find telephone numbers with parentheses
https://pgexercises.com/questions/string/reg.html
```
select memid, telephone from cd.members where telephone like '(%)%';
```
## 5. Pad zip codes with leading zeroes
https://pgexercises.com/questions/string/pad.html
```
select lpad(zipcode::text,5,'0') from cd.members;
```
## 6. Count the number of members whose surname starts with each letter of the alphabet 
https://pgexercises.com/questions/string/substr.html
```
select substring(surname for 1) as s, count(*) from cd.members group by s order by s;
```
## 7. Clean up telephone numbers
https://pgexercises.com/questions/string/translate.html
```
select memid,  translate(telephone,'[() -]','') from cd.members where telephone ~ '[()-]';
select memid,  regexp_replace(telephone,'[()\- ]','','g') from cd.members where telephone ~ '[()-]';
select memid,  regexp_replace(telephone,'[^0-9]','','g') from cd.members where telephone ~ '[()-]';
```
