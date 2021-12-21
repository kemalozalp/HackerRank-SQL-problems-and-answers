# Weather Observation Station 20

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-20/problem?isFullScreen=true)

A median is defined as a number separating the higher half of a data set from the lower half. Query the median of the Northern Latitudes (LAT_N) from STATION and round your answer to  decimal places.

**Input Format**

The STATION table is described as follows:

| Column | Type |
| --- | --- |
| ID | NUMBER |
| CITY | VARCHAR2(21) |
| STATE | VARCHAR2(2) |
| LAT_N | NUMBER |
| LONG_W | NUMBER |

where LAT_N is the northern latitude and LONG_W is the western longitude.

## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT ROUND(AVG(sub.LAT_N),4)
FROM
     (SELECT
     LAT_N,
     ROW_NUMBER() OVER (ORDER BY LAT_N) AS rn
    FROM STATION) AS sub
WHERE 
    (CASE MOD((SELECT COUNT(LAT_N) FROM STATION),2)
     WHEN 1 THEN sub.rn = ((SELECT COUNT(LAT_N) FROM STATION) + 1)/2
     WHEN 0 THEN sub.rn  IN 
     ((SELECT COUNT(LAT_N) FROM STATION)/2,((SELECT COUNT(LAT_N) FROM STATION)+2)/2)
    END)
```

If you want to see how to approach to the problem, continue reading. 

***

### Explanation

Did I solve the problem? Yes! Did I solve it without any help? Yes! Did I like my solution? No! Because it is hard to understand, but it is mine. But because it is messy, I'll give you an alternative solution which I found on the internet. I believe that solution is cleaner. Let's go!

As always, let's look at what the question wants us to do:

>Query the median of the Northern Latitudes (LAT_N) from STATION and round your answer to  decimal places.

In Python, MATLAB, or FORTRAN, there are built-in functions for finding MEDIAN of a column, but in SQL there is no such function. So, we have to do it on our own. First, we need to understand what median is. Let's say we have 3 values: 4,2,5. To find the median of this list, we should first sort it in ascending order. It becomes 2,4,5. Then, we get the middle value in the sorted list. We have 3 values in the list, no? So, the middle value is the second one. To be more specific the middle value is such a value that the number of entries before it should be equal to the number of entries after it. Let's check if this is true for the sorted list. If my middle value is 4, there is one entry before it which is 2 and one entry after it which is 5. Cool! But what if we had a list with 4 entries, such as 4,2,5,1.

Regardless of the number of entries, we start by sorting values in ascending order: 1,2,4,5. It is obvious that there is no middle value here. If we pick 2 as our middle value, the number of entries before it is 1 and the number of entries after it are 4,5. If we picked 4, then entries before it are 1,2 and after it is 5. The solution in case when we have even number of entries in our list is to:

1. Divide the sorted list into two equal subsets
2. Find the average of the last value from the first subset and first value from the second subset.

Let's do this: Our two subsets are 1,2 and 4,5. The last value of the first subset is 2. The first value of the second subset is 4. 2+4 = 6. 6 / 2 is 3. Voila! Our median is 3.

Before I move on to writing the code, I'll summarize what I did to find the median of a list:

1. Sort the list
2. If the list has odd number of entries, median is the middle value
3. If the list has even number of entries, median is the average of two values.

This is enough for us to find the median, but computer cannot understand #2 and 3. We should write it in the language of mathematics.

How can we tell the computer of the list has odd or even number of entries? The way to do this in math is to find the modulo of a value. For example, mod(5,2) is 1. What this means is 5 divided by 2 is equal to 1. Since we are looking for the entry that divides the list into two halves, we can use MOD(x,2). If it is 1, median is the mid value. Otherwise, median is the average of the two values. 

Great, but how computer will find the mid value? Here is an example. For a sorted list of [2,4,5], computer can find the mid value by calculating the total number of entries in the list, adding 1 to it, and dividing it by 2. So, (3 + 1) / 2 = 2. The median is 4.

Here is another example if the list has even number of entries, as in [1,2,4,5]. The list has 4 entries. 4/2 = 2. Get the 2nd entry which is 2. 4/2 + 1 = 3. Get the third entry which is 4. (2 + 4) / 2 = 3. The median is 3!

We layed out the math that we require to tell computer for calculating median. Now, we have one more question to answer. How will the computer get the third value from the list? Is there a column indicating the place of entries in the list? It is not given to us, but we can add it to the list and this is how I'll start the code.

```sql
SELECT
LAT_N,
ROW_NUMBER() OVER (ORDER BY LAT_N) AS rn
ROM STATION
```

I used the window function ROW_NUMBER to add indices to the list. Since we only need Northern Latitiudes, I only queried LAT_N and rn which is the column for row_numbers/indices. I'll use this query as a subquery for getting the median.

I'll continue with implementing the math to find the median which we layed out above. I'll use WHERE clause to filter the list for the mid value/s. I warn you: the messy part is coming. Therefore, I'll give the answer out step by step.

Since we have two cases, list with an odd and even number of entries, we have to use CASE WHEN THEN structure here. If odd, this. If even, that.

```sql
CASE MOD((SELECT COUNT(LAT_N) FROM STATION),2)
WHEN 1 THEN sub.rn = ((SELECT COUNT(LAT_N) FROM STATION) + 1)/2
WHEN 0 THEN sub.rn  IN 
((SELECT COUNT(LAT_N) FROM STATION)/2,((SELECT COUNT(LAT_N) FROM STATION)+2)/2)
END
```
One reason that the code looks messy is that I had to write subqueries. The one in the MOD, for example, is to find the number of total entries in the list. The one WHEN MOD 2 is 1, is to set the row number/ index to the middle value. The last one WHEN MOD 2 is 0 is to get the two row numbers of the two values in the middle of the list.

In case we have odd number of entries, the WHERE clause will bring only 1 value which is the median. However, in case there are even number of entries, the WHERE clause will bring 2 values from the middle of the list. In this case, the median is the average of the two values. Getting the average of 1 value will generate the same input value. So, I can find the average regardless of how many values the WHERE clause brings.

```sql
SELECT AVG(LAT_N)
```

Finally, we are asked to round the MEDIAN to 4 decimal places. For that,

```sql
SELECT ROUND(AVG(LAT_N,),4)
```

Now, it is time to bring all the pieces together. When I do that, I'll give an alias to the subquery. So, things will look slightly different.

```sql
SELECT ROUND(AVG(sub.LAT_N),4)
FROM
     (SELECT
     LAT_N,
     ROW_NUMBER() OVER (ORDER BY LAT_N) AS rn
    FROM STATION) AS sub
