# Weather Observation Station 11

## Question:

[Go to the question](https://www.hackerrank.com/challenges/weather-observation-station-11/problem?isFullScreen=true)

Query the list of CITY names from STATION that either do not start with vowels or do not end with vowels. Your result cannot contain duplicates.

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

If you solve Weather-Observation-Station questions from 6-10, it will be easier for you to understand this solution. Because questions 6-10 are easier versions of this one. You can write two different codes to answer this question. Depending on the method you choose, this question tests whether you know:

1. how to filter in a table using LIKE clause
2. how to use WILDCARD characters when filtering names based on prescribed rules
3. how to use REGEXP to filter in a table
4. how to filter unique values in a table using DISTINCT

The easy solution is to query using WHERE ... NOT LIKE clause with several AND clauses connected with an OR clause. Sounds fuzzy, no?! What do I mean?

When you see a question like this, I recommend you to chop up into bite size pieces. Let's start this condition: **either do not start with vowels or do not end with vowels**

1. Get rid of the noise first. Suppose that I substitute "do not start with vowels" with X and "do not end with vowels" with Y. The expression becomes much easier to understand: **X OR Y". So, you'll have conditions X and Y and you'll connect them with an OR clause. 

2. So, what is X? X holds place for CITY names that do not start with vowels. So, names cannot start with A,E,I,O,U. These 5 letters are forbidden, ALL AT THE SAME TIME. Not one OR the other but ALL. So, you cannot logically say 'A' OR 'E' OR 'I'... It should be 'A' AND 'E' AND 'I' ...

3. Y is similar to X, but this time, CITY names that do not END with vowels.

Here is the first method:

```sql
SELECT DISTINCT CITY 
FROM STATION 
WHERE (CITY NOT LIKE 'A%' AND CITY NOT LIKE 'E%' AND CITY NOT LIKE 'I%' AND CITY NOT LIKE 'O%' AND CITY NOT LIKE 'U%') 
OR (CITY NOT LIKE '%A' AND CITY NOT LIKE '%E' AND CITY NOT LIKE '%I' AND CITY NOT LIKE '%O' AND CITY NOT LIKE '%U');
```

As you see, we explicitly defined condition for names that do not start and end with vowels. Then, we connected these two with an OR clause. A% means starting with A. %A means ending with A. Easy peasy, lemon squeezy.

Let's talk a little bit about the PERCENT (%) character. % holds place for all other characters coming after a specified character. To search for names ending with 'A', we do '%A'. Let's pose another situation. We are searching for names that are 3 characters long and ending with 'A'. In this case, you should search for '__A'. The UNDERSCORE character (_) holds place of only ONE character. Compared to UNDERSCORE, % holds place for undefined number of characters. So, it gives you more freedom when searching. Good!

I have to say that WHERE ... LIKE ... OR combo leads to a very messy code. Because we have too many conditions here. Instead, I prefer REGEXP in such situations. REGEXP is for MySQL. Just be aware of that. REGEXP leads to a cleaner code, but you have to use another WILDCARD character. Here is the code:

```sql
SELECT DISTINCT CITY
FROM STATION
WHERE CITY REGEXP '^[^AEIOU].*|.*[^AEIOU]$';
```
This is much cleaner, no? The benefit of REGEXP is that you can put all vowels into a list. This is exactly what we did here. Careful readers realized that we used ^ and $ instead of %. ^ (circumflex) denotes that the characters in the list are the FIRST character of a name. On the other hand, $ indicates that the characters in the list are the LAST character of a name. More careful readers also saw the ".*" combo. To explain what this combo means, let's explain what each character means. "." denotes ONE character. When "." is combined with "*" as in ".*", this expression means undefined number of characters. For example, if you excluded "*" from the expression, you would select all names that are 3 characters long; starting and ending with a vowel. "*" expands the search to all names that start and end with vowels. It removes the character limit in the length of a name. Finally, we used ^ in the list. This makes the expression negative. Without the ^ IN LISTS, the same expression would mean "City Names STARTING or ENDING with vowels". With the ^ IN LISTS, it changes to "City Names that DO NOT START or END with vowels". Oh, I almost forgot to mention the PIPE (|) character. It means OR. To better understand it, let's break down the whole expression into pieces:

- '^[^AEIOU].*|.*[^AEIOU]$'
- '^[^AEIOU].* = DO NOT START WITH A,E,I,O,U
- | = OR
- .*[^AEIOU]$' = DO NOT END WITH A,E,I,O,U

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
- When ^ used IN THE LIST, it makes the expression negative.
- PIPE (|) character means OR.
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

- Finally, do not make the mistake I made with the |. If you leave space before and after pipe, your code will NOT work properly.

```sql
WHERE CITY REGEXP '^[^AEIOU].* | .*[^AEIOU]$' -- BAD
WHERE CITY REGEXP '^[^AEIOU].*|.*[^AEIOU]$'  -- GOOD
```

