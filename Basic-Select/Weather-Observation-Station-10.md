# Weather Observation Station 10

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-10/problem?isFullScreen=true)

Query the list of CITY names from STATION that do not end with vowels. Your result cannot contain duplicates.

**Input Format**

The **STATION** table is described as follows:

| Field | Type |
| --- | --- |
| ID | NUMBER |
| CITY | VARCHAR2(21) |
| STATE | VARCHAR2(2) |
| LAT_N| NUMBER |
| LONG_W | NUMBER |

where LAT_N is the northern latitude and LONG_W is the western longitude.

---

## ANSWER

You can write two different codes to answer this question. Depending on the method you choose, this question tests whether you know:

1. how to filter in a table using LIKE clause
2. how to use WILDCARD characters when filtering names based on prescribed rules
3. how to use REGEXP to filter in a table
4. how to filter unique values in a table using DISTINCT
5. how to make a positive statement negative using NOT

The easy solution is to query using WHERE ... NOT LIKE clause with several AND clauses. What do I mean? I guess it makes more sense when you see the code itself.

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE CITY NOT LIKE '%A'
AND CITY NOT LIKE '%E'
AND CITY NOT LIKE '%I'
AND CITY NOT LIKE '%O'
AND CITY NOT LIKE '%U';
```
As you see, we explicitly defined a rule for each vowel in the list and connected these rules with AND. Why AND? Because we want names that do not start with vowels. This might sound confusing if you solved previous Weather-Observation-Station questions 6-7-and-8. In those questions, we used OR. When you have conditions connected with our, you'll select any value satisfying ONE or MORE of the conditions. However, when you connect conditions with AND, you'll ONLY select those values that satisfies ALL conditions. So, AND is like a strict teacher. You cannot break any rules. OR, on the other hand, is like a more friendly teacher who ignores some of the misbehaviors.

Let's talk a little bit about the PERCENT (%) character. % holds place for all other characters coming BEFORE a specified character. To search for names starting with 'A', we do 'A%'. Let's pose another situation. We are searching for names that are 3 characters long. In this case, you should search for 'A__'. The UNDERSCORE character (_) holds place of only ONE character. Compared to UNDERSCORE, % holds place for undefined number of characters (zero characters or more). So, it gives you more freedom when searching. Good!

As I mentioned above, there is another way to query what we are asked. To do that, we use the REGEXP clause in MySQL. In other SQL languages, there are other alternative methods. So, REGEXP is for MySQL. Just be aware of that. REGEXP leads to a cleaner code, but you have to use another WILDCARD character. Here is the code:

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE CITY REGEXP '[^AEIOU]$';
```
This is much cleaner, no? The benefit of REGEXP is that you can put all vowels into a list. This is exactly what we did here. Careful readers realized that we used ^ inside the list and $ outside the list. The $ outside the list (after the bracket) specifies the position of vowels which is to be the LAST letter. The ^ (that is in the list), on the other hand, makes the whole expression NEGATIVE. Instead of names starting with vowels, we want names that DO NOT start with vowels.

To conclude, you can use LIKE or REGEXP clauses to search for special characters in a name. If there are one or two OR conditions, LIKE is as good as REGEXP. However, REGEXP is the clear winner when there are multiple OR conditions.

---

## Fundamentals to learn:

- LIKE is used to search a table with matching conditions.
- You can use OR to define multiple conditions together. In this case, you'll select values matching with ONE or the OTHER condition.
- % is a placeholder character. It can hold place for ZERO characters or 1231232 characters.
- REGEXP accepts a list of characters. Therefore, you don't have to define conditions connected with OR. It leads to a much cleaner code when there are multiple OR conditions.
- $ outside the list  denotes that characters in the list are the LAST character of a string.
- Circumflex character (^) INSIDE the list  denotes that we don't want those characters in the list. So, it actually means NOT. 
- To make an expression with LIKE, just use NOT before the LIKE. It is like as you speak.
- LIKE comes after the WHERE clause.
- When defining multiple conditions, you have to define what column should meet the condition after every OR cluase. For example, 

```sql
...
WHERE CITY NOT LIKE '%A'
OR CITY NOT LIKE '%E'
OR CITY NOT LIKE '%O';
```

If you do:

```sql
...
WHERE CITY NOT LIKE '%A'
OR NOT LIKE '%E'
OR NOT LIKE '%O';
```

Your code won't work. I just wanted to tell you this because I made this mistake before.