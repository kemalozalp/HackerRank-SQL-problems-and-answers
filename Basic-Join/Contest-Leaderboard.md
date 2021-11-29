# Contest Leaderboard

## Question:

[Go to the question](https://www.hackerrank.com/challenges/contest-leaderboard/problem?isFullScreen=true)

You did such a great job helping Julia with her last coding contest challenge that she wants you to work on this one, too!

The total score of a hacker is the sum of their maximum scores for all of the challenges. Write a query to print the hacker_id, name, and total score of the hackers ordered by the descending score. If more than one hacker achieved the same total score, then sort the result by ascending hacker_id. Exclude all hackers with a total score of 0 from your result.

**Input Format**

The following tables contain contest data:

- Hackers: The hacker_id is the id of the hacker, and name is the name of the hacker.

| Column | Type |
| --- | --- |
| hacker_id | integer |
| name | String |

- Submissions: The submission_id is the id of the submission, hacker_id is the id of the hacker who made the submission, challenge_id is the id of the challenge for which the submission belongs to, and score is the score of the submission. 

| Column | Type |
| --- | --- |
| submission_id | integer |
| hacker_id | String |
| challenge_id | integer |
| score | integer |

### Sample Input

**Hackers Table:** 

| hacker_id | name |
| --- | --- |
| 4071 | Rose |
| 4806 | Angela |
| 26071 | Frank |
| 49438 | Patrick |
| 74842 | Lisa |
| ... | ... |

**Submissions Table:**

| submission_id | hacker_id | challenge_id | score |
| --- | --- |
| 67194 | 74842 | 63132 | 76 |
| 64479 | 74842 | 19797 | 98 |
| 40742 | 26071 | 49593 | 20 |
| 17513 | 4806 | 49593 | 32 |
| 69846 | 80305 | 19797 | 19 |
| ... | ... | ... | ... |

### Sample Output
| hacker_id | name | total_score |
| --- | --- | --- |
| 4071 | Rose | 191 |
| 74842 | Lisa | 174 |
| 84072 | Bonnie | 100 |
| 4806 | Angela | 89 |
| 26071 | Frank | 85 |
| 80305 | Kimberly | 67 |
| 49438 | Patrick | 43 |

## ANSWER

Here is the code for those who are impatient. I explain my thought process to solve this problem super detailed. Check it out! 

```sql
SELECT sub.hacker_id, sub.name, SUM(sub.max_scores) as Total_Score
FROM (
    SELECT s.hacker_id, h.name, s.challenge_id, MAX(s.score) as max_scores
    FROM Submissions AS s
    INNER JOIN Hackers AS h
    ON s.hacker_id = h.hacker_id
    GROUP BY s.hacker_id, h.name, s.challenge_id
    ) sub
GROUP BY sub.hacker_id, sub.name
HAVING Total_Score > 0
ORDER BY Total_Score DESC, sub.hacker_id;
```

This question is listed under 'Basic Join' category on HackerRank website. So, I include it in the 'Basic Join' folder for those who are searching questions by category. However, the difficulty of this question is intermediate, mainly because you have to write a subquery to get the desired output. I'll tell you how I approach problems like this and tell you everything about my methodology.

I always start from the end. How? Let's go step by step. We are asked to query THREE columns of information: hacker_id, name, and total score of the hackers. 

| hacker_id | name | total_score |
| --- | --- | --- |

We have hacker_id and name columns given, but we have to calculate total score. What is total score?

>The total score of a hacker is the sum of their maximum scores for all of the challenges.

Hmm... It sounds like we have to get the maximum score of a hacker for each challenge. The question implies that hackers might submitted more than once for a challenge. Suppose there is one hacker. Then, we have to GROUP BY challenge and SELECT the MAXIMUM score. This way, we can get a list of CHALLENGES and MAX_SCORES. However, we have multiple hackers. No worries! We can GROUP BY two different attributes. So, we GROUP BY hacker_id, name, and challenge_id. Imagine we have a table as below:

| hacker_id | challenge_id | max_score|
| --- | --- | --- |
| 1 | 1 | 90 |
| 1 | 2 | 80 |
| 1 | 3 | 100 |
| 2 | 1 | 50 |
| 2 | 3 | 90 |
| ... | ... | ...|

Then, we can calculate the max score by Grouping by hacker_id and name, and SUMMING all MAX_SCORES. I hope this makes sense.

Let's take a break here and summarize my though process.

1. I start with the desired output. Do you have all information readily given? If not, what information do you need? In this case, we need the max score of a hacker for each challenge.
2. How can I get the max score of a hacker for each challenge? Let's simply! Suppose that there is only one hacker. Well, techically, if there were only one hacker who submitted multiple times for multiple challenges, I could GROUP BY the challenge_id and run MAX() function over the scores. Good.
3. But I have multiple hackers. No worries! I can add another grouping attribute that is unique for each hacker. For that, I can use hacker_id. Voila! 

This is where I am now. Let's remember what attributes we have to query: hacker_id, name, total_score. We are missing name. No worries! We can easily add it to the SELECT clause. 

| hacker_id | name | challenge_id | max_score|
| --- | --- | --- | --- |
| 1 | SQL | 1 | 90 |
| 1 | SQL | 2 | 80 |
| 1 | SQL | 3 | 100 |
| 2 | SQL | 1 | 50 |
| 2 | SQL | 3 | 90 |
| ... | ... | ... | ...|

Once I have the table above or the query that will generate the above table, I can write another query to list the desired output values:

1. SELECT hacker_id, name, SUM(max_score)
2. FROM sometable or subquery
3. GROUP BY hacker_id, name

This is ALMOST all I need. We are asked to do a few minor formatting.

> Write a query to print the hacker_id, name, and total score of the hackers **ordered by the descending score. If more than one hacker achieved the same total score, then sort the result by ascending hacker_id. Exclude all hackers with a total score of 0 from your result**.

So, we are asked to order the output. Now a biggie.

1. Print the hacker_id, name, and total score of the hackers **ordered by the descending score.

    - ORDER BY total_score DESC

2. If more than one hacker achieved the same total score, then sort the result by ascending hacker_id.

    - ORDER BY total_score DESC**, hacker_id**

3. Exclude all hackers with a total score of 0 from your result.

    - HAVING total_score > 0
    
We basically wrote the code in pieces. Now, let's put pieces together. Here is the code: 

```sql
SELECT sub.hacker_id, sub.name, SUM(sub.max_scores) as Total_Score
FROM (
    SELECT s.hacker_id, h.name, s.challenge_id, MAX(s.score) as max_scores
    FROM Submissions AS s
    INNER JOIN Hackers AS h
    ON s.hacker_id = h.hacker_id
    GROUP BY s.hacker_id, h.name, s.challenge_id
    ) sub
GROUP BY sub.hacker_id, sub.name
HAVING Total_Score > 0
ORDER BY Total_Score DESC, sub.hacker_id;
```
The indented lines are the subquery that I was talking about. We used an INNER JOIN to put the Hacker and Submissions tables together. Then, we GROUPED BY hacker_id, name, and challenge_id to get the MAX score of each hacker for each challenge they participated.

On the outside query, we selected the desired attributes to output: hacker_id, name, and total score. To calculate the total score, we used a GROUP BY on hacker_id and name. Careful readers realized that we used a 'sub' prefix. 'sub' is the alias for the subquery, the indented lines. Because, we are getting hacker_id and name attributes from the subquery. Finally, we excluded total scores less than 1 and ordered the output by total score first and then by hacker_id.

### Fundamentals to Learn

- Start with the output. Analyze if you have all attributes wanted given in the tables.
- If not, analyze what information you need and how you can calculate it. Most of the time, GROUP BY combined with an aggreagate function makes it for you.
- Used INNER JOIN to merge two tables. INNER JOIN picks values those that matches according to the reference columns.
- HAVING clause is used to filter the table. It is like WHERE, but WHERE is used before the GROUP BY. HAVING, on the other hand, is used AFTER the GROUP BY. Here, we used HAVING because we needed to GROUP BY first to calculate the total score before we filtered it using HAVING.
- ORDER BY is used to sort the output. You can use multiple sorting conditions.
- The default sorting is the Ascending Order. ORDER BY will sort the output in ascending order if otherwise stated. 
- Assigning an alias to the subquery (I assigned 'sub' alias to it) makes life easier. To assign an alias, either put the alias after the subquery or put AS and the alias.
