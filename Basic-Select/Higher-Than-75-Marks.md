# Higher Than 75 Marks

## Question:

[Go to the question](https://www.hackerrank.com/challenges/more-than-75-marks/problem?isFullScreen=true)

Query the Name of any student in STUDENTS who scored higher than  Marks. Order your output by the last three characters of each name. If two or more students both have names ending in the same last three characters (i.e.: Bobby, Robby, etc.), secondary sort them by ascending ID.

**Input Format**

The **STUDENTS** table is described as follows:

| Column | Type |
| --- | --- |
| ID | Integer |
| NAME | String |
| MARKS | Integer |

The Name column only contains uppercase (A-Z) and lowercase (a-z) letters.

### Sample Input

| ID | Name | Marks |
| --- | --- | --- |
| 1 | Ashley | 81 |
| 2 | Samantha | 75 |
| 4 | Julia | 76 |
| 3 | Belvet| 84 |

### Sample Ouput

```
Ashley
Julia
Belvet
```

### Explanation

Only Ashley, Julia, and Belvet have Marks >75.If you look at the last three characters of each of their names, there are no duplicates and 'ley' < 'lia' <  'vet'.

---

## ANSWER

This questions tests whether you know how to:

1. filter a table using WHERE clause
2. order values using ORDER BY clause
3. order strings by a character group other than the whole string

Except the 'order by LAST THREE characters' part, this questions is pretty basic. To order a string by a letter that is not the first one, we should use RIGHT or LEFT function together with the ORDER BY clause. Here is the code that solves this question.

```sql
SELECT NAME
FROM STUDENTS
WHERE MARKS > 75
ORDER BY RIGHT(NAME,3), ID;
```
Instead of ordering by NAME, we ordered by the last three characters of names. To do that, we used RIGHT(NAME,3). This expression tells ORDER BY to sort take the last three from the right in NAME and sort it. If you don't indicate ASC (ascending) or DESC (descending), the output will be sorted in ascending order by default.

Finally, we added a second ordering rule, by ID after the comma. The ORDER BY will execute the first rule and then the second rule obeying the first rule.

### Fundamentals to Learn

- To sort strings by a subset of characters, we use RIGHT() or LEFT() function. These functions take two arguments. First one is the column name and the second one is the number of characters. Ss, RIGHT(NAME,3) means take 3 characters from the right of each value in the NAME column. LEFT(NAME,5) means take 5 characters from the LEFT of each value in the NAME column.
- You can give ORDER BY more than one sorting rule. Remember that ORDER BY processes the first rule. Then, it processes the second rule by obeying the first rule. This means that if there are values sharing the same rank by the first rule, say two words in the 3rd place (3rd and 4th places in the list), the second rule can reorder the two words, but NOT the WHOLE LIST.
- ORDER BY clause comes after the WHERE clause.