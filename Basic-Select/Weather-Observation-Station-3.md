# Weather Observation Station 3

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-3/problem?isFullScreen=true)

Query a list of CITY names from STATION for cities that have an even ID number. Print the results in any order, but exclude duplicates from the answer.
The STATION table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| CITY | VARCHAR2(21) |
| STATE | VARCHAR2(2) |
| LAT_N| NUMBER |
| LONG_W | NUMBER |

## ANSWER

Okay! Things are getting more complicated. This question tests you whether you know:

- How to filter information in a table using WHERE clause
- MODULO of a number. This is what we use to find even or odd numbers in algorithms
- Filtering only unique entries. To do that we will use DISTINCT clause

Here we go:

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE (ID % 2) = 0;
```

Alternatively: 

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE MOD(ID,2) = 0; -- We changed this line. See the code line in the above code snippet for WHERE clause.
```

Fundamentals to learn:

- DISTINCT is used to find unique values.
- MODULO is used to find even and odd numbers but not limited to even or odd numbers. You can basically find any number which is divisible by any number using MODULO.
- WHERE clause always comes after SELECT and FROM clauses.