WHERE 
    (CASE MOD((SELECT COUNT(LAT_N) FROM STATION),2)
     WHEN 1 THEN sub.rn = ((SELECT COUNT(LAT_N) FROM STATION) + 1)/2
     WHEN 0 THEN sub.rn  IN 
     ((SELECT COUNT(LAT_N) FROM STATION)/2,((SELECT COUNT(LAT_N) FROM STATION)+2)/2)
    END)
```

There it is! This is my baby even though it is ugly =)

Now, let's see the alternative solution. I borrowed the idea from the post below:

https://www.geeksforgeeks.org/calculate-median-in-mysql/

```sql
SET @rowindex := -1;
 
SELECT
   ROUND(AVG(sub.LAT_N),4) 
FROM
   (SELECT @rowindex:=@rowindex + 1 AS rowindex,
           STATION.LAT_N
    FROM STATION
    ORDER BY STATION.LAT_N) AS sub
WHERE
sub.rowindex IN (FLOOR(@rowindex / 2), CEIL(@rowindex / 2));
```

This code looks much cleanier. The reasons is we only need one subquery. This is because we set a value and increase it by one for indexing rows instead of using the ROW_NUMBER window function. Although our subquery 'sub' is more efficient than this one above, using a variable to index columns becomes handy in the WHERE clause. Since the variable @rowindex holds the last index value in memory, it automatically gives us the number of entries in the list. So, you don't have to write subqueries. Finally, The code above don't use a CASE WHEN THEN structure to find the mid value.

Well, my friend, maybe you are thinking that why I didn't give you the elegant solution only. Because, my solution is more valuable to me since I struggled to come up with it. 

See you in the next question!