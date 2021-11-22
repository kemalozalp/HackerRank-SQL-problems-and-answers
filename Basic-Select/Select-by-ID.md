# Select by ID

## Question:

[Go to the question](https://www.hackerrank.com/challenges/select-by-id/problem?isFullScreen=true)

Query all columns for a city in CITY with the ID 1661.
The CITY table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| NAME | VARCHAR2(17) |
| COUNTRYCODE | VARCHAR2(3) |
| DISTRICT | VARCHAR2(20) |
| POPULATION | NUMBER |

## ANSWER

```sql
SELECT * -- Asterix (*) to select all columns
FROM CITY
WHERE ID = 1661; -- ATTENTION! I didn't use single quotes around 1661 because the data type for the ID column is NUMBER.
```