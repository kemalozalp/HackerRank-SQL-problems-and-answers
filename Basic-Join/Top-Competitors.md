# Top Competitors

## Question:

[Go to the question](https://www.hackerrank.com/challenges/full-score/problem?isFullScreen=true)

This question is way too long to type in MARKDOWN format. So, please use the above link to see the question.

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

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

I'll start with restating what we are asked:

>Write a query to print the respective hacker_id and name of hackers who achieved full scores for more than one challenge.

The challenge of this question is to assess:

1. who submitted to more multiple challenges
2. who achieved full scores

There are too many tables. So, take your time to understand what information each one has.
The SUBMISSIONS has actual SCORES for each submission. It also has challenge_id and hacker_id, and submission_id. The CHALLENGES table has challenge_id and hacker_id, but more importantly, it has the DIFFICULTY_LEVEL for each challenge. The DIFFICULTY table has difficulty_level and score. However, the SCORE column actually indicates THE MAX SCORE you can get at each difficulty level. Finally, the HACKERS table has hacker_id and name. Nice summary, brain. So, what? 

Well, we need to query NAME and HACKER_ID. It seems like only HACKERS table has the NAME attribute. So, we need to join this table with something else. Next?

We can compare SCORES from the SUBMISSIONS table if they are actually THE MAX SCORES, but we can do it immediately. First, we have to know the DIFFICULTY_LEVEL of the CHALLENGE. Well, let's start with that.

```sql
SELECT S.HACKER_ID, S.CHALLENGE_ID, S.SCORE, C.DIFFICULTY_LEVEL
FROM SUBMISSIONS AS S
INNER JOIN CHALLENGES AS C
ON S.CHALLENGE_ID = C.CHALLENGE_ID;
```

This code will generate an output of challenge_id, hacker_id, score, and difficulty_level. We don't need to output all four columns for the answer, but let's visualize the process as much as we can. If we can add the MAX SCORE for the corresponding DIFFICULTY_LEVEL, we can compare submission SCORE and MAX SCORE for the challenge. Let's continue joining then. So, after modifying the above code, we get:

```sql
SELECT S.HACKER_ID, S.CHALLENGE_ID, S.SCORE, C.DIFFICULTY_LEVEL, D.SCORE
FROM SUBMISSIONS AS S
INNER JOIN CHALLENGES AS C
ON S.CHALLENGE_ID = C.CHALLENGE_ID
INNER JOIN DIFFICULTY as D
ON C.DIFFICULTY_LEVEL = D.DIFFICULTY_LEVEL;
```

Nice, no?! Good, but we are lacking NAMES. Might as well, let's add the HACKERS table to this mess!

```sql
SELECT S.HACKER_ID, H.NAME, S.CHALLENGE_ID, S.SCORE, C.DIFFICULTY_LEVEL, D.SCORE
FROM SUBMISSIONS AS S
INNER JOIN CHALLENGES AS C
ON S.CHALLENGE_ID = C.CHALLENGE_ID
INNER JOIN DIFFICULTY as D
ON C.DIFFICULTY_LEVEL = D.DIFFICULTY_LEVEL
INNER JOIN HACKERS AS H
ON S.HACKER_ID = H.HACKER_ID;
```

Now, let's tidy up this mess. Good thing is that we have all the information we need in our hands. And remember, we only outputted all those columns just to visualize the process. We only need HACKER_ID and NAME in the end. To, get the answer we are asked, we will compare SCORES with MAX SCORES. 

```sql
SELECT S.HACKER_ID, H.NAME, S.CHALLENGE_ID, S.SCORE, C.DIFFICULTY_LEVEL, D.SCORE
FROM SUBMISSIONS AS S
INNER JOIN CHALLENGES AS C
ON S.CHALLENGE_ID = C.CHALLENGE_ID
INNER JOIN DIFFICULTY as D
ON C.DIFFICULTY_LEVEL = D.DIFFICULTY_LEVEL
INNER JOIN HACKERS AS H
ON S.HACKER_ID = H.HACKER_ID
WHERE S.SCORE = D.SCORE;
```

This way, we eliminate those hackers who couldn't achieve the max score. The list looks cleaner. Now, if we GROUP BY NAME and HACKER_ID and COUNT the number of NAMES per group, we can get who achieved MAX SCORE more than ONCE. At the same time, let's get rid of all the unnecessary columns. Because, we cannot display columns that we didn't use an AGGREGATE function on them. This is because we are using GROUP BY clause.

```sql
SELECT S.HACKER_ID, H.NAME
FROM SUBMISSIONS AS S
INNER JOIN CHALLENGES AS C
ON S.CHALLENGE_ID = C.CHALLENGE_ID
INNER JOIN DIFFICULTY as D
ON C.DIFFICULTY_LEVEL = D.DIFFICULTY_LEVEL
INNER JOIN HACKERS AS H
ON S.HACKER_ID = H.HACKER_ID
WHERE S.SCORE = D.SCORE
GROUP BY H.NAME, S.HACKER_ID
HAVING COUNT(NAME) > 1;
```

I want to clarify why we counted NAMES. Actually, we could count any column that we used with GROUP BY. I just picked NAMES randomly. Finally, let's order this query and finish it.

>Order your output in descending order by the total number of challenges in which the hacker earned a full score. If more than one hacker received full scores in same number of challenges, then sort them by ascending hacker_id.

```sql
SELECT S.HACKER_ID, H.NAME
FROM SUBMISSIONS AS S
INNER JOIN CHALLENGES AS C
ON S.CHALLENGE_ID = C.CHALLENGE_ID
INNER JOIN DIFFICULTY as D
ON C.DIFFICULTY_LEVEL = D.DIFFICULTY_LEVEL
INNER JOIN HACKERS AS H
ON S.HACKER_ID = H.HACKER_ID
WHERE S.SCORE = D.SCORE
GROUP BY H.NAME, S.HACKER_ID
HAVING COUNT(NAME) > 1
ORDER BY COUNT(S.CHALLENGE_ID) DESC, H.HACKER_ID;
```

Beautiful, isn't it! From nothing to a mass, and from a mass to a very clean result.

See you in the next question!