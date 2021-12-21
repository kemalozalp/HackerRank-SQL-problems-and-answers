# SQL Project Planning

## Question:

[Go to the question](https://www.hackerrank.com/challenges/sql-projects/problem?isFullScreen=true)

You are given a table, Projects, containing three columns: Task_ID, Start_Date and End_Date. It is guaranteed that the difference between the End_Date and the Start_Date is equal to 1 day for each row in the table.

| Column | Type |
| --- | --- |
| Task_ID | Integer |
| Start_Date | Date |
| End_Date | Date |

If the End_Date of the tasks are consecutive, then they are part of the same project. Samantha is interested in finding the total number of different projects completed.

Write a query to output the start and end dates of projects listed by the number of days it took to complete the project in ascending order. If there is more than one project that have the same number of completion days, then order by the start date of the project.

** Sample Input ** 

| Task_ID | Start_Date | End_Date |
| --- | --- | --- |
| 1 | 2015-10-01 | 2015-10-02 |
| 2 | 2015-10-02 | 2015-10-03 |
| 3 | 2015-10-03 | 2015-10-04 |
| 4 | 2015-10-13 | 2015-10-14 |
| 5 | 2015-10-14 | 2015-10-15 |
| 6 | 2015-10-28 | 2015-10-29 |
| 7 | 2015-10-30 | 2015-10-31 |

**Sample Output**

```sql
2015-10-28 2015-10-29
2015-10-30 2015-10-31
2015-10-13 2015-10-15
2015-10-01 2015-10-04
```

**Explanation**

The example describes following four projects:

* Project 1: Tasks 1, 2 and 3 are completed on consecutive days, so these are part of the project. Thus start date of project is 2015-10-01 and end date is 2015-10-04, so it took 3 days to complete the project.
* Project 2: Tasks 4 and 5 are completed on consecutive days, so these are part of the project. Thus, the start date of project is 2015-10-13 and end date is 2015-10-15, so it took 2 days to complete the project.
* Project 3: Only task 6 is part of the project. Thus, the start date of project is 2015-10-28 and end date is 2015-10-29, so it took 1 day to complete the project.
* Project 4: Only task 7 is part of the project. Thus, the start date of project is 2015-10-30 and end date is 2015-10-31, so it took 1 day to complete the project.

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT MIN(Start_Date), MAX(End_Date)
FROM
    (SELECT
    Start_Date,
    End_Date,
    DATEDIFF(End_Date,'2015-01-01') AS diff,
    ROW_NUMBER() OVER (ORDER BY End_Date) AS rn,
    (DATEDIFF(End_Date,'2015-01-01') - ROW_NUMBER() OVER (ORDER BY End_Date)) AS id
    FROM PROJECTS) AS sub
GROUP BY id
ORDER BY COUNT(Start_Date), MIN(Start_Date)
```

If you want to see how to approach to the problem, continue reading. 

***

### Explanation

I'll be honest. This question was a real head scratcher. The A-HA moment for me was a post I found on the StackExchange. It wasn't the solution for this question, but it helped a lot. However, before finding it, I did my best to solve this problem without any help. I tried for a couple of hours before turning to internet for help. I could try for longer, but I trying learn as much about SQL as I can in the minimum amount of time. Because I am trying to finish my Ph.D. So, it wasn't feasible for me to spend longer to solve a problem that I was stuck.

Before giving away the answer, I'll briefly talk about how I approached the problem although it didn't help. I highly encourage you to be patient and read along because there is a lesson to be learned in the end.

When I looked at the sample input table, it was pretty obvious which tasks were a part of the same project. If the latter End_Dates follow the previos one, those two are the part of the same project. When I say "follow", I mean that the difference between two consecutive End_Dates is 1. I repeat: the difference between two consecutive End_Dates is 1. So, I decided to write an algorithm that could help computer to compare consecutive End_Dates and group tasks with a 1-day difference  between End_Dates. To do that, I had to subtract consecutive End_Dates.

I guess we all learned by now that if you want SQL to do something for you, present the necessary information to it in the same row. So, to subtract consecutive end dates, I had to put the second end date in the same row with the first one, and third end date in the same row with the second one, and so on. Well, I did that by using a JOIN on Task_IDs. What I exactly did was something like below:

```sql
SELECT *
FROM Projects as P1
INNER JOIN Projects as P2
ON P1.Task_ID = P2.Task_ID + 1
```

I didn't try if the code above worked, but I wanted to give you an idea about how I did that before. Pay attention to the line which starts with 'ON'. Since the Task_ID is an integer, we can perform basic math operations on the Task_IDs.

Okay! With the above code, I can calculate the difference between dates. If the difference is more than 1, then, the tasks in the line with the difference > 1 are not the part of the same project. Great! Great. Hmm, wait! The difference between end_dates help you find the tasks belonging the same project but it is not enough. After doing that I'll have a table like below:

| Task_ID | Start_Date | End_Date | End_Date | Diff |
| --- | --- | --- | --- | --- |
| 1 | 2015-10-01 | 2015-10-02 | 2015-10-03 | 1 |
| 2 | 2015-10-02 | 2015-10-03 | 2015-10-04 | 1 |
| 3 | 2015-10-03 | 2015-10-04 | 2015-10-14 | 10 |
| 4 | 2015-10-13 | 2015-10-14 | 2015-10-14 | 1 |
| 5 | 2015-10-14 | 2015-10-15 | 2015-10-29 | 14 |
| 6 | 2015-10-28 | 2015-10-29 | 2015-10-31 | 2 |
| 7 | 2015-10-30 | 2015-10-31 |

I'm not sure if the last line of the table is correct, but what I am trying show you here is that things are getting more complicated. Why? First of all, I can find the difference between consecutive End_Dates, but I need something else to correctly group Task_IDs. If I group by difference, then, I'll end up with one big group. And what about the other groups? With this approach, I can randomly group different projects together. Not a good approach. So, I am obviously missing something. I discarded the idea of subtracting end_dates of consecutive tasks. However, it is still true that SQL needs the information in the same row to operate. This is for sure. The other thing that is for sure is that I had to subtract values given in consecutive rows. I tried a few more things before I gave up and turned to internet for some help and I found the below post on StackExchange:

https://dba.stackexchange.com/questions/297319/get-a-count-of-consecutive-dates

I heard bells are ringing, choirs singing HAAAAAALELUJAH! HAAAAALELUJAH! HALELUJAH! HALELUJAH! At the same time, I pictured Archimedes running in the streets of Sicilia naked screamin EUREKA!!!
So, I borrowed the idea that helped me solve this questions from the post above. 

If you are patient enough to read until here, here is the lesson to learn: Coding problems, like many other problems, require you to recognize a pattern. If you don't know the pattern, you have to learn it first and learning it might take a lot of time. This is why I turned to internet for help after a couple of hours of self-study.

The main idea that I borrowed from the above post is this:

- To get a count of consecutive values given in rows, you have to use the same reference value. Using this reference value, you calculate the difference between the reference value and each value. Call this value as DIFF. Assuming values are pre-sorted, you assign row_numbers to values. Finally, you take the difference between DIFF and the row_numbers. Call this as ID. In the end, you end up with consecutive values having the same ID. Voila! If you don't see it now, don't worry. It will be clear in a minute once we start to run a few lines of code.

I, first, wrote a subquery to find DIFF, Row_numbers, and IDs. I put it below. Run this and look what it does.

```sql
SELECT
Start_Date,
End_Date,
DATEDIFF(End_Date,'2015-01-01') AS diff,
ROW_NUMBER() OVER (ORDER BY End_Date) AS rn,
(DATEDIFF(End_Date,'2015-01-01') - ROW_NUMBER() OVER (ORDER BY End_Date)) AS id
FROM PROJECTS
```
How did I pick '2015-01-01'? Well, I queried the MIN(End_Date) in the given table. Do you have to do it? No! In the blog post above, they used '1970-01-01' as a reference date. They picked an early enough date so the differences are positive. Should they be positive? I don't know, but it sounds like if they are you'll have one less thing to think about. So, all we did was for simplicity.

If you run the code above, you'll see that this code generates IDs which are the same for tasks that are the part of the same project! YYYYYYYYYYES! The rest is a piece of cake, my friends!

But, remember:

> Write a query to output the start and end dates of projects

If you look at the sample answer, we should basically output the start and end dates of each project. To do that, I grouped by IDs and selected the MIN(Start_Date) to get the earliest start date and MAX(End_Date) for the group. 

> ... listed by the number of days it took to complete the project in ascending order. If there is more than one project that have the same number of completion days, then order by the start date of the project.

Some sorting as always. See the full code below:

```sql
SELECT MIN(Start_Date), MAX(End_Date)
FROM
    (SELECT
    Start_Date,
    End_Date,
    DATEDIFF(End_Date,'2015-01-01') AS diff,
    ROW_NUMBER() OVER (ORDER BY End_Date) AS rn,
    (DATEDIFF(End_Date,'2015-01-01') - ROW_NUMBER() OVER (ORDER BY End_Date)) AS id
    FROM PROJECTS) AS sub
GROUP BY id
ORDER BY COUNT(Start_Date), MIN(Start_Date)
```
Is that all? YES!

Believe me I feel SOOOOOOOoooo good after solving this problem. Did I figure out all on my own? Hell NO! But I tried my best to solve it in a limited time. I failed to do it on my own, but I had the correct approach to solve it and searched the internet for it. I learned the pattern to find the consecutive values given in rows.

One thing to note is that, although this question is listed under the "Advanced Join" category, my solution doesn't require a JOIN, but it works!

See you in the next question!