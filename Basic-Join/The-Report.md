# The Report

## Question:

[Go to the question](https://www.hackerrank.com/challenges/the-report/problem?isFullScreen=true)

You are given two tables: Students and Grades. Students contains three columns ID, Name and Marks.

| Column | Type |
| --- | --- |
| ID | Integer |
| Name | String |
| Marks | Integer |

Grades contains the following data:

| Grade | Min_Mark | Max_Mark |
| --- | --- | --- |
| 1 | 0 | 9 |
| 2 | 10 | 19 |
| 3 | 20 | 29 |
| 4 | 30 | 39 |
| 5 | 40 | 49 |
| 6 | 50 | 59 |
| 7 | 60 | 69 |
| 8 | 70 | 79 |
| 9 | 80 | 89 |
| 10 | 90 | 100 |

Ketty gives Eve a task to generate a report containing three columns: Name, Grade and Mark. Ketty doesn't want the NAMES of those students who received a grade lower than 8. The report must be in descending order by grade -- i.e. higher grades are entered first. If there is more than one student with the same grade (8-10) assigned to them, order those particular students by their name alphabetically. Finally, if the grade is lower than 8, use "NULL" as their name and list them by their grades in descending order. If there is more than one student with the same grade (1-7) assigned to them, order those particular students by their marks in ascending order.

Write a query to help Eve.

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long than just getting the answer:

```sql
SELECT
(CASE WHEN G.GRADE < 8 THEN NULL
ELSE S.NAME END),
G.GRADE,
S.MARKS
FROM STUDENTS AS S
INNER JOIN GRADES AS G
ON S.MARKS BETWEEN G.MIN_MARK AND G.MAX_MARK
ORDER BY G.GRADE DESC, S.NAME, S.MARKS;
```

If you want to see how to approach to the problem, continue reading. 

There are two parts two this questions:

1. Pick the correct GRADE corresponding to given MARK range
2. Replace names with *NULL* for those who has a grade less than 8.

I'll start with the first part and clarify something before I dive into the code.
Honestly, I thought the second table was completely useless. Because, I can define multiple CASES for MARKS and get the value for the correct case. Something like this:

- If MARK < 10 THEN 1
- If MARK between 10 AND 19 THEN 2
- If MARK between 20 AND 29 THEN 3
- ...

Yes, this is really tedious, but why not?! Then, I learned that SQL can actually compare a given MARK whether it is between Min_Mark and Max_Mark at each row. Moreover, when it finds the row that the given MARK is between Min_Mark and Max_Mark, it can get the GRADE for you.
So, we can do start by getting the corresponding GRADES to the MARKS in the STUDENTS table.

```sql
SELECT S.NAME, G.GRADE, S.MARKS
FROM STUDENTS AS S
INNER JOIN GRADES AS G
ON S.MARKS BETWEEN G.MIN_MARK AND G.MAX_MARK;
```

If you try the code above, you'll see that this code will generate an ouput very similar to the first table but including corresponding grades. We can proceed to the second part now.

Let's replace NAMES with NULL in CASE that person has a GRADE less than 8. This is a classic IF condition THEN outcome type of situation. In SQL, we use CASE ... WHEN ... THEN clause in such situations. A minor modification on the code above will suffice to do the necessary changes.

```sql
SELECT
(CASE WHEN G.GRADE < 8 THEN NULL
ELSE S.NAME END),
G.GRADE,
S.MARKS
FROM STUDENTS AS S
INNER JOIN GRADES AS G
ON S.MARKS BETWEEN G.MIN_MARK AND G.MAX_MARK;
```

With this modification, we replaced students' names with NULL. We are almost done except some formatting.

>The report must be in descending order by grade -- i.e. higher grades are entered first. If there is more than one student with the same grade (8-10) assigned to them, order those particular students by their name alphabetically.

To do that, we will ORDER the output BY GRADE first in DESCENDING ORDER and BY NAME second in ASCENDING ORDER. The code will be:

```sql
SELECT
(CASE WHEN G.GRADE < 8 THEN NULL
ELSE S.NAME END),
G.GRADE,
S.MARKS
FROM STUDENTS AS S
INNER JOIN GRADES AS G
ON S.MARKS BETWEEN G.MIN_MARK AND G.MAX_MARK
ORDER BY G.GRADE DESC, S.NAME;
```

One final step thing to do:

>Finally, if the grade is lower than 8, use "NULL" as their name and list them by their grades in descending order. If there is more than one student with the same grade (1-7) assigned to them, order those particular students by their marks in ascending order.

We already changed names with NULL. We just have to list NULLs in ASCENDING ORDER BY MARKS. So, the code will be: 

```sql
SELECT
(CASE WHEN G.GRADE < 8 THEN NULL
ELSE S.NAME END),
G.GRADE,
S.MARKS
FROM STUDENTS AS S
INNER JOIN GRADES AS G
ON S.MARKS BETWEEN G.MIN_MARK AND G.MAX_MARK
ORDER BY G.GRADE DESC, S.NAME, S.MARKS;

Good job, pal! 

See you in the next question!