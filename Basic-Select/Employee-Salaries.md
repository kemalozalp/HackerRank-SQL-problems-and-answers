# Employee Names

## Question:

[Go to the question](https://www.hackerrank.com/challenges/salary-of-employees/problem?isFullScreen=true)

Write a query that prints a list of employee names (i.e.: the name attribute) for employees in **Employee** having a salary greater than $2000 per month who have been employees for less than 10 months. Sort your result by ascending employee_id.

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
Michael
Todd
Joe
```

### Explanation

Angela has been an employee for 1 month and earns $3443 per month.

Michael has been an employee for 6 months and earns $2017 per month.

Todd has been an employee for 5 months and earns $3996 per month.

Joe has been an employee for 9 months and earns $3547 per month.

We order our output by ascending employee_id.

## ANSWER

This questions tests whether you know how to 

1. filter a table using the WHERE clause
2. filter for two different conditons
3. order values using ORDER BY clause. Here is the code that solves this question.

Here is the code: 

```sql
SELECT NAME
FROM EMPLOYEE
WHERE SALARY > 2000 AND MONTHS < 10
ORDER BY EMPLOYEE_ID;
```

We didn't do anything special here. The only trick is to provide two conditions rather than one. It might not be obvious whether to use OR or AND to connect the two different conditions for a beginner. In this case, I reread the question "... having a salary greater than $2000 per month who have been employees for less than 10 months" and place AND or OR between the conditions. Try it:

1. "having a salary greater than $2000 per month **AND** who have been employees for less than 10 months."

2. "aving a salary greater than $2000 per month **OR** who have been employees for less than 10 months."

Does 1 or 2 provide the same meaning with the question? I believe the first one. 

### Fundamentals to Learn

- WHERE comes before the ORDER BY clause.
- WHERE can get two different conditions. Remember that you have to connect these conditions with OR or AND.
- If you can't decide to use OR or AND, try to rewrite the question using OR or AND. This generally reveals the answer.
- The attribute comes after ORDER BY is the attribute that the output is going to be sorted by.
- If you don't indiciate the type of order, i.e. ASCEDING (ASC) or DESCENDING (DESC), the output will be sorted in ASCENDING order by default.
- Ascending order correspond to the alphabetical order.