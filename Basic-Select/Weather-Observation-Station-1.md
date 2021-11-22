# Weather Observation Station 1

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-1/problem?isFullScreen=true)

Query a list of CITY and STATE from the STATION table.
The STATION table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| CITY | VARCHAR2(21) |
| STATE | VARCHAR2(2) |
| LAT_N| NUMBER |
| LONG_W | NUMBER |

## ANSWER

This question tests if you know how to get certain columns from a table.
Specifically, we are asked to filter CITY and STATE columns from the STATION table.
This is SQL 101. However, don't forget that being good at fundamentals makes you a 
great player. Things to pay attention in this question are:

- How to separate column names when selecting multiple columns. We use comma (,) to do that.
- Also, find your style of writing a SQL query. With style, I mean 'formatting'. I personally
prefer writing each clause on a new line. This way, I can detect mistakes easier.



```sql
SELECT CITY, STATE 
FROM STATION;
```