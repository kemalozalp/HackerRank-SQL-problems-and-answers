# Employee Names

## Question:

[Go to the question](https://www.hackerrank.com/challenges/name-of-employees/problem?isFullScreen=true)

Write a query that prints a list of employee names (i.e.: the name attribute) from the **Employee** table in alphabetical order.

**Input Format**

The **Employee** table containing employee data for a company is described as follows:

| Column | Type |
| --- | --- |
| employee_id | Integer |
| name | String |
| months | Integer |
| salary | Integer |

where employee_id is an employee's ID number, name is their name, months is the total number of months they've been working for the company, and salary is their monthly salary.

### Sample Input

| employee_id | name | months | salary |
| --- | --- | --- | --- |
| 12228 | Rose | 15 | 1968 |
| 33645 | Angela | 1 | 3443 |
| 45692 | Frank | 17 | 1608 |
| 56118 | Patrick | 7 | 1345 |
| 59725 | Lisa | 11 | 2330 |
...

### Sample Ouput

```
Angela
Bonnie
Frank
Joe
Kimberly
Lisa
Michael
Patrick
Rose
Todd
```
## ANSWER

This questions tests whether you know how to order values using ORDER BY clause. Here is the code that solves this question.

```sql
SELECT NAME
FROM EMPLOYEE
ORDER BY NAME;
```
Pretty basic question. ORDER BY comes after we SELECT the attribute to list from the EMPLOYEE table.

### Fundamentals to Learn

- The attribute comes after ORDER BY is the attribute that the output is going to be sorted by.
- If you don't indiciate the type of order, i.e. ASCEDING (ASC) or DESCENDING (DESC), the output will be sorted in ASCENDING order by default.
- Ascending order correspond to the alphabetical order.