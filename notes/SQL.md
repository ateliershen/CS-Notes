<!-- GFM-TOC -->
* [一、基礎](#一基礎)
* [二、創建表](#二創建表)
* [三、修改表](#三修改表)
* [四、插入](#四插入)
* [五、更新](#五更新)
* [六、刪除](#六刪除)
* [七、查詢](#七查詢)
* [八、排序](#八排序)
* [九、過濾](#九過濾)
* [十、通配符](#十通配符)
* [十一、計算字段](#十一計算字段)
* [十二、函數](#十二函數)
* [十三、分組](#十三分組)
* [十四、子查詢](#十四子查詢)
* [十五、連接](#十五連接)
* [十六、組合查詢](#十六組合查詢)
* [十七、視圖](#十七視圖)
* [十八、存儲過程](#十八存儲過程)
* [十九、遊標](#十九遊標)
* [二十、觸發器](#二十觸發器)
* [二十一、事務管理](#二十一事務管理)
* [二十二、字符集](#二十二字符集)
* [二十三、權限管理](#二十三權限管理)
* [參考資料](#參考資料)
<!-- GFM-TOC -->


# 一、基礎

模式定義了數據如何存儲、存儲什麼樣的數據以及數據如何分解等信息，數據庫和表都有模式。

主鍵的值不允許修改，也不允許複用（不能將已經刪除的主鍵值賦給新數據行的主鍵）。

SQL（Structured Query Language)，標準 SQL 由 ANSI 標準委員會管理，從而稱為 ANSI SQL。各個 DBMS 都有自己的實現，如 PL/SQL、Transact-SQL 等。

SQL 語句不區分大小寫，但是數據庫表名、列名和值是否區分依賴於具體的 DBMS 以及配置。

SQL 支持以下三種註釋：

```sql
# 註釋
SELECT *
FROM mytable; -- 註釋
/* 註釋1
   註釋2 */
```

數據庫創建與使用：

```sql
CREATE DATABASE test;
USE test;
```

# 二、創建表

```sql
CREATE TABLE mytable (
  # int 類型，不為空，自增
  id INT NOT NULL AUTO_INCREMENT,
  # int 類型，不可為空，默認值為 1，不為空
  col1 INT NOT NULL DEFAULT 1,
  # 變長字符串類型，最長為 45 個字符，可以為空
  col2 VARCHAR(45) NULL,
  # 日期類型，可為空
  col3 DATE NULL,
  # 設置主鍵為 id
  PRIMARY KEY (`id`));
```

# 三、修改表

添加列

```sql
ALTER TABLE mytable
ADD col CHAR(20);
```

刪除列

```sql
ALTER TABLE mytable
DROP COLUMN col;
```

刪除表

```sql
DROP TABLE mytable;
```

# 四、插入

普通插入

```sql
INSERT INTO mytable(col1, col2)
VALUES(val1, val2);
```

插入檢索出來的數據

```sql
INSERT INTO mytable1(col1, col2)
SELECT col1, col2
FROM mytable2;
```

將一個表的內容插入到一個新表

```sql
CREATE TABLE newtable AS
SELECT * FROM mytable;
```

# 五、更新

```sql
UPDATE mytable
SET col = val
WHERE id = 1;
```

# 六、刪除

```sql
DELETE FROM mytable
WHERE id = 1;
```

**TRUNCATE TABLE**  可以清空表，也就是刪除所有行。

```sql
TRUNCATE TABLE mytable;
```

使用更新和刪除操作時一定要用 WHERE 子句，不然會把整張表的數據都破壞。可以先用 SELECT 語句進行測試，防止錯誤刪除。

# 七、查詢

## DISTINCT

相同值只會出現一次。它作用於所有列，也就是說所有列的值都相同才算相同。

```sql
SELECT DISTINCT col1, col2
FROM mytable;
```

## LIMIT

限制返回的行數。可以有兩個參數，第一個參數為起始行，從 0 開始；第二個參數為返回的總行數。

返回前 5 行：

```sql
SELECT *
FROM mytable
LIMIT 5;
```

```sql
SELECT *
FROM mytable
LIMIT 0, 5;
```

返回第 3 \~ 5 行：

```sql
SELECT *
FROM mytable
LIMIT 2, 3;
```

# 八、排序

-  **ASC** ：升序（默認）
-  **DESC** ：降序

可以按多個列進行排序，並且為每個列指定不同的排序方式：

```sql
SELECT *
FROM mytable
ORDER BY col1 DESC, col2 ASC;
```

# 九、過濾

不進行過濾的數據非常大，導致通過網絡傳輸了多餘的數據，從而浪費了網絡帶寬。因此儘量使用 SQL 語句來過濾不必要的數據，而不是傳輸所有的數據到客戶端中然後由客戶端進行過濾。

```sql
SELECT *
FROM mytable
WHERE col IS NULL;
```

下表顯示了 WHERE 子句可用的操作符

|  操作符 | 說明  |
| :---: | :---: |
| = | 等於 |
| &lt; | 小於 |
| &gt; | 大於 |
| &lt;&gt; != | 不等於 |
| &lt;= !&gt; | 小於等於 |
| &gt;= !&lt; | 大於等於 |
| BETWEEN | 在兩個值之間 |
| IS NULL | 為 NULL 值 |

應該注意到，NULL 與 0、空字符串都不同。

**AND 和 OR**  用於連接多個過濾條件。優先處理 AND，當一個過濾表達式涉及到多個 AND 和 OR 時，可以使用 () 來決定優先級，使得優先級關係更清晰。

**IN**  操作符用於匹配一組值，其後也可以接一個 SELECT 子句，從而匹配子查詢得到的一組值。

**NOT**  操作符用於否定一個條件。

# 十、通配符

通配符也是用在過濾語句中，但它只能用於文本字段。

-  **%**  匹配 >=0 個任意字符；

-  **\_**  匹配 ==1 個任意字符；

-  **[ ]**  可以匹配集合內的字符，例如 [ab] 將匹配字符 a 或者 b。用脫字符 ^ 可以對其進行否定，也就是不匹配集合內的字符。

使用 Like 來進行通配符匹配。

```sql
SELECT *
FROM mytable
WHERE col LIKE '[^AB]%'; -- 不以 A 和 B 開頭的任意文本
```

不要濫用通配符，通配符位於開頭處匹配會非常慢。

# 十一、計算字段

在數據庫服務器上完成數據的轉換和格式化的工作往往比客戶端上快得多，並且轉換和格式化後的數據量更少的話可以減少網絡通信量。

計算字段通常需要使用  **AS**  來取別名，否則輸出的時候字段名為計算表達式。

```sql
SELECT col1 * col2 AS alias
FROM mytable;
```

**CONCAT()**  用於連接兩個字段。許多數據庫會使用空格把一個值填充為列寬，因此連接的結果會出現一些不必要的空格，使用 **TRIM()** 可以去除首尾空格。

```sql
SELECT CONCAT(TRIM(col1), '(', TRIM(col2), ')') AS concat_col
FROM mytable;
```

# 十二、函數

各個 DBMS 的函數都是不相同的，因此不可移植，以下主要是 MySQL 的函數。

## 彙總

|函 數 |說 明|
| :---: | :---: |
| AVG() | 返回某列的平均值 |
| COUNT() | 返回某列的行數 |
| MAX() | 返回某列的最大值 |
| MIN() | 返回某列的最小值 |
| SUM() |返回某列值之和 |

AVG() 會忽略 NULL 行。

使用 DISTINCT 可以彙總不同的值。

```sql
SELECT AVG(DISTINCT col1) AS avg_col
FROM mytable;
```

## 文本處理

| 函數  | 說明  |
| :---: | :---: |
|  LEFT() |  左邊的字符 |
| RIGHT() | 右邊的字符 |
| LOWER() | 轉換為小寫字符 |
| UPPER() | 轉換為大寫字符 |
| LTRIM() | 去除左邊的空格 |
| RTRIM() | 去除右邊的空格 |
| LENGTH() | 長度 |
| SOUNDEX() | 轉換為語音值 |

其中， **SOUNDEX()**  可以將一個字符串轉換為描述其語音表示的字母數字模式。

```sql
SELECT *
FROM mytable
WHERE SOUNDEX(col1) = SOUNDEX('apple')
```

## 日期和時間處理


- 日期格式：YYYY-MM-DD
- 時間格式：HH:<zero-width space>MM:SS

|函 數 | 說 明|
| :---: | :---: |
| ADDDATE() | 增加一個日期（天、周等）|
| ADDTIME() | 增加一個時間（時、分等）|
| CURDATE() | 返回當前日期 |
| CURTIME() | 返回當前時間 |
| DATE() |返回日期時間的日期部分|
| DATEDIFF() |計算兩個日期之差|
| DATE_ADD() |高度靈活的日期運算函數|
| DATE_FORMAT() |返回一個格式化的日期或時間串|
| DAY()| 返回一個日期的天數部分|
| DAYOFWEEK() |對於一個日期，返回對應的星期幾|
| HOUR() |返回一個時間的小時部分|
| MINUTE() |返回一個時間的分鐘部分|
| MONTH() |返回一個日期的月份部分|
| NOW() |返回當前日期和時間|
| SECOND() |返回一個時間的秒部分|
| TIME() |返回一個日期時間的時間部分|
| YEAR() |返回一個日期的年份部分|

```sql
mysql> SELECT NOW();
```

```
2018-4-14 20:25:11
```

## 數值處理

| 函數 | 說明 |
| :---: | :---: |
| SIN() | 正弦 |
| COS() | 餘弦 |
| TAN() | 正切 |
| ABS() | 絕對值 |
| SQRT() | 平方根 |
| MOD() | 餘數 |
| EXP() | 指數 |
| PI() | 圓周率 |
| RAND() | 隨機數 |

# 十三、分組

把具有相同的數據值的行放在同一組中。

可以對同一分組數據使用匯總函數進行處理，例如求分組數據的平均值等。

指定的分組字段除了能按該字段進行分組，也會自動按該字段進行排序。

```sql
SELECT col, COUNT(*) AS num
FROM mytable
GROUP BY col;
```

GROUP BY 自動按分組字段進行排序，ORDER BY 也可以按彙總字段來進行排序。

```sql
SELECT col, COUNT(*) AS num
FROM mytable
GROUP BY col
ORDER BY num;
```

WHERE 過濾行，HAVING 過濾分組，行過濾應當先於分組過濾。

```sql
SELECT col, COUNT(*) AS num
FROM mytable
WHERE col > 2
GROUP BY col
HAVING num >= 2;
```

分組規定：

- GROUP BY 子句出現在 WHERE 子句之後，ORDER BY 子句之前；
- 除了彙總字段外，SELECT 語句中的每一字段都必須在 GROUP BY 子句中給出；
- NULL 的行會單獨分為一組；
- 大多數 SQL 實現不支持 GROUP BY 列具有可變長度的數據類型。

# 十四、子查詢

子查詢中只能返回一個字段的數據。

可以將子查詢的結果作為 WHRER 語句的過濾條件：

```sql
SELECT *
FROM mytable1
WHERE col1 IN (SELECT col2
               FROM mytable2);
```

下面的語句可以檢索出客戶的訂單數量，子查詢語句會對第一個查詢檢索出的每個客戶執行一次：

```sql
SELECT cust_name, (SELECT COUNT(*)
                   FROM Orders
                   WHERE Orders.cust_id = Customers.cust_id)
                   AS orders_num
FROM Customers
ORDER BY cust_name;
```

# 十五、連接

連接用於連接多個表，使用 JOIN 關鍵字，並且條件語句使用 ON 而不是 WHERE。

連接可以替換子查詢，並且比子查詢的效率一般會更快。

可以用 AS 給列名、計算字段和表名取別名，給表名取別名是為了簡化 SQL 語句以及連接相同表。

## 內連接

內連接又稱等值連接，使用 INNER JOIN 關鍵字。

```sql
SELECT A.value, B.value
FROM tablea AS A INNER JOIN tableb AS B
ON A.key = B.key;
```

可以不明確使用 INNER JOIN，而使用普通查詢並在 WHERE 中將兩個表中要連接的列用等值方法連接起來。

```sql
SELECT A.value, B.value
FROM tablea AS A, tableb AS B
WHERE A.key = B.key;
```

## 自連接

自連接可以看成內連接的一種，只是連接的表是自身而已。

一張員工表，包含員工姓名和員工所屬部門，要找出與 Jim 處在同一部門的所有員工姓名。

子查詢版本

```sql
SELECT name
FROM employee
WHERE department = (
      SELECT department
      FROM employee
      WHERE name = "Jim");
```

自連接版本

```sql
SELECT e1.name
FROM employee AS e1 INNER JOIN employee AS e2
ON e1.department = e2.department
      AND e2.name = "Jim";
```

## 自然連接

自然連接是把同名列通過等值測試連接起來的，同名列可以有多個。

內連接和自然連接的區別：內連接提供連接的列，而自然連接自動連接所有同名列。

```sql
SELECT A.value, B.value
FROM tablea AS A NATURAL JOIN tableb AS B;
```

## 外連接

外連接保留了沒有關聯的那些行。分為左外連接，右外連接以及全外連接，左外連接就是保留左表沒有關聯的行。

檢索所有顧客的訂單信息，包括還沒有訂單信息的顧客。

```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

customers 表：

| cust_id | cust_name |
| :---: | :---: |
| 1 | a |
| 2 | b |
| 3 | c |

orders 表：

| order_id | cust_id |
| :---: | :---: |
|1    | 1 |
|2    | 1 |
|3    | 3 |
|4    | 3 |

結果：

| cust_id | cust_name | order_id |
| :---: | :---: | :---: |
| 1 | a | 1 |
| 1 | a | 2 |
| 3 | c | 3 |
| 3 | c | 4 |
| 2 | b | Null |

# 十六、組合查詢

使用  **UNION**  來組合兩個查詢，如果第一個查詢返回 M 行，第二個查詢返回 N 行，那麼組合查詢的結果一般為 M+N 行。

每個查詢必須包含相同的列、表達式和聚集函數。

默認會去除相同行，如果需要保留相同行，使用 UNION ALL。

只能包含一個 ORDER BY 子句，並且必須位於語句的最後。

```sql
SELECT col
FROM mytable
WHERE col = 1
UNION
SELECT col
FROM mytable
WHERE col =2;
```

# 十七、視圖

視圖是虛擬的表，本身不包含數據，也就不能對其進行索引操作。

對視圖的操作和對普通表的操作一樣。

視圖具有如下好處：

- 簡化複雜的 SQL 操作，比如複雜的連接；
- 只使用實際表的一部分數據；
- 通過只給用戶訪問視圖的權限，保證數據的安全性；
- 更改數據格式和表示。

```sql
CREATE VIEW myview AS
SELECT Concat(col1, col2) AS concat_col, col3*col4 AS compute_col
FROM mytable
WHERE col5 = val;
```

# 十八、存儲過程

存儲過程可以看成是對一系列 SQL 操作的批處理。

使用存儲過程的好處：

- 代碼封裝，保證了一定的安全性；
- 代碼複用；
- 由於是預先編譯，因此具有很高的性能。

命令行中創建存儲過程需要自定義分隔符，因為命令行是以 ; 為結束符，而存儲過程中也包含了分號，因此會錯誤把這部分分號當成是結束符，造成語法錯誤。

包含 in、out 和 inout 三種參數。

給變量賦值都需要用 select into 語句。

每次只能給一個變量賦值，不支持集合的操作。

```sql
delimiter //

create procedure myprocedure( out ret int )
    begin
        declare y int;
        select sum(col1)
        from mytable
        into y;
        select y*y into ret;
    end //

delimiter ;
```

```sql
call myprocedure(@ret);
select @ret;
```

# 十九、遊標

在存儲過程中使用遊標可以對一個結果集進行移動遍歷。

遊標主要用於交互式應用，其中用戶需要對數據集中的任意行進行瀏覽和修改。

使用遊標的四個步驟：

1. 聲明遊標，這個過程沒有實際檢索出數據；
2. 打開遊標；
3. 取出數據；
4. 關閉遊標；

```sql
delimiter //
create procedure myprocedure(out ret int)
    begin
        declare done boolean default 0;

        declare mycursor cursor for
        select col1 from mytable;
        # 定義了一個 continue handler，當 sqlstate '02000' 這個條件出現時，會執行 set done = 1
        declare continue handler for sqlstate '02000' set done = 1;

        open mycursor;

        repeat
            fetch mycursor into ret;
            select ret;
        until done end repeat;

        close mycursor;
    end //
 delimiter ;
```

# 二十、觸發器

觸發器會在某個表執行以下語句時而自動執行：DELETE、INSERT、UPDATE。

觸發器必須指定在語句執行之前還是之後自動執行，之前執行使用 BEFORE 關鍵字，之後執行使用 AFTER 關鍵字。BEFORE 用於數據驗證和淨化，AFTER 用於審計跟蹤，將修改記錄到另外一張表中。

INSERT 觸發器包含一個名為 NEW 的虛擬表。

```sql
CREATE TRIGGER mytrigger AFTER INSERT ON mytable
FOR EACH ROW SELECT NEW.col into @result;

SELECT @result; -- 獲取結果
```

DELETE 觸發器包含一個名為 OLD 的虛擬表，並且是隻讀的。

UPDATE 觸發器包含一個名為 NEW 和一個名為 OLD 的虛擬表，其中 NEW 是可以被修改的，而 OLD 是隻讀的。

MySQL 不允許在觸發器中使用 CALL 語句，也就是不能調用存儲過程。

# 二十一、事務管理

基本術語：

- 事務（transaction）指一組 SQL 語句；
- 回退（rollback）指撤銷指定 SQL 語句的過程；
- 提交（commit）指將未存儲的 SQL 語句結果寫入數據庫表；
- 保留點（savepoint）指事務處理中設置的臨時佔位符（placeholder），你可以對它發佈回退（與回退整個事務處理不同）。

不能回退 SELECT 語句，回退 SELECT 語句也沒意義；也不能回退 CREATE 和 DROP 語句。

MySQL 的事務提交默認是隱式提交，每執行一條語句就把這條語句當成一個事務然後進行提交。當出現 START TRANSACTION 語句時，會關閉隱式提交；當 COMMIT 或 ROLLBACK 語句執行後，事務會自動關閉，重新恢復隱式提交。

設置 autocommit 為 0 可以取消自動提交；autocommit 標記是針對每個連接而不是針對服務器的。

如果沒有設置保留點，ROLLBACK 會回退到 START TRANSACTION 語句處；如果設置了保留點，並且在 ROLLBACK 中指定該保留點，則會回退到該保留點。

```sql
START TRANSACTION
// ...
SAVEPOINT delete1
// ...
ROLLBACK TO delete1
// ...
COMMIT
```

# 二十二、字符集

基本術語：

- 字符集為字母和符號的集合；
- 編碼為某個字符集成員的內部表示；
- 校對字符指定如何比較，主要用於排序和分組。

除了給表指定字符集和校對外，也可以給列指定：

```sql
CREATE TABLE mytable
(col VARCHAR(10) CHARACTER SET latin COLLATE latin1_general_ci )
DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
```

可以在排序、分組時指定校對：

```sql
SELECT *
FROM mytable
ORDER BY col COLLATE latin1_general_ci;
```

# 二十三、權限管理

MySQL 的賬戶信息保存在 mysql 這個數據庫中。

```sql
USE mysql;
SELECT user FROM user;
```

**創建賬戶** 

新創建的賬戶沒有任何權限。

```sql
CREATE USER myuser IDENTIFIED BY 'mypassword';
```

**修改賬戶名** 

```sql
RENAME USER myuser TO newuser;
```

**刪除賬戶** 

```sql
DROP USER myuser;
```

**查看權限** 

```sql
SHOW GRANTS FOR myuser;
```

**授予權限** 

賬戶用 username@host 的形式定義，username@% 使用的是默認主機名。

```sql
GRANT SELECT, INSERT ON mydatabase.* TO myuser;
```

**刪除權限** 

GRANT 和 REVOKE 可在幾個層次上控制訪問權限：

- 整個服務器，使用 GRANT ALL 和 REVOKE ALL；
- 整個數據庫，使用 ON database.\*；
- 特定的表，使用 ON database.table；
- 特定的列；
- 特定的存儲過程。

```sql
REVOKE SELECT, INSERT ON mydatabase.* FROM myuser;
```

**更改密碼** 

必須使用 Password() 函數進行加密。

```sql
SET PASSWROD FOR myuser = Password('new_password');
```

# 參考資料

- BenForta. SQL 必知必會 [M]. 人民郵電出版社, 2013.




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
