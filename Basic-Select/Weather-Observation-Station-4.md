# Weather Observation Station 4

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-4/problem?isFullScreen=true)

Find the difference between the total number of CITY entries in the table and the number of distinct CITY entries in the table.
The STATION table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| CITY | VARCHAR2(21) |
| STATE | VARCHAR2(2) |
| LAT_N| NUMBER |
| LONG_W | NUMBER |

where LAT_N is the northern latitude and LONG_W is the western longitude.

For example, if there are three records in the table with CITY values 'New York', 'New York', 'Bengalaru', there are 2 different city names: 'New York' and 'Bengalaru'. The query returns 1, because

**total number of records - number of unique city names = 3 - 2 = 1.**

## ANSWER

This question tests you whether you know:

- how to count the total number of entries for a given variable. To do that we are going to use COUNT() function.
- Filtering only unique entries. To do that we will use DISTINCT clause
- how to do basic when selecting entries

Here we go:

```sql
SELECT COUNT(CITY) - COUNT(DISTINCT CITY)
FROM STATION;
```

Fundamentals to learn:

- DISTINCT is used to find unique values.
- COUNT() function is used to count the number of variables in a column
- You can perform math operations between aggregating functions (COUNT, SUM, etc...)