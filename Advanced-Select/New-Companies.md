# New Companies

## Question:

[Go to the question](https://www.hackerrank.com/challenges/the-company/problem?isFullScreen=true)

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT 
C.COMPANY_CODE,
C.FOUNDER,
COUNT(DISTINCT LM.LEAD_MANAGER_CODE),
COUNT(DISTINCT SM.SENIOR_MANAGER_CODE),
COUNT(DISTINCT M.MANAGER_CODE),
COUNT(DISTINCT EMPLOYEE_CODE)
FROM COMPANY AS C
INNER JOIN LEAD_MANAGER AS LM
ON C.COMPANY_CODE = LM.COMPANY_CODE
INNER JOIN SENIOR_MANAGER AS SM
ON C.COMPANY_CODE = SM.COMPANY_CODE
INNER JOIN MANAGER AS M
ON C.COMPANY_CODE = M.COMPANY_CODE
INNER JOIN EMPLOYEE AS E
ON C.COMPANY_CODE = E.COMPANY_CODE
GROUP BY C.COMPANY_CODE, C.FOUNDER
ORDER BY C.COMPANY_CODE;
```

If you want to see how to approach to the problem, continue reading. 

I'll start with restating what we are asked:

>write a query to print the company_code, founder name, total number of lead managers, total number of senior managers, total number of managers, and total number of employees. Order your output by ascending company_code.

Here are some warnings:

>- The tables may contain duplicate records.
>- The company_code is string, so the sorting should not be **numeric**. For example, if the company_codes are C_1, C_2, and C_10, then the ascending company_codes will be C_1, C_10, and C_2.

So, we should only select the DISTINCT values. Sure! What's next?

We want an output like this:

| company_code | founder | LM | SM | M | E |
| --- | --- | --- | --- | --- | --- |
| c1 | Monika | 1 | 2 | 1 | 2 |
| c2 | Samantha | 1 | 1 | 2 | 2 |

To list LM, SM, M, and E all at the same time, we should join existing tables fist. To do that, I'll use INNER JOIN statement and use COMPANY_CODE as my reference column.

```sql
FROM COMPANY AS C
INNER JOIN LEAD_MANAGER AS LM
ON C.COMPANY_CODE = LM.COMPANY_CODE
INNER JOIN SENIOR_MANAGER AS SM
ON C.COMPANY_CODE = SM.COMPANY_CODE
INNER JOIN MANAGER AS M
ON C.COMPANY_CODE = M.COMPANY_CODE
INNER JOIN EMPLOYEE AS E
ON C.COMPANY_CODE = E.COMPANY_CODE
```

What's next? Now, we have to select company_code, founder, LM, SM, M, and E. BUT REMEMBER! Only the distinct values.

```sql
SELECT
C.COMPANY_CODE,
C.FOUNDER,
COUNT(DISTINCT LM.LEAD_MANAGER_CODE),
COUNT(DISTINCT SM.SENIOR_MANAGER_CODE),
COUNT(DISTINCT M.MANAGER_CODE),
COUNT(DISTINCT EMPLOYEE_CODE)
FROM COMPANY AS C
INNER JOIN LEAD_MANAGER AS LM
ON C.COMPANY_CODE = LM.COMPANY_CODE
INNER JOIN SENIOR_MANAGER AS SM
ON C.COMPANY_CODE = SM.COMPANY_CODE
INNER JOIN MANAGER AS M
ON C.COMPANY_CODE = M.COMPANY_CODE
INNER JOIN EMPLOYEE AS E
ON C.COMPANY_CODE = E.COMPANY_CODE
GROUP BY C.COMPANY_CODE, C.FOUNDER
```

I used DISTINCT ON LM, SM, M, and E because company code and founder are unique values naturally. I grouped by company_code and founder before counting. Otherwise, I couldn't get numbers for each unique company.

Are we done? ALMOST! As always, we have to do some sorting:

```sql
SELECT
C.COMPANY_CODE,
C.FOUNDER,
COUNT(DISTINCT LM.LEAD_MANAGER_CODE),
COUNT(DISTINCT SM.SENIOR_MANAGER_CODE),
COUNT(DISTINCT M.MANAGER_CODE),
COUNT(DISTINCT EMPLOYEE_CODE)
FROM COMPANY AS C
INNER JOIN LEAD_MANAGER AS LM
ON C.COMPANY_CODE = LM.COMPANY_CODE
INNER JOIN SENIOR_MANAGER AS SM
ON C.COMPANY_CODE = SM.COMPANY_CODE
INNER JOIN MANAGER AS M
ON C.COMPANY_CODE = M.COMPANY_CODE
INNER JOIN EMPLOYEE AS E
ON C.COMPANY_CODE = E.COMPANY_CODE
GROUP BY C.COMPANY_CODE, C.FOUNDER
ORDER BY C.COMPANY_CODE;
```

See you in the next question!