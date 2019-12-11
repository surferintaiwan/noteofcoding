# SQL基本指令

## `CRUD指令`

### `CREATE TABLE`建立資料表

``CREATE TABLE `資料表名```

用來創建資料表，可以[線上工具](https://wtools.io/generate-sql-create-table)快速產生創建資料表指令。

```sql
CREATE TABLE `drinks` (
	`id` INT unsigned NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(20) NOT NULL,
	`price` INT unsigned NOT NULL,
	`cost` INT unsigned NOT NULL,
	PRIMARY KEY (`id`)
);
```

### `INSERT INTO`新增資料

``INSERT INTO `資料表名` (`欄位名`) + VALUE(`對應值`)``

```sql
INSERT INTO `drinks` (`name`,`price`,`cost`)
VALUE ('阿華田',65,20);
```

### `UPDATE`更新資料

``UPDATE `資料表名` SET `欄位名` = 新的值``

用來更新特定欄位的資料，搭配WHERE可以指定要更新哪一筆資料。

```sql
UPDATE `drinks`
SET `price` = 90
WHERE `name` = '冰咖啡';
```

### `DELETE FROM`刪除資料

``DELETE FROM `資料表名` + WHERE `欄位名` = 對應值``

搭配WHERE刪除特定一筆資料。

```sql
DELETE FROM `drinks`
WHERE `name` = '冰咖啡';
```

### `SELECT * FROM`查詢資料

``SELECT * FROM `資料表名` + WHERE `欄位名` = 對應值``

\*星號代表把資料表所有欄位都列出來。

```sql
SELECT * FROM `drinks`;
```

如果只要列出特定欄位，就要如下指定欄位。

```sql
SELECT `name`, `price` FROM `drinks`;
```

搭配`WHERE`可以再指定是哪一筆資料。

```sql
SELECT * FROM `orders`
WHERE `customer_id` = 2 
```

## `可獨立存在的搭配指令`

這裡指的就是可以自己當一行的開頭的指令，但當然還是要先搭配CRUD這幾個指令在第一行。

### `WHERE`指定欄位

``WHERE `欄位名` = '對應值'``

可以在更新資料、查詢資料時使用。

### `ORDER BY`排序

``ORDER BY `欄位名` ASC``

可以進行資料排序，或是``ORDER BY `欄未名`　DESC``

### `HAVING`篩選函式計算的值

延續前面，如果我想要找出total\_sold大於1的資料，直覺上會使用`WHERE`去做篩選，但會出現錯誤，因為WHERE只讀得原本資料表裡已經有的欄位，像在計算時產生的新欄位它就沒辦法辨識。

```sql
SELECT `drink_id`, SUM(`quantity`) AS total_sold
FROM `orders`
WHERE `total_sold` < 1
GROUP BY `drink_id`;
```

所以就要改用HAVING接在最後面

```sql
SELECT `id`, COUNT(`amount`) AS `total_sold`
FROM `orders`
GROUP BY `drink_id`
HAVING `total_sold` > 1
```

## `GROUP BY`分組

```sql
SELECT `id` ,SUM(`amount`) AS `total_sold`
FROM `orders`
GROUP BY `drink_id`
```

## `不可獨立存在的搭配指令`

這裡指的是必須接在SELECT之後的指令。

### `AVG`平均

```sql
SELECT AVG(`price`) AS `avg_price`
FROM `drinks`
WHERE `price` > 10;
```

### `SUM`加總

```sql
SELECT SUM(`price`) AS `sum_price`
FROM `drinks`
WHERE `price` > 10;
```

### `COUNT計算次數`

以下面為例，SELECT \`id\`就是說我要鎖定id去做出現次數的計算，接著程式會去找看各個id，amount裡面有值的就算+1，  
相對地，若是`COUNT`選了一個裡面沒有值的欄位來計算，那就會發現算出來的值跟自己想的不一樣。  
所以使用的時候要注意，到底是要COUNT什麼欄位?

| id | total\_sold |
| :--- | :--- |
| 1 | 1 |
| 2 | 1 |
| 3 | 2 |

```sql
SELECT `id`, COUNT(`amount`) AS `total_sold`
FROM `orders`
GROUP BY `drink_id`
```

## `Join` 多個資料表比對

基本的公式大概是這樣:

```sql
SELECT A資料表.欄位名稱, B資料表.
FROM 放左邊的那張表 // 如果是同一張表自己比較，可以搭配AS產出新的表
JOIN 放右邊的那張表 // 如果是同一張表自己比較，可以搭配AS產出新的表
ON 兩張表的參照基準 //可以放> = <等等的比較運算子
```



### INNER Join 兩邊互相都要有

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/7726/ExportedContentImage_02.png)

```sql
SELECT `orders`.*, `drinks`.`name`, `drinks`.`price` 
FROM `orders` 
JOIN `drinks`
ON `orders`.`drink_id` = `drinks`.`id`
```

FROM的資料表就是左邊T1的意思，JOIN的資料表就是右邊T2的意思，但因為是你有我也有的狀況，所以FROM跟JOIN顛倒過來寫也是一樣意思的。

```sql
SELECT `orders`.*, `drinks`.`name`, `drinks`.`price` 
FROM `drinks` 
JOIN `drinks`
ON `orders`.`drink_id` = `drinks`.`id`
```

### **LEFT JOIN 只有左邊有**

\*\*\*\*

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/7728/ExportedContentImage_04.png)

```sql
SELECT `orders`.*, `drinks`.`name`, `drinks`.`price` 
FROM `drinks` 
RIGHT JOIN `orders`
ON `orders`.`drink_id` = `drinks`.`id`
```

| id | drink\_id | customer\_id | amount | created | name | price |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 2 | 4 | 2019-07-14T13:55:56Z | 阿華田 | 65 |
| 2 | 2 | 2 | 3 | 2019-07-14T14:21:23Z | 百香紅茶 | 45 |
| 3 | 3 | 2 | 9 | 2019-07-14T14:26:12Z | 四季春茶 | 30 |
| 4 | 3 | 1 | 2 | 2019-07-14T18:51:50Z | 四季春茶 | 30 |

### **RIGHT JOIN 只有右邊有**

\*\*\*\*

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/7729/ExportedContentImage_05.png)

```sql
SELECT `orders`.*, `drinks`.`name`, `drinks`.`price` 
FROM `orders` 
RIGHT JOIN `drinks`
ON `orders`.`drink_id` = `drinks`.`id`
```

| id | drink\_id | customer\_id | amount | created | name | price |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 2 | 4 | 2019-07-14T13:55:56Z | 阿華田 | 65 |
| 2 | 2 | 2 | 3 | 2019-07-14T14:21:23Z | 百香紅茶 | 45 |
| 3 | 3 | 2 | 9 | 2019-07-14T14:26:12Z | 四季春茶 | 30 |
| 4 | 3 | 1 | 2 | 2019-07-14T18:51:50Z | 四季春茶 | 30 |
| \(null\) | \(null\) | \(null\) | \(null\) | \(null\) | 愛玉冰茶 | 30 |

### **SELF JOIN 以自己跟自己比對**

就是同一張資料表自己跟自己比對，有兩種情境可以適用。

**1. 把自己某一筆資料，跟自己其他資料比對帶入**  
假設有張公司職員表，其中一欄叫做deputyid存放代理人的id，那要怎麼查詢某人的代理人名字是誰呢?  
這時候就可以搭配`AS`把一張資料表變成兩張不同名稱的資料表，再回頭SELECT FROM JOIN ON，其實蠻怪的，AS寫在後面，但前面的SELECT竟然知道我新的兩張表是指誰。

```sql
SELECT t1.* , t2.name
FROM emp AS  t1
JOIN emp AS t2
ON t1.deputyid = t2.id
```

**2. 以自己某一筆資料為基準，跟自己的其他資料比較**

如果想知道我的飲料表裡面，比我阿華田便宜的飲料有哪些**?**  
一樣透過AS把一張表變成兩張表**，**接著就會去比較t1價格中比t2價格還高的飲料有哪些，但我只想知道有哪些比阿華田便宜，所以最後再透過WHERE指定阿華田這個飲料。

```sql
SELECT t1.name, t2.name AS cheaper_name, t2.price 
FROM `drinks` AS t1 
JOIN `drinks` AS t2
ON t1.price > t2.price
WHERE t1.name = '阿華田'
```

| name | cheaper\_name | price |
| :--- | :--- | :--- |
| 阿華田 | 百香紅茶 | 45 |
| 阿華田 | 四季春茶 | 30 |
| 阿華田 | 愛玉冰茶 | 30 |

## 練習時間

可以到[SQL FIDDLE](http://sqlfiddle.com/)在線上練習SQL指令，練習建立下面三張資料表跟資料。

drinks

| 飲料名稱 | 售價 | 成本 |
| :--- | :--- | :--- |
| 阿華田 | 65 | 20 |
| 百香紅茶 | 45 | 10 |
| 四季春茶 | 25 | 5 |
| 愛玉冰茶 | 50 | 7 |
| 冰咖啡 | 70 | 30 |

customers

| 顧客姓名 | 電話 | 生日 |
| :--- | :--- | :--- |
| Johny | 0980123123 | 1997-04-22 |
| Wendy | 0919456456 | 1999-07-07 |

orders

| 飲料編號 | 顧客編號 | 數量 | 建立日期 |
| :--- | :--- | :--- | :--- |
| 1 | 2 | 4 | 2019-07-14 13:55:56 |
| 2 | 2 | 3 | 2019-07-14 14:21:23 |
| 3 | 2 | 9 | 2019-07-14 14:26:12 |
| 3 | 1 | 2 | 2019-07-14 18:51:50 |

```sql
CREATE TABLE `drinks` (
	`id` INT unsigned NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(20) NOT NULL,
	`price` INT unsigned NOT NULL,
	`cost` INT unsigned NOT NULL,
	PRIMARY KEY (`id`)
);

INSERT INTO `drinks` (`name`,`price`,`cost`)
VALUE ('阿華田',65,20);

INSERT INTO `drinks` (`name`,`price`,`cost`)
VALUE ('百香紅茶',45,10);

INSERT INTO `drinks` (`name`, `price`, `cost`)
VALUE ('四季春茶',30,5);

INSERT INTO `drinks` (`name`, `price`, `cost`)
VALUE ('愛玉冰茶',30,7);

INSERT INTO `drinks` (`name`, `price`, `cost`)
VALUE ('冰咖啡',70,30);

UPDATE `drinks`
SET `price` = 90
WHERE `name` = '冰咖啡';

DELETE FROM `drinks`
WHERE `name` = '冰咖啡';

CREATE TABLE `customers` (
	`id` INT unsigned NOT NULL AUTO_INCREMENT,
	`tel_no` VARCHAR(20) NOT NULL,
	`birth` DATE NOT NULL,
	`name` VARCHAR(20) NOT NULL,
	PRIMARY KEY (`id`)
);

INSERT INTO `customers` (`name`,`tel_no`,`birth`)
VALUE ('Johny','0980123123','1997-04-22');

INSERT INTO `customers` (`name`,`tel_no`,`birth`)
VALUE('Wendy','0919456456','1999-07-07');

CREATE TABLE `orders` (
	`id` INT unsigned NOT NULL AUTO_INCREMENT,
	`drink_id` INT unsigned NOT NULL,
	`customer_id` INT unsigned NOT NULL,
	`amount` INT(20) unsigned NOT NULL,
	`created` DATETIME NOT NULL,
	PRIMARY KEY (`id`)
);

INSERT INTO `orders` (`drink_id`,`customer_id`,`amount`,`created`)
VALUE (1,2,4, '2019-07-14 13:55:56');

INSERT INTO `orders` (`drink_id`,`customer_id`,`amount`,`created`)
VALUE (2,2,3, '2019-07-14 14:21:23');

INSERT INTO `orders` (`drink_id`,`customer_id`,`amount`,`created`)
VALUE (3,2,9, '2019-07-14 14:26:12');

INSERT INTO `orders` (`drink_id`,`customer_id`,`amount`,`created`)
VALUE (3,1,2, '2019-07-14 18:51:50');
```

{% hint style="info" %}
下面這題當時有搞錯，要好好記錄一下。  
Q.請問以下 SQL 語法所代表的意義為何？

```sql
SELECT `customer_id`, `drink_id`, MAX(`quantity`) 
FROM orders 
GROUP BY `customer_id`; 
```

A.找出所有有消費過的顧客中，每個顧客消費過最多杯的訂單是什麼，以及他們買了幾杯。

如果不加上GROUP BY會發現它會篩選所有使用者中，購買數量最高的。
{% endhint %}

