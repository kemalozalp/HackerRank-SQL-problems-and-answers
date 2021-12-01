# Ollivander's Inventory

## Question:

[Go to the question](https://www.hackerrank.com/challenges/harry-potter-and-wands/problem?isFullScreen=true)

This question is way too long to type in MARKDOWN format. So, please use the above link to see the question.

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT WW.ID, SUB.AGE, SUB.COINS, SUB.POWER
FROM WANDS AS WW
INNER JOIN WANDS_PROPERTY AS WWP
ON WW.CODE = WWP.CODE
RIGHT JOIN
    (SELECT MIN(W.COINS_NEEDED) AS COINS, W.POWER, WP.AGE
    FROM WANDS AS W
    INNER JOIN WANDS_PROPERTY AS WP
    ON W.CODE = WP.CODE
    WHERE WP.IS_EVIL = 0
    GROUP BY W.POWER, WP.AGE) AS SUB
ON WW.COINS_NEEDED = SUB.COINS AND WW.POWER = SUB.POWER AND WWP.AGE = SUB.AGE
ORDER BY SUB.POWER DESC, SUB.AGE DESC;
```

If you want to see how to approach to the problem, continue reading. 

I'll start with restating what we are asked:

>Hermione decides the best way to choose is by determining the minimum number of gold galleons needed to buy each non-evil wand of high power and age.

I'll just make a list of keywords:

- minimum number of gold galleons; it just means the "cheapest"
- non-evil; probably 1 for evil and 0 for non-evil wands.
- high power
- high age

Now, let's see in which tables we can find these attributes.

- price -> Wands
- evil / non-evil -> Wands_Property
- power -> Wands
- age -> Wands

So, we have to use both tables to get the output we need. Before, we move onto the solution, let's read the question carefully. If you're practicing, you can run a few different versions of the code, but you cannot run the code until you're sure during an interview. So, we have to make sure that we understood everything correctly. Look at below:

>Wands_Property: The code is the code of the wand, age is the age of the wand, and is_evil denotes whether the wand is good for the dark arts. If the value of is_evil is 0, it means that the wand is not evil.

So, as I guessed, if the wand is evil, it has value 1. If not, 0. This is pretty straightforward. However, the next sentence is not. 

>The mapping between code and age is one-one, meaning that if there are two pairs, (code1,age1)  and (code2,age2), then code1 != code2 and and age1!= age2.

Hmm. The mapping... If I was in an interview, I would ask for a clarification to the interviewer, but I can't now. So, my understanding is that the code corresponding to an age is a unique value. What this means is that we can use either code or age to match wands in different tables. Cool, but what is the plan? Where we are going to start? What's the game plan?

At this point, get a pen and paper and write down bunch of different ideas. Try to visualize tables. 

1. So, I'm thinking that I can start by putting together the Wands and Wands_Property tables together using the CODE column in both tables. This way, I have all attibutes in one table. 

2. Let's recap the question:

>Hermione decides the best way to choose is by determining the minimum number of gold galleons needed to buy each non-evil wand of high power and age.

So, we want the "minimum price" for each non-evil, high power and high age wand.
In the sample input table, I see wands with the same power values. So, I can GROUP BY power. Moreover, wands with the same code has the same age value. So, I can also GROUP BY age. This is convenient because, then, I can find the minimum price for a wand for each POWER and AGE group. I guess I'll start writing a query that prints minimum price for wands grouped by power and age. It would be great to get the ID of the wand, but I need to use an aggreagate function on ID since I am using the GROUP BY clause in the query. So, for now, let's don't think about the ID. Here is the query:

```sql
SELECT MIN(W.COINS_NEEDED) AS COINS, W.POWER, WP.AGE
FROM WANDS AS W
INNER JOIN WANDS_PROPERTY AS WP
ON W.CODE = WP.CODE
GROUP BY W.POWER, WP.AGE
```
Great! This works! Now, how can I get the ID of the wand? Oh, before the ID, let's remember that we need NON_EVIL wands. We can just add a WHERE clause to the above query to filter NON_EVIL wands:

```sql
SELECT MIN(W.COINS_NEEDED) AS COINS, W.POWER, WP.AGE
FROM WANDS AS W
INNER JOIN WANDS_PROPERTY AS WP
ON W.CODE = WP.CODE
WHERE WP.IS_EVIL = 0
GROUP BY W.POWER, WP.AGE
```

Good! Now, let's think how we can add ID to our list. Hmmm...

So, ID attribute is in the WANDS table. Technically, I can use the above query as a subquery and join its results to the WANDS table. BUT! What columns in my subquery can I use to perfectly match values in both tables? There are two common columns: coins_needed and power. I can use these two columns, but there can be wands with at the same power and same price. So, I cannot be sure if this process can match values correctly. However, if I had AGE in the WANDS or CODE in my subquery, I could then use coins_needed, power,and code/age to match unique values. Remember that we grouped values by POWER and AGE. There is only one PRICE (coins_needed) for a pair of POWER and AGE. 

I hate when this happens, but there is no easy way. Practically, we can first JOIN WANDS and WANDS_PROPERTY so that we have AGE, POWER, CODE, and PRICE in one table. Then, we can take the values in the subquery and JOIN it to the combination of WANDS and WANDS_PROPERTY, but only the matching values. Because remember, we already got rid of evil wands and picked cheapest wands for each power and age group. Okay, it is tedious, but let's do it!

```sql
SELECT WW.ID, SUB.AGE, SUB.COINS, SUB.POWER
FROM WANDS AS WW
INNER JOIN WANDS_PROPERTY AS WWP
ON WW.CODE = WWP.CODE
RIGHT JOIN
    (SELECT MIN(W.COINS_NEEDED) AS COINS, W.POWER, WP.AGE
    FROM WANDS AS W
    INNER JOIN WANDS_PROPERTY AS WP
    ON W.CODE = WP.CODE
    WHERE WP.IS_EVIL = 0
    GROUP BY W.POWER, WP.AGE) AS SUB
ON WW.COINS_NEEDED = SUB.COINS AND WW.POWER = SUB.POWER AND WWP.AGE = SUB.AGE
```

One thing to notice is that I used RIGHT JOIN to join the combination of WANDS and WANDS_PROPERTY to the SUBQUERY. Why? Because RIGHT JOIN will only get tables that matches with the SUBQUERY. In general RIGHT JOIN gets values from the second table. On the other hand, LEFT JOIN gets values from the first table. LEFT and RIGHT means FIRST and SECOND in the order you are JOINING tables.

Messy, but does the job. Are we done? I don't know. Let's look at the question.

>...sorted in order of descending power. If more than one wand has same power, sort the result in order of descending age.

It seems like we have to do a bit of sorting before we go. No biggie! Here it is: 

```sql
SELECT WW.ID, SUB.AGE, SUB.COINS, SUB.POWER
FROM WANDS AS WW
INNER JOIN WANDS_PROPERTY AS WWP
ON WW.CODE = WWP.CODE
RIGHT JOIN
    (SELECT MIN(W.COINS_NEEDED) AS COINS, W.POWER, WP.AGE
    FROM WANDS AS W
    INNER JOIN WANDS_PROPERTY AS WP
    ON W.CODE = WP.CODE
    WHERE WP.IS_EVIL = 0
    GROUP BY W.POWER, WP.AGE) AS SUB
ON WW.COINS_NEEDED = SUB.COINS AND WW.POWER = SUB.POWER AND WWP.AGE = SUB.AGE
ORDER BY SUB.POWER DESC, SUB.AGE DESC;
```

Here is a confession! I sometimes trick myself thinking that there should be a more elegant solution. Why do I do that? I guess I always have to be the smartest person in the room. Fuck me! Just do it! Don't worry, I did =) No-one is perfect. We all learn, internalize, and change.

See you in the next question!