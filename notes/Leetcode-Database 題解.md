<!-- GFM-TOC -->
* [595. Big Countries](#595-big-countries)
* [627. Swap Salary](#627-swap-salary)
* [620. Not Boring Movies](#620-not-boring-movies)
* [596. Classes More Than 5 Students](#596-classes-more-than-5-students)
* [182. Duplicate Emails](#182-duplicate-emails)
* [196. Delete Duplicate Emails](#196-delete-duplicate-emails)
* [175. Combine Two Tables](#175-combine-two-tables)
* [181. Employees Earning More Than Their Managers](#181-employees-earning-more-than-their-managers)
* [183. Customers Who Never Order](#183-customers-who-never-order)
* [184. Department Highest Salary](#184-department-highest-salary)
* [176. Second Highest Salary](#176-second-highest-salary)
* [177. Nth Highest Salary](#177-nth-highest-salary)
* [178. Rank Scores](#178-rank-scores)
* [180. Consecutive Numbers](#180-consecutive-numbers)
* [626. Exchange Seats](#626-exchange-seats)
<!-- GFM-TOC -->


# 595. Big Countries

https://leetcode.com/problems/big-countries/description/

## Description

```html
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
```

查找面積超過 3,000,000 或者人口數超過 25,000,000 的國家。

```html
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```

## SQL Schema

SQL Schema 用於在本地環境下創建表結構並導入數據，從而方便在本地環境解答。

```sql
DROP TABLE
IF
    EXISTS World;
CREATE TABLE World ( NAME VARCHAR ( 255 ), continent VARCHAR ( 255 ), area INT, population INT, gdp INT );
INSERT INTO World ( NAME, continent, area, population, gdp )
VALUES
    ( 'Afghanistan', 'Asia', '652230', '25500100', '203430000' ),
    ( 'Albania', 'Europe', '28748', '2831741', '129600000' ),
    ( 'Algeria', 'Africa', '2381741', '37100000', '1886810000' ),
    ( 'Andorra', 'Europe', '468', '78115', '37120000' ),
    ( 'Angola', 'Africa', '1246700', '20609294', '1009900000' );
```

## Solution

```sql
SELECT name,
    population,
    area
FROM
    World
WHERE
    area > 3000000
    OR population > 25000000;
```

# 627. Swap Salary

https://leetcode.com/problems/swap-salary/description/

## Description

```html
| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
```

只用一個 SQL 查詢，將 sex 字段反轉。

```html
| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS salary;
CREATE TABLE salary ( id INT, NAME VARCHAR ( 100 ), sex CHAR ( 1 ), salary INT );
INSERT INTO salary ( id, NAME, sex, salary )
VALUES
    ( '1', 'A', 'm', '2500' ),
    ( '2', 'B', 'f', '1500' ),
    ( '3', 'C', 'm', '5500' ),
    ( '4', 'D', 'f', '500' );
```

## Solution

使用異或操作，兩個相等的數異或的結果為 0，而 0 與任何一個數異或的結果為這個數。

```
'f' ^ 'm' ^ 'f' = 'm'
'm' ^ 'm' ^ 'f' = 'f'
```



```sql
UPDATE salary
SET sex = CHAR ( ASCII(sex) ^ ASCII( 'm' ) ^ ASCII( 'f' ) );
```

# 620. Not Boring Movies

https://leetcode.com/problems/not-boring-movies/description/

## Description


```html
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
```

查找 id 為奇數，並且 description 不是 boring 的電影，按 rating 降序。

```html
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS cinema;
CREATE TABLE cinema ( id INT, movie VARCHAR ( 255 ), description VARCHAR ( 255 ), rating FLOAT ( 2, 1 ) );
INSERT INTO cinema ( id, movie, description, rating )
VALUES
    ( 1, 'War', 'great 3D', 8.9 ),
    ( 2, 'Science', 'fiction', 8.5 ),
    ( 3, 'irish', 'boring', 6.2 ),
    ( 4, 'Ice song', 'Fantacy', 8.6 ),
    ( 5, 'House card', 'Interesting', 9.1 );
```

## Solution

```sql
SELECT
    *
FROM
    cinema
WHERE
    id % 2 = 1
    AND description != 'boring'
ORDER BY
    rating DESC;
```

# 596. Classes More Than 5 Students

https://leetcode.com/problems/classes-more-than-5-students/description/

## Description

```html
+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
+---------+------------+
```

查找有五名及以上 student 的 class。

```html
+---------+
| class   |
+---------+
| Math    |
+---------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS courses;
CREATE TABLE courses ( student VARCHAR ( 255 ), class VARCHAR ( 255 ) );
INSERT INTO courses ( student, class )
VALUES
    ( 'A', 'Math' ),
    ( 'B', 'English' ),
    ( 'C', 'Math' ),
    ( 'D', 'Biology' ),
    ( 'E', 'Math' ),
    ( 'F', 'Computer' ),
    ( 'G', 'Math' ),
    ( 'H', 'Math' ),
    ( 'I', 'Math' );
```

## Solution

對 class 列進行分組之後，再使用 count 彙總函數統計數量，統計之後使用 having 進行過濾。

```sql
SELECT
    class
FROM
    courses
GROUP BY
    class
HAVING
    count( DISTINCT student ) >= 5;
```

# 182. Duplicate Emails

https://leetcode.com/problems/duplicate-emails/description/

## Description

郵件地址表：

```html
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

查找重複的郵件地址：

```html
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS Person;
CREATE TABLE Person ( Id INT, Email VARCHAR ( 255 ) );
INSERT INTO Person ( Id, Email )
VALUES
    ( 1, 'a@b.com' ),
    ( 2, 'c@d.com' ),
    ( 3, 'a@b.com' );
```

## Solution

對 Email 進行分組，如果相同 Email 的數量大於等於 2，則表示該 Email 重複。

```sql
SELECT
    Email
FROM
    Person
GROUP BY
    Email
HAVING
    COUNT( * ) >= 2;
```

# 196. Delete Duplicate Emails

https://leetcode.com/problems/delete-duplicate-emails/description/

## Description

郵件地址表：

```html
+----+---------+
| Id | Email   |
+----+---------+
| 1  | john@example.com |
| 2  | bob@example.com |
| 3  | john@example.com |
+----+---------+
```

刪除重複的郵件地址：

```html
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

## SQL Schema

與 182 相同。

## Solution

只保留相同 Email 中 Id 最小的那一個，然後刪除其它的。

連接：

```sql
DELETE p1
FROM
    Person p1,
    Person p2
WHERE
    p1.Email = p2.Email
    AND p1.Id > p2.Id
```

子查詢：

```sql
DELETE
FROM
    Person
WHERE
    id NOT IN ( SELECT id FROM ( SELECT min( id ) AS id FROM Person GROUP BY email ) AS m );
```

應該注意的是上述解法額外嵌套了一個 SELECT 語句，如果不這麼做，會出現錯誤：You can't specify target table 'Person' for update in FROM clause。以下演示了這種錯誤解法。

```sql
DELETE
FROM
    Person
WHERE
    id NOT IN ( SELECT min( id ) AS id FROM Person GROUP BY email );
```

參考：[pMySQL Error 1093 - Can't specify target table for update in FROM clause](https://stackoverflow.com/questions/45494/mysql-error-1093-cant-specify-target-table-for-update-in-from-clause)

# 175. Combine Two Tables

https://leetcode.com/problems/combine-two-tables/description/

## Description

Person 表：

```html
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId is the primary key column for this table.
```

Address 表：

```html
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId is the primary key column for this table.
```

查找 FirstName, LastName, City, State 數據，而不管一個用戶有沒有填地址信息。

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS Person;
CREATE TABLE Person ( PersonId INT, FirstName VARCHAR ( 255 ), LastName VARCHAR ( 255 ) );
DROP TABLE
IF
    EXISTS Address;
CREATE TABLE Address ( AddressId INT, PersonId INT, City VARCHAR ( 255 ), State VARCHAR ( 255 ) );
INSERT INTO Person ( PersonId, LastName, FirstName )
VALUES
    ( 1, 'Wang', 'Allen' );
INSERT INTO Address ( AddressId, PersonId, City, State )
VALUES
    ( 1, 2, 'New York City', 'New York' );
```

## Solution

涉及到 Person 和 Address 兩個表，在對這兩個表執行連接操作時，因為要保留 Person 表中的信息，即使在 Address 表中沒有關聯的信息也要保留。此時可以用左外連接，將 Person 表放在 LEFT JOIN 的左邊。

```sql
SELECT
    FirstName,
    LastName,
    City,
    State
FROM
    Person P
    LEFT JOIN Address A
    ON P.PersonId = A.PersonId;
```

# 181. Employees Earning More Than Their Managers

https://leetcode.com/problems/employees-earning-more-than-their-managers/description/

## Description

Employee 表：

```html
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```

查找薪資大於其經理薪資的員工信息。

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS Employee;
CREATE TABLE Employee ( Id INT, NAME VARCHAR ( 255 ), Salary INT, ManagerId INT );
INSERT INTO Employee ( Id, NAME, Salary, ManagerId )
VALUES
    ( 1, 'Joe', 70000, 3 ),
    ( 2, 'Henry', 80000, 4 ),
    ( 3, 'Sam', 60000, NULL ),
    ( 4, 'Max', 90000, NULL );
```

## Solution

```sql
SELECT
    E1.NAME AS Employee
FROM
    Employee E1
    INNER JOIN Employee E2
    ON E1.ManagerId = E2.Id
    AND E1.Salary > E2.Salary;
```

# 183. Customers Who Never Order

https://leetcode.com/problems/customers-who-never-order/description/

## Description

Customers 表：

```html
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

Orders 表：

```html
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

查找沒有訂單的顧客信息：

```html
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS Customers;
CREATE TABLE Customers ( Id INT, NAME VARCHAR ( 255 ) );
DROP TABLE
IF
    EXISTS Orders;
CREATE TABLE Orders ( Id INT, CustomerId INT );
INSERT INTO Customers ( Id, NAME )
VALUES
    ( 1, 'Joe' ),
    ( 2, 'Henry' ),
    ( 3, 'Sam' ),
    ( 4, 'Max' );
INSERT INTO Orders ( Id, CustomerId )
VALUES
    ( 1, 3 ),
    ( 2, 1 );
```

## Solution

左外鏈接

```sql
SELECT
    C.Name AS Customers
FROM
    Customers C
    LEFT JOIN Orders O
    ON C.Id = O.CustomerId
WHERE
    O.CustomerId IS NULL;
```

子查詢

```sql
SELECT
    Name AS Customers
FROM
    Customers
WHERE
    Id NOT IN ( SELECT CustomerId FROM Orders );
```

# 184. Department Highest Salary

https://leetcode.com/problems/department-highest-salary/description/

## Description

Employee 表：

```html
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

Department 表：

```html
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

查找一個 Department 中收入最高者的信息：

```html
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

## SQL Schema

```sql
DROP TABLE IF EXISTS Employee;
CREATE TABLE Employee ( Id INT, NAME VARCHAR ( 255 ), Salary INT, DepartmentId INT );
DROP TABLE IF EXISTS Department;
CREATE TABLE Department ( Id INT, NAME VARCHAR ( 255 ) );
INSERT INTO Employee ( Id, NAME, Salary, DepartmentId )
VALUES
    ( 1, 'Joe', 70000, 1 ),
    ( 2, 'Henry', 80000, 2 ),
    ( 3, 'Sam', 60000, 2 ),
    ( 4, 'Max', 90000, 1 );
INSERT INTO Department ( Id, NAME )
VALUES
    ( 1, 'IT' ),
    ( 2, 'Sales' );
```

## Solution

創建一個臨時表，包含了部門員工的最大薪資。可以對部門進行分組，然後使用 MAX() 彙總函數取得最大薪資。

之後使用連接找到一個部門中薪資等於臨時表中最大薪資的員工。

```sql
SELECT
    D.NAME Department,
    E.NAME Employee,
    E.Salary
FROM
    Employee E,
    Department D,
    ( SELECT DepartmentId, MAX( Salary ) Salary FROM Employee GROUP BY DepartmentId ) M
WHERE
    E.DepartmentId = D.Id
    AND E.DepartmentId = M.DepartmentId
    AND E.Salary = M.Salary;
```

# 176. Second Highest Salary

https://leetcode.com/problems/second-highest-salary/description/

## Description

```html
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

查找工資第二高的員工。

```html
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

沒有找到返回 null 而不是不返回數據。

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS Employee;
CREATE TABLE Employee ( Id INT, Salary INT );
INSERT INTO Employee ( Id, Salary )
VALUES
    ( 1, 100 ),
    ( 2, 200 ),
    ( 3, 300 );
```

## Solution

為了在沒有查找到數據時返回 null，需要在查詢結果外面再套一層 SELECT。

```sql
SELECT
    ( SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC LIMIT 1, 1 ) SecondHighestSalary;
```

# 177. Nth Highest Salary

## Description

查找工資第 N 高的員工。

## SQL Schema

同 176。

## Solution

```sql
CREATE FUNCTION getNthHighestSalary ( N INT ) RETURNS INT BEGIN

SET N = N - 1;
RETURN ( SELECT ( SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC LIMIT N, 1 ) );

END
```

# 178. Rank Scores

https://leetcode.com/problems/rank-scores/description/

## Description

得分表：

```html
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

將得分排序，並統計排名。

```html
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS Scores;
CREATE TABLE Scores ( Id INT, Score DECIMAL ( 3, 2 ) );
INSERT INTO Scores ( Id, Score )
VALUES
    ( 1, 3.5 ),
    ( 2, 3.65 ),
    ( 3, 4.0 ),
    ( 4, 3.85 ),
    ( 5, 4.0 ),
    ( 6, 3.65 );
```

## Solution

要統計某個 score 的排名，只要統計大於該 score 的 score 數量，然後加 1。

| score | 大於該 score 的 score 數量 | 排名 |
| :---: | :---: | :---: |
| 4.1 | 2 | 3 |
| 4.2 | 1 | 2 |
| 4.3 | 0 | 1 |

但是在本題中，相同的 score 只算一個排名：

| score | 排名 |
| :---: | :---: |
| 4.1 | 3 |
| 4.1 | 3 |
| 4.2 | 2 |
| 4.2 | 2 |
| 4.3 | 1 |
| 4.3 | 1 |

可以按 score 進行分組，將同一個分組中的 score 只當成一個。

但是如果分組字段只有 score 的話，那麼相同的 score 最後的結果只會有一個，例如上面的 6 個記錄最後只取出 3 個。

| score | 排名 |
| :---: | :---: |
| 4.1 | 3 |
| 4.2 | 2 |
| 4.3 | 1 |

所以在分組中需要加入 Id，每個記錄顯示一個結果。綜上，需要使用 score 和 id 兩個分組字段。

在下面的實現中，首先將 Scores 表根據 score 字段進行自連接，得到一個新表，然後在新表上對 id 和 score 進行分組。

```sql
SELECT
    S1.score 'Score',
    COUNT( DISTINCT S2.score ) 'Rank'
FROM
    Scores S1
    INNER JOIN Scores S2
    ON S1.score <= S2.score
GROUP BY
    S1.id, S1.score
ORDER BY
    S1.score DESC;
```

# 180. Consecutive Numbers

https://leetcode.com/problems/consecutive-numbers/description/

## Description

數字表：

```html
+----+-----+
| Id | Num |
+----+-----+
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |
+----+-----+
```

查找連續出現三次的數字。

```html
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS LOGS;
CREATE TABLE LOGS ( Id INT, Num INT );
INSERT INTO LOGS ( Id, Num )
VALUES
    ( 1, 1 ),
    ( 2, 1 ),
    ( 3, 1 ),
    ( 4, 2 ),
    ( 5, 1 ),
    ( 6, 2 ),
    ( 7, 2 );
```

## Solution

```sql
SELECT
    DISTINCT L1.num ConsecutiveNums
FROM
    Logs L1,
    Logs L2,
    Logs L3
WHERE L1.id = l2.id - 1
    AND L2.id = L3.id - 1
    AND L1.num = L2.num
    AND l2.num = l3.num;
```

# 626. Exchange Seats

https://leetcode.com/problems/exchange-seats/description/

## Description

seat 表存儲著座位對應的學生。

```html
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
```

要求交換相鄰座位的兩個學生，如果最後一個座位是奇數，那麼不交換這個座位上的學生。

```html
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
```

## SQL Schema

```sql
DROP TABLE
IF
    EXISTS seat;
CREATE TABLE seat ( id INT, student VARCHAR ( 255 ) );
INSERT INTO seat ( id, student )
VALUES
    ( '1', 'Abbot' ),
    ( '2', 'Doris' ),
    ( '3', 'Emerson' ),
    ( '4', 'Green' ),
    ( '5', 'Jeames' );
```

## Solution

使用多個 union。

```sql
# 處理偶數 id，讓 id 減 1
# 例如 2,4,6,... 變成 1,3,5,...
SELECT
    s1.id - 1 AS id,
    s1.student
FROM
    seat s1
WHERE
    s1.id MOD 2 = 0 UNION
# 處理奇數 id，讓 id 加 1。但是如果最大的 id 為奇數，則不做處理
# 例如 1,3,5,... 變成 2,4,6,...
SELECT
    s2.id + 1 AS id,
    s2.student
FROM
    seat s2
WHERE
    s2.id MOD 2 = 1
    AND s2.id != ( SELECT max( s3.id ) FROM seat s3 ) UNION
# 如果最大的 id 為奇數，單獨取出這個數
SELECT
    s4.id AS id,
    s4.student
FROM
    seat s4
WHERE
    s4.id MOD 2 = 1
    AND s4.id = ( SELECT max( s5.id ) FROM seat s5 )
ORDER BY
    id;
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
