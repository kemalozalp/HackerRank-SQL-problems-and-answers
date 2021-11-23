# Weather Observation Station 8

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-8/problem?isFullScreen=true)

Query the list of CITY names from STATION which have vowels (i.e., a, e, i, o, and u) as both their first and last characters. Your result cannot contain duplicates.

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

The easy solution is to query using WHERE ... LIKE clause with several OR clauses. What do I mean? I guess it makes more sense when you see the code itself.

```sql
SELECT DISTINCT CITY 
FROM STATION 
WHERE (CITY LIKE 'A%' OR CITY LIKE 'E%' OR CITY LIKE 'I%' OR CITY LIKE 'O%' OR CITY LIKE 'U%') 
AND (CITY LIKE '%A' OR CITY LIKE '%E' OR CITY LIKE '%I' OR CITY LIKE '%O' OR CITY LIKE '%U');
```
As you see, we explicitly defined a condition for each vowel in the list. Then, we grouped conditions for vowels being the FIRST and LAST characters and connected these two with an AND clause. It is very important to realize how we used the % character to indicate a vowel is the first or last character.

Let's talk a little bit about the PERCENT (%) character. % holds place for all other characters coming after a specified character. To search for names ending with 'A', we do '%A'. Let's pose another situation. We are searching for names that are 3 characters long and ending with 'A'. In this case, you should search for '__A'. The UNDERSCORE character (_) holds place of only ONE character. Compared to UNDERSCORE, % holds place for undefined number of characters. So, it gives you more freedom when searching. Good!

I have to say that WHERE ... LIKE ... OR combo leads to a very messy code. Because we have too many conditions here. Instead, I prefer REGEXP in such situations. REGEXP is for MySQL. Just be aware of that. REGEXP leads to a cleaner code, but you have to use another WILDCARD character. Here is the code:

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE CITY REGEXP '^[AEIOU].*[AEIOU]$';
```
This is much cleaner, no? The benefit of REGEXP is that you can put all vowels into a list. This is exactly what we did here. Careful readers realized that we used ^ and $ instead of %. ^ (circumflex) denotes that the characters in the list are the FIRST character of a name. On the other hand, $ indicates that the characters in the list are the LAST character of a name. More careful readers also saw the ".*" combo. To explain what this combo means, let's explain what each character means. "." denotes ONE character. When "." is combined with "*" as in ".*", this expression means undefined number of characters. For example, if you excluded "*" from the expression, you would select all names that are 3 characters long; starting and ending with a vowel. "*" expands the search to all names that start and end with vowels. It removes the character limit in the length of a name.

To conclude, you can use LIKE or REGEXP clauses to search for special characters in a name. If there are one or two OR conditions, LIKE is as good as REGEXP. However, REGEXP is the clear winner when there are multiple OR conditions.

---

## Fundamentals to learn:

- LIKE is used to search a table with matching conditions.
- LIKE comes after the WHERE clause.
- You can use OR to define multiple conditions together. In this case, you'll select values matching with ONE or the OTHER condition.
- % is a placeholder character. It can hold place for ZERO characters or 1231232 characters.
- It is important where you put the % sign. If it comes after a letter, it means that there are more characters after the letter. Conversely, if it comes before a letter, it means that there are more characters BEFORE the letter. So, use it wisely. 
- REGEXP accepts a list of characters. Therefore, you don't have to define conditions connected with OR. It leads to a much cleaner code when there are multiple OR conditions.
- ^ denotes that characters in the list are the fist character of a string.
- $ denotes that characters in the list are the last character of a string.
- .* combo is a place holder for undefined number of characters.
- When defining multiple conditions, you have to define what column should meet the condition after every OR cluase. For example, 

```sql
...
WHERE CITY LIKE '%A'
OR CITY LIKE '%E'
OR CITY LIKE '%O';
```

If you do:

```sql
...
WHERE CITY LIKE '%A'
OR LIKE '%E'
OR LIKE '%O';
```

Your code won't work. I just wanted to tell you this because I made this mistake before.