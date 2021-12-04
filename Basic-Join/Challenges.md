# Challenges

## Question:

[Go to the question](https://www.hackerrank.com/challenges/challenges/problem?isFullScreen=true)

This question is way too long to type in MARKDOWN format. So, please use the above link to see the question.

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT H.HACKER_ID, H.NAME, COUNT(C1.CHALLENGE_ID) AS TC
FROM HACKERS AS H
INNER JOIN CHALLENGES AS C1
ON H.HACKER_ID = C1.HACKER_ID
GROUP BY H.HACKER_ID, H.NAME
HAVING TC = (SELECT COUNT(C2.CHALLENGE_ID) AS TC2
            FROM CHALLENGES AS C2
            GROUP BY C2.HACKER_ID
            ORDER BY TC2 DESC LIMIT 1)
OR TC IN (SELECT S.TC3
              FROM (SELECT COUNT(C3.CHALLENGE_ID) AS TC3
                    FROM CHALLENGES AS C3
                    GROUP BY C3.HACKER_ID) AS S
              GROUP BY S.TC3
              HAVING COUNT(S.TC3) = 1)
ORDER BY TC DESC, H.HACKER_ID;
```

If you want to see how to approach to the problem, continue reading. 

I'll start with restating what we are asked:

>Write a query to print the hacker_id, name, and the total number of challenges created by each student.

So, where will this information come from? Let's look at the tables.

- HACKERS table -> hacker_id, name attributes
- CHALLENGES table -> challenge_id, hacker_id

It seems like we can get the hacker_id and name attributes directly from tables, but we have to calculate total number of challenges. I think I can do that by grouping the CHALLENGES table BY hacker_id and COUNT challenges. Well, easy peasy, no?! Honestly, it sounds too easy. So, let's read the rest of the question:

>Sort your results by the total number of challenges in descending order.

No problem. Just ORDER BY number of challenges in DESC. What else is wanted?

>If more than one student created the same number of challenges, then sort the result by hacker_id.

Another sorting rule. We will just add one more rule to the ORDER BY clause. What else?

>If more than one student created the same number of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.

Hmm. There it is! I was waiting for that. How can we do that? Let's say listed hacker_id, name, and total number of challenges (tc) by each hacker. Something like this:

| hacker_id | name | tc |
| --- | --- | --- |
| 123 | Oz | 12 |
| 231 | MK | 43 |
| 53 | Kyle | 9 |
| 12 | Lua | 9 |
| 512 | John | 94|

Okay, this gives us something to talk about and brainstorm. The question simply want us to remove Kyle and Lua from the table since they have equal number of challenges created and the number of challenges created is less than the maximim number of challenges created which is 94 according to the table above. So, we can define some rules based on this observation:

1. If a hacker created challenges that is equal to the maximum number of challenges, list it even if there are other hackers with the same number of challenges created. Something like this:

HAVING COUNT(challenge_id) = MAX(COUNT(challenge_id))

2. If a hacker created less than the maximum number of challenges and there is another hacker with the same number of challenges created, then exclude these hackers from the list. We can filter this by GROUPING BY total number of challenges created and COUNTING the values in each group. Values equal to 1 means that there is only ONE hacker with that many challenges created. Okay, enough brain storming. Let's write some code. I'll start by just putting the two tables together because we, then, have all the attributes that we need to get to the answer.

```sql
SELECT H.HACKER_ID, H.NAME, COUNT(C1.CHALLENGE_ID) AS TC
FROM HACKERS AS H
INNER JOIN CHALLENGES AS C1
ON H.HACKER_ID = C1.HACKER_ID
GROUP BY H.HACKER_ID, H.NAME
```
This code will list hacker_id, name, and the number of challenges created by each hacker. Good! Now, we should implement the rules we defined and filter by them. We have already put a GROUP BY clause in our code. Therefore, we cannot use WHERE anymore. HAVING statement then!

```sql
SELECT H.HACKER_ID, H.NAME, COUNT(C1.CHALLENGE_ID) AS TC
FROM HACKERS AS H
INNER JOIN CHALLENGES AS C1
ON H.HACKER_ID = C1.HACKER_ID
GROUP BY H.HACKER_ID, H.NAME
HAVING TC = (SELECT COUNT(C2.CHALLENGE_ID) AS TC2
            FROM CHALLENGES AS C2
            GROUP BY C2.HACKER_ID
            ORDER BY TC2 DESC LIMIT 1)
```

Well, this was honestly harder than I expected. The tricky part is to calculate the maximum number of challenges created in a HAVING statement. To do that, I wrote a subquery. The subquery is actually a simpler version of the main query without the join. I threw in a LIMIT clasue after an ORDER BY to get the MAX number of challenges ONLY. Good! Now, the last rule: eliminate hackers with the same number of challenges who also has less than the max number of challenges created.

First of all, let's think about how the two rules will interact. I mean if rule 1 is true, should rule 2 also be true? OR is it suffucient for at least one rule to be true? If you run the code above, you'll see that the output only has the hacker's information who created max number of challenges. So, we actually need other hacker's information who created less than the max number of challenges. Hmmmm... Then, I need an OR clause to connect the two rules. It is decided! Next?

Let's remember how we defined the second rule:

>We can filter this by GROUPING BY total number of challenges created and COUNTING the values in each group. Values equal to 1 means that there is only ONE hacker with that many challenges created. Okay, enough brain storming. Let's write some code. I'll start by just putting the two tables together because we, then, have all the attributes that we need to get to the answer.

Let's do this first:

>GROUPING BY total number of challenges created.

```sql
SELECT COUNT(C3.CHALLENGE_ID) AS TC3
FROM CHALLENGES AS C3
GROUP BY C3.HACKER_ID
```
Cool. Now, the challenge is to group challenge counts and count how many entries are there in each group. Man, subqueries are confusing. They kind of make me feel like I am watching INCEPTION, but we need them. What I'll do is I'll use the above query as a subquery; group it by the number of challenges and only SELECT the singular values.

```sql
SELECT S.TC3
FROM (SELECT COUNT(C3.CHALLENGE_ID) AS TC3
      FROM CHALLENGES AS C3
      GROUP BY C3.HACKER_ID) AS S
GROUP BY S.TC3
HAVING COUNT(S.TC3) = 1
```

... and this is our second rule! Now, let's put all the small pieces together:

```sql
SELECT H.HACKER_ID, H.NAME, COUNT(C1.CHALLENGE_ID) AS TC
FROM HACKERS AS H
INNER JOIN CHALLENGES AS C1
ON H.HACKER_ID = C1.HACKER_ID
GROUP BY H.HACKER_ID, H.NAME
HAVING TC = (SELECT COUNT(C2.CHALLENGE_ID) AS TC2
            FROM CHALLENGES AS C2
            GROUP BY C2.HACKER_ID
            ORDER BY TC2 DESC LIMIT 1)
OR TC IN (SELECT S.TC3
              FROM (SELECT COUNT(C3.CHALLENGE_ID) AS TC3
                    FROM CHALLENGES AS C3
                    GROUP BY C3.HACKER_ID) AS S
              GROUP BY S.TC3
              HAVING COUNT(S.TC3) = 1)
```

Are we done? ALMOST! As always, we have to do some sorting:

>Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by hacker_id. 

We can do: ORDER BY TC DESC, H.HACKER_ID

... and we are here! The final version of the code is:

```sql
SELECT H.HACKER_ID, H.NAME, COUNT(C1.CHALLENGE_ID) AS TC
FROM HACKERS AS H
INNER JOIN CHALLENGES AS C1
ON H.HACKER_ID = C1.HACKER_ID
GROUP BY H.HACKER_ID, H.NAME
HAVING TC = (SELECT COUNT(C2.CHALLENGE_ID) AS TC2
            FROM CHALLENGES AS C2
            GROUP BY C2.HACKER_ID
            ORDER BY TC2 DESC LIMIT 1)
OR TC IN (SELECT S.TC3
              FROM (SELECT COUNT(C3.CHALLENGE_ID) AS TC3
                    FROM CHALLENGES AS C3
                    GROUP BY C3.HACKER_ID) AS S
              GROUP BY S.TC3
              HAVING COUNT(S.TC3) = 1)
ORDER BY TC DESC, H.HACKER_ID;
```

I have to admit that this was a harder question than it looked. It took me quite some time to figure out how to write the rules in the HAVING statements. I guess subqueries are an indispensable part of our lives now. Get used to it, guys!

See you in the next question!