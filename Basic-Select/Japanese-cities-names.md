# Japanese Cities' Names

## Question:

[Go to the question](https://www.hackerrank.com/challenges/japanese-cities-name/problem?isFullScreen=true)

Query the names of all the Japanese cities in the CITY table. 
The COUNTRYCODE for Japan is JPN. The CITY table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| NAME | VARCHAR2(17) |
| COUNTRYCODE | VARCHAR2(3) |
| DISTRICT | VARCHAR2(20) |
| POPULATION | NUMBER |

## ANSWER

This question tests if you know how to filter certain information in a table.
Specifically, we are asked to filter 'ONLY NAMES' of cities in Japan using 'WHERE' clause. 
We are asked to filter by 'COUNTRYCODE' which is a VARCHAR2 type of data.
Therefore, you need to put the keyword in single quotes ('JPN'). 

```sql
SELECT NAME 
FROM CITY
WHERE COUNTRYCODE = 'JPN';
```