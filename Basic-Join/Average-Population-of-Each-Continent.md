# Population Census

## Question:

[Go to the question](https://www.hackerrank.com/challenges/average-population-of-each-continent/problem?isFullScreen=true)

Given the CITY and COUNTRY tables, query the names of all the continents (COUNTRY.Continent) and their respective average city populations (CITY.Population) rounded down to the nearest integer.

**Note:** CITY.CountryCode and COUNTRY.Code are matching key columns.

**Input Format**

The **CITY** and **COUNTRY** tables are described as follows:

**CITY**

| Field | Type |
| --- | --- |
| ID | NUMBER |
| NAME | VARCHAR2(17) |
| COUNTRYCODE | VARCHAR2(3) |
| DISCTRICT | VARCHAR2(20) |
| POPULATION | NUMBER |

**COUNTRY**

| Field | Type |
| --- | --- |
| CODE | VARCHAR2(3) |
| NAME | VARCHAR2(44) |
| CONTINENT | VARCHAR2(13) |
| REGION | VARCHAR2(25) |
| SURFACEAREA | NUMBER |
| INDEPYEAR | VARCHAR2(5) |
| POPULATION | NUMBER |
| LIFEEXPECTANCY | VARCHAR2(4) |
| GNP | NUMBER |
| GNPOLD | VARCHAR2(9) |
| LOCALNAME | VARCHAR2(44) |
| HEADOFSTATE | VARCHAR2(32) |
| CAPITAL | VARCHAR2(4) |
| CODE | VARCHAR2(2) |

## ANSWER

This questions tests whether you know how to 

1. SELECT a specific column from a table
2. calculate the AVERAGE of values in a column
3. round numbers to the desired integer or decimal
4. JOIN two tables using a common column as a reference
5. GROUP values BY a column

Here is the code: 

```sql
SELECT COUNTRY.CONTINENT, FLOOR(AVG(CITY.POPULATION))
FROM COUNTRY
INNER JOIN CITY
ON COUNTRY.CODE = CITY.COUNTRYCODE
GROUP BY COUNTRY.CONTINENT;
```

The first trick here is to know what JOIN function to use. For that, we have to look at each table, but we don't have such chance here. So, we played it safe and used an INNER JOIN clause to join only the matching values from the two columns of two tables. Specifically, we picked COUNTRYCODE column from the CITY table and CODE column from the COUNTRY table. Once the values in two columns match, MySQL will join all the columns from those rows with matching values together.

The second trick here is to use the AVG() function over CITY.POPULATION column to calculate the average of the population of all cities in Asia.

The third trick is to use the FLOOR() function over AVG(CITY.POPULATION) to round DOWN the average number of population to the nearest integer.

Finally, you should use GROUP BY clause to group cities by COUNTRY before calcualating the average population by CONTINENT.

### Fundamentals to Learn

- FLOOR() is used to round DOWN an floating number to the nearest integer.
- AVG() is used to calculate the average of values.
- INNER JOIN is used to match two tables using two columns as references.
- Any JOIN function comes after the FROM clause.
- After INNER JOIN, you specify the name of the second table you want to join.
- Once tables specificed, you use ON clause to specify which columns in each table are going to be used as references.
- Only rows with matching values are selected for the JOIN.
- In queries where you JOIN two tables, you have to indicate table names before column names, such as when selecting city names 'CITY.NAME' or when filtering cities in Africa 'COUNTRY.CONTINENT = 'AFRICA''.
- GROUP BY clause is used to group values by another field. Here, we GROUP cities BY CONTINENT.