# Revising the Select Query I

## Question:

[Go to the question](https://www.hackerrank.com/challenges/revising-the-select-query/problem?isFullScreen=true)

Query all columns for all American cities in the CITY table with populations larger than 100000. The CountryCode for America is USA.

The CITY table is described as follows:

| Field | Type |
| --- | --- |
| ID | Number |
| NAME | VARCHAR2(17) |
| COUNTRYCODE | VARCHAR2(3) |
| DISTRICT | VARCHAR2(20) |
| POPULATION | NUMBER |

## ANSWER

This questions tests whether you know how to:

1. SELECT ALL columns in a table
2. SELECT FROM table XXX
3. FILTER CERTAIN VALUES
4. FILTER FOR MULTIPLE CONDITIONS

The code is below.

```sql
SELECT *
FROM CITY
WHERE POPULATION > 100000 AND COUNTRYCODE ='USA';
```

### Fundamentals to Learn

- SELECT function is used to query columns from a table.
- Asteriks (*) means ALL. So, to SELECT all columns in a table, we do 'SELECT *'.
- We indicate the table we are chosing from using the FROM clause. Specifically, FROM Tablename
- WHERE clause is used to FILTER tables. The conditions comes right after the WHERE clause.
- When you want to pose multiple conditions, we use AND and/or OR to connect multiple conditions.
- > means 'greater'; < means 'less than'; and = means 'equal'.
- Numbers are put without quotation marks (''). However, string values are put between quotation marks.
- MySQL queries end with a semicolon (;)