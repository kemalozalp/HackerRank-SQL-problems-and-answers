# Symmetric Pairs

## Question:

[Go to the question](https://www.hackerrank.com/challenges/symmetric-pairs/problem?isFullScreen=true)

You are given a table, Functions, containing two columns: X and Y.

| Column | Type |
| --- | --- |
| X | Integer |
| Y | Integer |

Two pairs (X1, Y1) and (X2, Y2) are said to be symmetric pairs if X1 = Y2 and X2 = Y1.
Write a query to output all such symmetric pairs in ascending order by the value of X. List the rows such that X1 ≤ Y1.

** Sample Input ** 

| X| Y |
| --- | --- |
| 20 | 20 |
| 20 | 20 |
| 20 | 21 |
| 23 | 22 |
| 22 | 23 |
| 21 | 20 |

**Sample Output**

```sql
20 20
20 21
22 23
```

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT F1.X, F1.Y
FROM FUNCTIONS AS F1
CROSS JOIN FUNCTIONS AS F2
WHERE F1.X <= F1.Y
AND F1.X = F2.Y
AND F2.X = F1.Y
GROUP BY F1.X, F1.Y
HAVING COUNT(CASE WHEN F1.X = F1.Y THEN F1.X END) > 2
OR COUNT(CASE WHEN F1.X < F1.Y THEN F1.X END) = 1
ORDER BY F1.X;
```

If you want to see how to approach to the problem, continue reading. 

***

### Explanation

I'll start with restating what we are asked:

>Write a query to output all such symmetric pairs in ascending order by the value of X. List the rows such that X1 ≤ Y1.

Hmm. Let's make sure we understand what a symmetric pair is.

>Two pairs (X1, Y1) and (X2, Y2) are said to be symmetric pairs if X1 = Y2 and X2 = Y1.

So, here are some examples to symmetric pairs:

```sql
(1,1) and (1,1) is a symmetric pair. X1 = Y2 = 1 and X2 = Y1 = 1.
(1,2) and (2,1) is a symmetric pair. X1 = Y2 = 1 and X2 = Y1 = 2.
(3,1) and (1,3) is a symmetric pair. X1 = Y2 = 3 and X2 = Y1 = 1.
```

Good. I guess we, all, know clear on what a symmetric pair is. Now! How can we find symmetric pairs in a table? Well, first thing that comes to my mind is to use the rules that are given in the questions. For a pair to be symmetric, X1 shoild be equal to Y2, and X2 should be equal to Y1. We can define these rules using a WHERE clause:

```sql
WHERE X1 = Y2 AND X2 = Y1
```

I have a confession to make. I am still new to SQL. Therefore, I still approach SQL problems with an approach that I developed using programming languages like, FORTRAN, PYTHON, or MATLAB. If this were a question that I coded in PYTHON, for example, I would write a code something like this:

```sql
1. Pick first pair
2. Compare it with other pairs and see if X1 = Y2 and X2 = Y1
3. If yes, assign it to a variable. If not, go to next pair
4. Repeat 2,3
```

However, SQL has a different logic. We cannot loop in SQL, or at least, this is what I think after solving 100+ HackerRank and W3 questions. SQL works in rows. Imagine that you have a table like below and a filtering rule as WHERE X1 = Y2 AND X2 = Y1.

| X| Y | X | Y |
| --- | --- | --- | --- |
| 20 | 20 | 20 | 20 |
| 20 | 20 | 20 | 20 |
| 20 | 21 | 20 | 21 |
| 23 | 22 | 23 | 22 |
| 22 | 23 | 22 | 23 |
| 21 | 20 | 21 | 20 |

Then, SQL would compare X and Y values at each row. For the first row, it would check if X1, which is 20, is equal to Y2, which is also 20. And it would check if X2, 20, is equal to Y1,20. Since they X1 = Y2 and X2 = Y1, this would pass the filter. But is it enough? NO! Because we want to compare each pair with all other pairs.

The way to do this in SQL is to use a CROSS JOIN function. CROSS JOIN works differently from other JOIN functions. When joining two tables, CROSS JOIN gets the first row from the first table and merges it with the second table. Then, it gets the second row from the first table and merges it with the second table again. But wait, how can you merge one row from the first table with an entire table? Well, it duplicates the first row as many times as the number of rows the second table has. Too many words. Well, a picture means more than a thousand words. See the link below:

https://www.w3resource.com/mysql/advance-query-in-mysql/mysql-cross-join.php

Now, imagine we cross join this sample table below with itself.

| X| Y |
| --- | --- |
| 18 | 21 |
| 30 | 13 |

It will transform into this:

| X | Y | X | Y | 
| --- | --- | --- | --- |
| 18 | 21 | 18 | 21 |
| 18 | 21 | 30 | 13 |
| 30 | 13 | 18 | 21 |
| 30 | 13 | 30 | 13 | 

When we have a table like this, we can compare each pair with all other pairs. Take the pair of (18,21). For every pair in the table, we created a row with (18,21). So, when SQL search each row whether X1 = Y2 and X2 = Y1, it will compare a pair with all other pairs in the table. If we understand that, the rest will be easy.

We will start our solution by CROSS JOINING the FUNCTIONS TABLE with itself.

```sql
    SELECT *
    FROM FUNCTIONS AS F1
    CROSS JOIN FUNCTIONS AS F2
```

Next, we will define our rules. So far, we talked about X1 = Y2 and X2 = Y1. Actually, there is one more rule. In the questions, it says:

>List the rows such that X1 ≤ Y1.

Let's add the rules:

```sql
SELECT F1.X, F1.Y
FROM FUNCTIONS AS F1
CROSS JOIN FUNCTIONS AS F2
WHERE F1.X <= F1.Y
AND F1.X = F2.Y
AND F2.X = F1.Y
```

Is that all? Well, You might think so, but when you run this query, it will fail. The query actually works for pairs where X1 is not equal to Y1. The problem is with the pairs where X1 = Y1.
For example, let's say we are looking if we have a symmetric pair for (4,4). Since we CROSS JOINED FUNCTIONS with itself, SQL will think that (4,4) is symmetric with itself, which is true. But for (4,4) to be symmetric, there should be two (4,4) in the table. So, we should count the number of (X,Y) pairs for X1=Y1, and flag them as symmetric only if the count of (X,Y) pairs is more than 2.
For pairs where X1 != Y1, having one pair is fine. To count the number of pairs, I'll first GROUP BY for X and then for Y, and use a HAVING clause for the counts.

```sql
SELECT F1.X, F1.Y
FROM FUNCTIONS AS F1
CROSS JOIN FUNCTIONS AS F2
WHERE F1.X <= F1.Y
AND F1.X = F2.Y
AND F2.X = F1.Y
GROUP BY F1.X, F1.Y
HAVING COUNT(CASE WHEN F1.X = F1.Y THEN F1.X END) > 2
OR COUNT(CASE WHEN F1.X < F1.Y THEN F1.X END) = 1
```

Are we done? Almost! As always, we have a sorting rule.

> ... in ascending order by the value of X

```sql
SELECT F1.X, F1.Y
FROM FUNCTIONS AS F1
CROSS JOIN FUNCTIONS AS F2
WHERE F1.X <= F1.Y
AND F1.X = F2.Y
AND F2.X = F1.Y
GROUP BY F1.X, F1.Y
HAVING COUNT(CASE WHEN F1.X = F1.Y THEN F1.X END) > 2
OR COUNT(CASE WHEN F1.X < F1.Y THEN F1.X END) = 1
ORDER BY F1.X;
```

If you have hard time understanding this solution, please don't worry. It was hard for me, as well. It took two days for me to solve this problem. Because, I didn't know how CROSS JOIN worked. Look it from the other side. By solving this questions, we learned how CROSS JOIN works. Moreover, we learned how to compare value in a table with all other values in the same or another table. The important thing is that there are so many things that we don't know, but there is one less now after solving this problem =)

See you in the next question!