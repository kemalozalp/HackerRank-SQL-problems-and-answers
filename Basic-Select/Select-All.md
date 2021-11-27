# Select All

## Question:

[Go to the question](https://www.hackerrank.com/challenges/select-all-sql/problem?isFullScreen=true)

Query all columns (attributes) for every row in the **CITY** table. The **CITY** table is described as follows:

| Field | Type |
| --- | --- |
| ID | Number |
| NAME | VARCHAR2(17) |
| COUNTRYCODE | VARCHAR2(3) |
| DISTRICT | VARCHAR2(20) |
| POPULATION | NUMBER |

## ANSWER

I should say 'WELCOME TO SQL' to y'all! This question most probably is the most basic one for SQL. We are simply asked to query the whole table. So, we are tested whether we know:

1. How to SELECT columns from a table
2. How to SELECT ALL columns
3. How to specify the table that the columns will be selected from.

The code is below.

```sql
SELECT *
FROM CITY;
```

### Fundamentals to Learn

- SELECT function is used to query columns from a table.
- Asteriks (*) means ALL. So, to SELECT all columns in a table, we do 'SELECT *'.
- We indicate the table we are chosing from using the FROM clause. Specifically, FROM Tablename
- MySQL queries end with a semicolon (;)