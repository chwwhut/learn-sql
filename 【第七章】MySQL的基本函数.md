## 【第七章】MySQL的基本函数

内置的用来处理数值、文本、日期等的函数

## 1. 数值函数

**小结**

主要介绍最常用的几个数值函数：ROUND、TRUNCATE、CEILING、FLOOR、ABS、RAND

查看MySQL全部数值函数可**谷歌 'mysql numeric function'**，第一个就是官方文档。

```sql
SELECT ROUND(5.7365, 2)  -- 四舍五入
SELECT TRUNCATE(5.7365, 2)  -- 截断
SELECT CEILING(5.2)  -- 天花板函数，大于等于此数的最小整数
SELECT FLOOR(5.6)  -- 地板函数，小于等于此数的最大整数
SELECT ABS(-5.2)  -- 绝对值
SELECT RAND()  -- 随机函数，0到1的随机值
```

---

## 2. 字符串函数

**小结**

依然介绍最常用的字符串函数：
1. LENGTH, UPPER, LOWER
2. TRIM, LTRIM, RTRIM
3. LEFT, RIGHT, SUBSTRING
4. LOCATE, REPLACE, 【CONCAT】

查看全部搜索关键词 **'mysql string functions'**

长度、转大小写：

```sql
SELECT LENGTH('sky')  -- 字符串字符个数/长度（LENGTH）
SELECT UPPER('sky')  -- 转大写
SELECT LOWER('Sky')  -- 转小写
```

用户输入时时常多打空格，下面三个函数用于处理/修剪（trim）字符串前后的空格，L、R 表示 LEFT、RIGHT：

```sql
SELECT LTRIM('  Sky')
SELECT RTRIM('Sky  ')
SELECT TRIM(' Sky ')
```

切片：

```sql
-- 取左边，取右边，取中间
SELECT LEFT('Kindergarden', 4)  -- 取左边（LEFT）4个字符
SELECT RIGHT('Kindergarden', 6)  -- 取右边（RIGHT）6个字符
SELECT SUBSTRING('Kindergarden', 7, 6)  
-- 取中间从第7个开始的长度为6的子串（SUBSTRING）
-- 注意是从第1个（而非第0个）开始计数的
-- 省略第3参数（子串长度）则一直截取到最后
```

定位：

```sql
SELECT LOCATE('gar', 'Kindergarden')  -- 定位（LOCATE）首次出现的位置
-- 没有的话返回0（其他编程语言大多返回-1，可能因为索引是从0开始的）
-- 这个定位/查找函数依然是不区分大小写的
```

替换：

```sql
SELECT REPLACE('Kindergarten', 'garten', 'garden')
```

连接：

```sql
USE sql_store;

SELECT CONCAT(first_name, ' ', last_name) AS full_name
-- concatenate v. 连接
FROM customers
```

---

## 3. MySQL中的日期函数

**小结**

本节学基本的处理时间日期的函数，下节课学日期时间的格式化

1. NOW, CURDATE, CURTIME
2. YEAR, MONTH, DAY, HOUR, MINUTE, SECOND, DAYNAME, MONTHNAME
3. EXTRACT(单位 FROM 日期时间对象)， 如 EXTRACT(YEAR FROM NOW())

**实例**

当前时间

```sql
SELECT NOW()  -- 2020-09-12 08:50:46
SELECT CURDATE()  -- current date, 2020-09-12
SELECT CURTIME()  -- current time, 08:50:46
```

以上函数将返回时间日期对象

提取时间日期对象中的元素：

```sql
SELECT YEAR(NOW())  -- 2020
```

还有MONTH, DAY, HOUR, MINUTE, SECOND。

以上函数均返回整数，还有另外两个返回字符串的：

```sql
SELECT DAYNAME(NOW())  -- Saturday
SELECT MONTHNAME(NOW())  -- September
```

**标准SQL语句**有一个类似的函数 EXTRACT()，若需要在不同DBMS中录入代码，最好用EXTRACT()：

```sql
SELECT EXTRACT(YEAR FROM NOW())
```

当然第一参数也可以是MONTH, DAY, HOUR ……
总之就是：`EXTRACT(单位 FROM 日期时间对象)`

