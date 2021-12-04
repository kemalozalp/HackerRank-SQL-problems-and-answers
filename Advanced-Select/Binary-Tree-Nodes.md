# Binary Tree Nodes

## Question:

[Go to the question](https://www.hackerrank.com/challenges/binary-search-tree-1/problem?isFullScreen=true)

You are given a table, BST, containing two columns: N and P, where N represents the value of a node in Binary Tree, and P is the parent of N.

| Column | Type |
| --- | --- |
| N | Integer |
| P | Integer |

Write a query to find the node type of Binary Tree ordered by the value of the node. Output one of the following for each node:

- Root: If node is root node.
- Leaf: If node is leaf node.
- Inner: If node is neither root nor leaf node.

** Sample Input ** 

| N | P |
| --- | --- |
| 1 | 2 |
| 3 | 2 |
| 6 | 8 |
| 9 | 8 |
| 2 | 5 |
| 8 | 5 |
| 5 | null |

**Sample Output**

```sql
1 Leaf
2 Inner
3 Leaf
5 Root
6 Leaf
8 Inner
9 Leaf
```
**Explanation**

The Binary Tree below illustrates the sample:

```sql
      5
   /    \
  2      8
 / \    / \
1   3  6    
```
## ANSWER

For those who are impatient, here is the code. Although, I highly recommend you to continue reading because how you approach to the problem is more important in the long run than just getting the answer:

```sql
SELECT N,
(CASE
WHEN P IS NULL THEN 'Root'
WHEN N IN (SELECT P FROM BST) THEN 'Inner'
ELSE 'Leaf'
END) AS T
FROM BST
ORDER BY N;
```

If you want to see how to approach to the problem, continue reading. 

I'll start with restating what we are asked:

>Write a query to find the node type of Binary Tree ordered by the value of the node. 

Let's look at the sample input:

```sql
1 Leaf
2 Inner
3 Leaf
5 Root
6 Leaf
8 Inner
9 Leaf
```
I'll start from the end and write down the steps to get this output in the reverse order:

1. The last thing we will do *is to select N and Type*.
2. The output is sorted. We are asked to order by the value of the node (N). So, I would do something like: ORDER BY N
3. I guess I have to construct the column carrying the node type information now. Because there is nothing left to do. How are we going to do that?

Let's read the question carefully:

>Output one of the following for each node:

>- Root: If node is root node.
>- Leaf: If node is leaf node.
>- Inner: If node is neither root nor leaf node.

If condition, then action. This immediately signals me to use CASE WHEN THEN structure. 
Maybe, a very brief background on Binary Trees help you to understand the solution better.
Binary Tree is a structure carrying information in an hierarchial order. The value at the top is called the 'root' node. Conversely, the values at the bottom of the tree are called the 'leaf' nodes. Finally, values in between the root and leaf nodes are called 'inner' nodes. I believe this information is sufficient to better understand the conditions we are going to pose to solve this question.

If you carefully observe the sample input and the tree figure, you'll see '5' is the root node and its value is 'null' in the sample input table. P is the column listing parent nodes of node values (N). Since, '5' is root node, there is no parent of '5'. Here is our first rule:

- If P is NULL, then, node type is 'Root'.

Root node condition is defined. What's next? I recognized a pattern. Some numbers in column N shows up in column P. This is because some values in N are parent nodes of other values in N. Since leaf nodes are the last level of the tree, they cannot be parent nodes of other values. Therefore, the values showing up in both column N and P should be parent nodes. Here is the second rule I'll use:

- If a value in N shows up in column P, then, node type is 'Inner'.

I'll not bother to explicitly define a third rule. Because anything doesn't fit to the two rules above will be 'leaf' node. In computer programming, almost all conditional structures has an ELSE line where you can define a value for all values that don't fit the previous descriptions.

As I mentioned above, I'll use a CASE WHEN THEN structure in the code to define the rules. Let's write the code for the rules then.

```sql
CASE
WHEN P IS NULL THEN 'Root'
WHEN N IN (SELECT P FROM BST) THEN 'Inner'
ELSE 'Leaf'
END
```

Now, I should implement these rules in the code so that they will return node type for each node value, N. What I'll do is to put this in the SELECT line after N. This way I'll create a new column which will get its values from the CASE WHEN THEN structure for each value of N. Brilliant!

```sql
SELECT N,
(CASE
WHEN P IS NULL THEN 'Root'
WHEN N IN (SELECT P FROM BST) THEN 'Inner'
ELSE 'Leaf'
END) AS T
FROM BST
```

Are we done? Almost! As always, we need to do some sorting. The questions wants the output ordered by node values. When nothing specified about the order, it is generally an ascending order like 1, 2, 3, 4, 5, etc. Then, here is the final version of the code:

```sql
SELECT N,
(CASE
WHEN P IS NULL THEN 'Root'
WHEN N IN (SELECT P FROM BST) THEN 'Inner'
ELSE 'Leaf'
END) AS T
FROM BST
ORDER BY N;
```
Remember that we are in the 'ADVANCED SELECT' category now. Here is an example of an advanced level of selection in SQL. We should make ourselves more familiar with CASE WHEN THEN statement for sophisticated selections.

See you in the next question!