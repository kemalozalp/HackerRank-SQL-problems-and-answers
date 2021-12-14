# Placements

## Question:

[Go to the question](https://www.hackerrank.com/challenges/placements/problem?isFullScreen=true)

You are given three tables: Students, Friends and Packages. Students contains two columns: ID and Name. Friends contains two columns: ID and Friend_ID (ID of the ONLY best friend). Packages contains two columns: ID and Salary (offered salary in $ thousands per month).

**Students**

| Column | Type |
| --- | --- |
| ID | Integer |
| Name | String |

**Friends**

| Column | Type |
| --- | --- |
| ID | Integer |
| Friend_ID | Integer |

**Packages**

| Column | Type |
| --- | --- |
| ID | Integer |
| Salary | Float |

Write a query to output the names of those students whose best friends got offered a higher salary than them. Names must be ordered by the salary amount offered to the best friends. It is guaranteed that no two students got same salary offer.

** Sample Input ** 

Students

| ID | Name |
| --- | --- |
| 1 | Ashley |
| 2 | Samantha |
| 3 | Julia |
| 4 | Scarlet |

Friends

| ID | Name |
| --- | --- |
| 1 | 2 |
| 2 | 3 |
| 3 | 4 |
| 4 | 1 |

Packages

| ID | Name |
| --- | --- |
| 1 | 15.20 |
| 2 | 10.06 |
| 3 | 11.55 |
| 4 | 12.12 |

**Sample Output**

```sql
Samantha
Julia
Scarlet
```

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT S.NAME
FROM STUDENTS AS S
INNER JOIN PACKAGES AS P
ON S.ID = P.ID
INNER JOIN FRIENDS AS F
ON S.ID = F.ID
INNER JOIN PACKAGES AS P1
ON F.FRIEND_ID = P1.ID
WHERE P1.SALARY > P.SALARY
ORDER BY P1.SALARY;
```

If you want to see how to approach to the problem, continue reading. 

I'll start with restating what we are asked:

>Write a query to output the names of those students whose best friends got offered a higher salary than them.

It sounds like we have to compare salary of a person with the salary of their best friend. Then, we need the two salaries in the same table. Let's list what attributes we need in a table so that we can successfully query what we are asked:

1. Name of the person since we will output their name
2. Salary of the person since we are going to compare it with the salary of their best friend
3. Salary of the person's best friends for comparison

Those are the essentials. The tricky part of this questions to me was to figure out how to get salary for the person and salary for their best friend separately. To solve this issue, I decided to join the table carrying the salary information, PACKAGES, twice. I can join it once by matching the ID values from STUDENTS and PACKAGES tables and once again by matching the FRIEND_ID values from the FRIENDS table with ID values from the PACKAGES table. This way, I'll have both salary values in one table without any confusion. So, let's do it!

```sql
SELECT S.ID, S.NAME, P.SALARY, F.FRIEND_ID, P1.SALARY
FROM STUDENTS AS S
INNER JOIN PACKAGES AS P
ON S.ID = P.ID
INNER JOIN FRIENDS AS F
ON S.ID = F.ID
INNER JOIN PACKAGES AS P1
ON F.FRIEND_ID = P1.ID
```
Run this code and see the result for yourself. Is this all? Of course not. Because above code outputs selected attributes without filtering. Remember that we have to output the names of students whose best friends earn more than them. I'll do the filtering using a WHERE clause.

```sql
SELECT S.ID, S.NAME, P.SALARY, F.FRIEND_ID, P1.SALARY
FROM STUDENTS AS S
INNER JOIN PACKAGES AS P
ON S.ID = P.ID
INNER JOIN FRIENDS AS F
ON S.ID = F.ID
INNER JOIN PACKAGES AS P1
ON F.FRIEND_ID = P1.ID
WHERE P1.SALARY > P.SALARY
```
Are we done? Of couse not. I suspect that we need to do some sorting as always. Also, let's rearrange the first line of the code so that we only output the names of students.

> Names must be ordered by the salary amount offered to the best friends.

```sql
SELECT S.NAME
FROM STUDENTS AS S
INNER JOIN PACKAGES AS P
ON S.ID = P.ID
INNER JOIN FRIENDS AS F
ON S.ID = F.ID
INNER JOIN PACKAGES AS P1
ON F.FRIEND_ID = P1.ID
WHERE P1.SALARY > P.SALARY
ORDER BY P1.SALARY;
```

Are we done? YES! However, I want to point out one important thing. Look below.

> It is guaranteed that no two students got same salary offer.

We are lucky that there are no duplicates in tables. If there were duplicates, we had to use DISTINCT or some sort of GROUP BY clause to get rid of duplicate values. But we are lucky. So, that's all!

See you in the next question!