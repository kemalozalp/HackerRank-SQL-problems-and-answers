# Weather Observation Station 5

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-5/problem?isFullScreen=true)

Query the two cities in STATION with the shortest and longest CITY names, as well as their respective lengths (i.e.: number of characters in the name). If there is more than one smallest or largest city, choose the one that comes first when ordered alphabetically.
The STATION table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| CITY | VARCHAR2(21) |
| STATE | VARCHAR2(2) |
| LAT_N| NUMBER |
| LONG_W | NUMBER |

where LAT_N is the northern latitude and LONG_W is the western longitude.

For example, CITY has four entries: DEF, ABC, PQRS and WXY.

**Sample Output**

```
ABC 3
PQRS 4
```

**Explanation**

When ordered alphabetically, the CITY names are listed as ABC, DEF, PQRS, and WXY, with lengths 3,3,4 and 3. The longest name is PQRS, but there are 3 options for shortest named city. Choose ABC, because it comes first alphabetically.

**Note**

You can write two separate queries to get the desired output. It need not be a single query.

---

## ANSWER

This question tests you whether you know:

- how to MIN and MAX for a given value. There are two ways to do this in MySQL. One is to sort outcomes so that the MAX or MIN value will be at the top of the list. Then, you limit the outcomes with ONE hence only the MIN or MAX value is selected. Alternative way to do this is to use the MIN() and MAX() functions in combination with other functions. Here, we can use them with LENGTH() function.
- writing two queries at a time.

The common solution is to query and SORT outcomes to select MIN and MAX values. I'll start with that. Below, I wrote two separate queries. The first one is to select the CITY with the LONGEST NAME. The second query is very similar, but by sorting the list in an ASCENDING ORDER, I place the CITY with the SHORTEST NAME at the top. In both cases, I used the LIMIT clause to ONLY select the values at the top of the list. Note that I used two different columns with the ORDER BY clause. ORDER BY orders the selection by the first rule which is by LENGTH of the string. Then, it orders the list alphabetically as stated in the question. When ORDER BY clause is given two rules, it applies the second rule while obeying the first rule:

```sql
SELECT CITY, LENGTH(CITY)
FROM STATION
ORDER BY LENGTH(CITY) DESC, CITY LIMIT 1;

SELECT CITY, LENGTH(CITY)
FROM STATION
ORDER BY LENGTH(CITY) ASC, CITY LIMIT 1;
```
We can write two separate queries because we are allowed to do it. If we are not allowed to write two queries, we can write one query consisting of two parts and join the parts using the UNION clause. Here is the alternative method:

```sql
(SELECT CITY, LENGTH(CITY)
FROM STATION
ORDER BY LENGTH(CITY) DESC, CITY LIMIT 1)
UNION
(SELECT CITY, LENGTH(CITY)
FROM STATION
ORDER BY LENGTH(CITY) ASC, CITY LIMIT 1);
```
Last but not least! We can use MIN and MAX functions. If you do scientific computing like me, using MIN() and MAX() functions makes more sense. However, this way requires writing subqueries. As you see below, I wrote a subquery to select the LONGEST and SHORTEST CITY names in the WHERE clause. Moreover, I still have to use ORDER BY clause to sort multiple outputs alphabetically. This method is not very popular because you have to write a few extra line of code. With that said, choose the one that you're most comfortable with.

```sql
(SELECT CITY, LENGTH(CITY)
FROM STATION
WHERE LENGTH(CITY) = (SELECT MAX(LENGTH(CITY)) FROM STATION)
ORDER BY CITY LIMIT 1)
UNION
(SELECT CITY, LENGTH(CITY)
FROM STATION
WHERE LENGTH(CITY) = (SELECT MIN(LENGTH(CITY)) FROM STATION)
ORDER BY CITY LIMIT 1);
```

Before I conclude, I want to tell you that none of these codes are actually right. Because some city names consist of multiple word. In this case, LENGTH() counts SPACE as a character. The correct way to do this is to manipulate strings before we count characters in them. But this is for another question.

---

## Fundamentals to learn:

- LENGTH() is used to calculate how many characters a string has. Remember that SPACE is a character and is counted by the LENGTH() function.
- ORDER BY is a clause that is used to sort a list in an ASCENDING OR DESCENDING order.
- ORDER BY accepts more than one rule to sort a list. When given multiple rules, latter rules obey the previous rules in the given order.
- LIMIT clause limits the output by given number.
- You can write two separate queries to answer ONE question.
- When you are not allowed to write two queries (e.g. in a coding interview), use UNION to merge results of two separate queies.
- When using UNION, do NOT use semicolon (;) at the end of the first query. Because, remember, UNION makes two separate queries ONE. So, you need only one semicolon.
- From the formatting perspective, putting subqueries in paratheses is a good idea but it is your choice.
- You can use MIN() and MAX() function to select the LONGEST and SHORTEST strings. However, you have to write a few extra lines of code with that method. Although, this method is easier to think for some people, like me =).