返回【今年】的订单

用时间日期函数而非手动输入年份，代码更可靠，不会随着时间的改变而失效

```sql
USE sql_store;

SELECT * 
FROM orders
WHERE YEAR(order_date) = YEAR(now())
```

---

## 4. 格式化日期和时间

**小结**

`DATE_FORMAT(date, format)` 将 date 根据 format 字符串进行格式化。

`TIME_FORMAT(time, format)` 类似于 DATE_FORMAT 函数，但这里 format 字符串只能包含用于小时，分钟，秒和微秒的格式说明符。其他说明符产生一个 NULL 值或0。

**方法**

很多像这种完全不需要记也不可能记得完，重要的是知道有这么个可以实现这个功能的函数，具体的**格式说明符（Specifiers）**可以需要的时候去查，至少有两种方法：

1. 直接谷歌关键词 如 **mysql date format functions**, 其实是在官方文档的 12.7 Date and Time Functions 小结里，有两个函数的说明和 specifiers 表

2. 用软件里的帮助功能，如 workbench 里的 HELP INDEX 打开官方文档查询或者右侧栏的 automatic comtext help (其是也是查官方文档，不过是自动的)

**实例**

```sql
SELECT DATE_FORMAT(NOW(), '%M %d, %Y')  -- September 12, 2020
-- 格式说明符里，大小写是不同的，这是目前SQL里第一次出现大小写不同的情况
SELECT TIME_FORMAT(NOW(), '%H:%i %p')  -- 11:07 AM
```

---

## 5. 计算日期和时间

**小结**

有时需要对日期事件对象进行运算，如增加一天或算两个时间的差值之类，介绍一些最有用的日期时间计算函数：

1. DATE_ADD, DATE_SUB
2. DATEDIFF
3. TIME_TO_SEC

增加或减少一定的天数、月数、年数、小时数等等

```sql
SELECT DATE_ADD(NOW(), INTERVAL -1 DAY)
SELECT DATE_SUB(NOW(), INTERVAL 1 YEAR)
```

但其实不用函数，直接加减更简洁：

```sql
NOW() - INTERVAL 1 DAY
NOW() - INTERVAL 1 YEAR 
```

计算日期差异

```sql
SELECT DATEDIFF('2019-01-01 09:00', '2019-01-05')  -- -4
-- 会忽略时间部分，只算日期差异

借助 TIME_TO_SEC 函数计算时间差异

TIME_TO_SEC：计算从 00:00 到某时间经历的秒数

SELECT TIME_TO_SEC('09:00')  -- 32400
SELECT TIME_TO_SEC('09:00') - TIME_TO_SEC('09:02')  -- -120
```

---

## 6. IFNULL和COALESCE函数

**小结**

两个用来替换空值的函数：IFNULL, COALESCE. 后者更灵活

**案例**

