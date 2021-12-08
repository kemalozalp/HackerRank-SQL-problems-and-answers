# Occupations

## Question:

[Go to the question](https://www.hackerrank.com/challenges/occupations/problem?isFullScreen=true)

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT MIN(DOCTOR), MIN(PROFESSOR), MIN(SINGER), MIN(ACTOR)
FROM
    (SELECT
    (IF (OCCUPATION = 'DOCTOR',NAME,NULL)) AS DOCTOR,
    (IF (OCCUPATION = 'PROFESSOR',NAME,NULL)) AS PROFESSOR,
    (IF (OCCUPATION = 'SINGER',NAME,NULL)) AS SINGER,
    (IF (OCCUPATION = 'ACTOR',NAME,NULL)) AS ACTOR,
    RN
    FROM
        (SELECT
        ROW_NUMBER() OVER (PARTITION BY OCCUPATION ORDER BY NAME) AS RN,
        NAME,
        OCCUPATION
        FROM OCCUPATIONS) SUB) SUB2
GROUP BY RN;
```

If you want to see how to approach to the problem, continue reading. 

I'll start with restating what we are asked:

> Pivot the Occupation column in OCCUPATIONS so that each Name is sorted alphabetically and displayed underneath its corresponding Occupation. 

> The output column headers should be Doctor, Professor, Singer, and Actor, respectively.

> Print NULL when there are no more names corresponding to an occupation.

Let's first talk about PIVOTING TABLES **SPECIFICALLY** IN MySQL. Unfortunately, MySQL doesn't have a PIVOT function, like SQL Server or Oracle SQL. So, pivoting in MySQL is very indirect. What helped me solving this question was to learn the logic to pivot tables. I'll explain it using the table given in this question.

We are given a sample input as below:

| Name | Occupation |
| --- | --- |
| Samantha | Doctor|
| Julia | Actor |
| Maria | Actor |
| Meera | Singer |
| Ashely | Professor |
| Ketty | Professor |
| Christeen | Professor |
| Jane | Actor |
| Jenny | Doctor |
| Priya | Singer |

We have two rows which are Name and Occupation. When we pivot this table, we want columns to be occupational categories, such as Doctor, Actor, Singer, and Professor. Therefore, columns are basically each unique occupation in the occupation column. So, we want to turn the sample table into something like this:

| Doctor | Professor | Singer | Actor|
| --- | --- | --- | --- |
| Jenny | Ashley | Meera | Jane | 
| Samantha | Christeen | Priya | Julia |

One thing is hidden in this table and I believe it is the key to this problem. Look below table:

| Some Rule for Sorting | Doctor | Professor | Singer | Actor|
| --- | --- | --- | --- | --- |
| 1 | Jenny | Ashley | Meera | Jane | 
| 2 | Samantha | Christeen | Priya | Julia |

You see the first column? It is the key column for pivoting. Why? Because, we will group the original table by that column. I know this doesn't make sense now, but just bare with me. To group by the first column, we should first add it. For this question, I decided to use row numbers as the first column. Let's add row number column first and group by it. I recommend you running the code below and see what it does.

```sql
SELECT
ROW_NUMBER() OVER (PARTITION BY OCCUPATION ORDER BY NAME) AS RN,
NAME,
OCCUPATION
FROM OCCUPATIONS
```

So, first rule is to add a column to group the table by. To add the row numbers, I used a window function called ROW_NUMBER(). The row numbers are assigned AFTER the names are grouped by occupation first. Then results are sorted by name. So, we did all the grouping and sorting while we created the row numbers.

Next, we start to build the pivot table but in a very raw form. To do that, we have to list each column from scratch. Look below to see how I did it:

```sql
SELECT
IF (OCCUPATION = 'DOCTOR',NAME,NULL)) AS DOCTOR,
IF (OCCUPATION = 'PROFESSOR',NAME,NULL)) AS PROFESSOR,
IF (OCCUPATION = 'SINGER',NAME,NULL)) AS SINGER,
IF (OCCUPATION = 'ACTOR',NAME,NULL)) AS ACTOR,
RN
FROM
    (SELECT
    ROW_NUMBER() OVER (PARTITION BY OCCUPATION ORDER BY NAME) AS RN,
    NAME,
    OCCUPATION
    FROM OCCUPATIONS) SUB
```
As you see above, I used the IF statement to build each column. Simply, if occupation from the occupation column is doctor, it will list the name in the first column. If not, it will put a null  instead. Same for the other occupations. Finally, I used the previous query as the subquery. Run the code above and see what it does. Careful readers probably recognized that I also listed the row numbers as a separate column. Why? Remember that we need to group by row number to list names under the occupation they belonged.

What we need in the final step is to use the code above as a subquery and clean up the table filled with nulls. Yes, we will group by with row number. This part is pretty important. So, I'll slow down and explain it in detail. To better understand what's going on, just run the code above and have the output ready in front of you. 

Let's say you grouped by ROW NUMBER. For ROW NUMBER = 1, our rows will be:

| Doctor | Professor | Singer | Actor| RN | 
| --- | --- | --- | --- | --- |
| null | null | null | Eve | 1 | 
| Aamina | null | null | null | 1 | 
| null | Ashley | null | null | 1 | 
| null | null | Christeen | null | 1 |

GROUP BY function has to be used with an aggreagate function so that we boil down values under each column to a single value. Say we will reduce values under Doctor column to one value only. In this case, that value should be the name, Aamina and values we have are (null, Aamina, null, null).
This is hard to figure out what function to use. So, I'll give it right away. Use MIN() or MAX() to get the name out of the NULL values. So, to get the columns of the pivot table, you need to use aggregate functions MIN() or MAX().

The code should be:

```sql
SELECT MIN(DOCTOR), MIN(PROFESSOR), MIN(SINGER), MIN(ACTOR)
FROM
    (SELECT
    (IF (OCCUPATION = 'DOCTOR',NAME,NULL)) AS DOCTOR,
    (IF (OCCUPATION = 'PROFESSOR',NAME,NULL)) AS PROFESSOR,
    (IF (OCCUPATION = 'SINGER',NAME,NULL)) AS SINGER,
    (IF (OCCUPATION = 'ACTOR',NAME,NULL)) AS ACTOR,
    RN
    FROM
        (SELECT
        ROW_NUMBER() OVER (PARTITION BY OCCUPATION ORDER BY NAME) AS RN,
        NAME,
        OCCUPATION
        FROM OCCUPATIONS) SUB) SUB2
GROUP BY RN;
```

I have to say that this code was the hardest to undertand for me so far. Because pivoting is very indirect and you have to use aggreagate functions for their secondary purposes. What helped me understading the solution is to simplify the problem and solve it over a few days. You have to let each part sink. I used the website below to understand pivoting in MySQL.

https://codingsight.com/pivot-tables-in-mysql/

I also saw another solution for this question, but that guy used counters to create row numbers which can be done easily by using window functions. Here it is if you want to check it out:

https://www.apdaga.com/2021/03/hackerrank-advanced-select-occupations-pivot-set-case-when-order-by-group-by-in-sql.html

See you in the next question!