将 orders 里 [shipper.id](https://link.zhihu.com/?target=http%3A//shipper.id/) 中的空值替换为 'Not Assigned'（未分配）

```sql
USE sql_store;

SELECT 
    order_id,
    IFNULL(shipper_id, 'Not Assigned') AS shipper
    /* If expr1 is not NULL, IFNULL() returns expr1; 
    otherwise it returns expr2. */
FROM orders
```

将 orders 里 [shipper.id](https://link.zhihu.com/?target=http%3A//shipper.id/) 中的空值替换为 comments，若 comments 也为空则替换为 'Not Assigned'（未分配）

```sql
USE sql_store;

SELECT 
    order_id,
    COALESCE(shipper_id, comments, 'Not Assigned') AS shipper
    /* Returns the first non-NULL value in the list, 
    or NULL if there are no non-NULLvalues. */
FROM orders
```

**COALESCE 函数是返回一系列值中的首个非空值，更灵活**

（coalesce vi. 合并；结合；联合）

返回一个有如下两列的查询结果：

1. customer (顾客的全名)
2. phone (没有的话，显示'Unknown')

```sql
USE sql_store;

SELECT 
    CONCAT(first_name, ' ', last_name) AS customer,
    IFNULL/COALESCE(phone, 'Unknown') AS phone   
FROM customers
```

---

## 7. IF函数

**小结**

根据是否满足条件返回不同的值:

`IF(条件表达式, 返回值1, 返回值2)` 返回值可以是任何东西，数值 文本 日期时间 空值null 均可

**案例**

将订单表中订单按是否是今年的订单分类为active（活跃）和archived（存档），之前讲过用UNION法，即用两次查询分别得到今年的和今年以前的订单，添加上分类列再用UNION合并，这里直接在SELECT里运用IF函数可以更容易地得到相同的结果

```sql
USE sql_store;

SELECT *,
    IF(YEAR(order_date) = YEAR(NOW()),
       'Active',
       'Archived') AS category
FROM orders
```

得到包含如下字段的表：
1. product_id
2. name (产品名称)
3. orders (该产品出现在订单中的次数)
4. frequency (根据是否多于一次而分类为'Once'或'Many times')

```sql
USE sql_store;

SELECT 
    product_id,
    name,
    COUNT(*) AS orders,
    IF(COUNT(*) = 1, 'Once', 'Many times') AS frequency
    /* 因为之后的内连接筛选掉了无订单的商品，
    所以这里不变考虑次数为0的情况 */
FROM products
JOIN order_items USING(product_id)
GROUP BY product_id
```

另外，发现如果想用同级列别名orders怎么都不行：

若写成 `IF(orders = 1, 'Once', 'Many times') AS frequency`
会报错：Error Code: 1054. Unknown column 'orders' in 'field list'

若写成 `IF((SELECT orders) = 1, 'Once', 'Many times') AS frequency`
会报错：Error Code: 1247. Reference 'orders' not supported (reference to group function)

1. 首先，普通列（不含聚合函数的列）的列别名 可以 通过 (SELECT 列别名) 的方式方式进行同级引用；

2. 但，由分组聚合函数 COUNT(*) AS orders 构成的列的列别名 不可以 通过(SELECT 列别名) 进行同级引用，会报错：Reference 'orders' not supported (reference to group function)；

---

## 8. CASE运算符

**小结**

当分类多余两种时，可以用IF嵌套，也可以用CASE语句，后者可读性更好

CASE语句结构：

```sql
CASE 
    WHEN …… THEN ……
    WHEN …… THEN ……
    WHEN …… THEN ……
    ……
    [ELSE ……] （ELSE子句是可选的）
END
```

**案例**

不是将订单分两类，而是分为三类：今年的是 'Active', 去年的是 'Last Year', 比去年更早的是 'Achived'：

```sql
USE sql_store;

SELECT
    order_id,
    CASE
        WHEN YEAR(order_date) = YEAR(NOW()) THEN 'Active'
        WHEN YEAR(order_date) = YEAR(NOW()) - 1 THEN 'Last Year'
        WHEN YEAR(order_date) < YEAR(NOW()) - 1 THEN 'Achived'
        ELSE 'Future'  
    END AS 'category'
FROM orders
```

ELSE 'Future' 是可选的，实验发现若分类不完整，比如只写了今年和去年的两个分类条件，则不在这两个分类的记录的 category 字段会是 null.

得到包含如下字段的表：customer, points, category（根据积分 <2k、2k~3k（包含两端）、>3k 分为青铜、白银和黄金用户）

之前也是用过 UNION 法，分别查询增加分类字段再合并，很麻烦。

```sql
USE sql_store;

SELECT
    CONCAT(first_name, ' ', last_name) AS customer,
    points,
    CASE
        WHEN points < 2000 THEN 'Bronze'
        WHEN points BETWEEN 2000 AND 3000 THEN 'Silver'
        WHEN points > 3000 THEN 'Gold'
        -- ELSE null
    END AS category
FROM customers
ORDER BY points DESC
```

其实也可以用IF嵌套，甚至代码还少些，但感觉没有CASE语句结构清晰、可读性好

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS customer,
    points,
    IF(points < 2000, 'Bronze', 
        IF(points BETWEEN 2000 AND 3000, 'Silver', 
        -- 第二层的条件表达式也可以简化为 <= 3000
            IF(points > 3000, 'Gold', null))) AS category
FROM customers
ORDER BY points DESC
```

