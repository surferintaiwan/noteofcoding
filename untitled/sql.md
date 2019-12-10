# SQL基本指令

## ``CREATE TABLE `資料表名```

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

## ``INSERT INTO `資料表名` (`欄位名`) + VALUE(`對應值`)``

用來新增資料。

```sql
INSERT INTO `drinks` (`name`,`price`,`cost`)
VALUE ('阿華田',65,20);
```

## ``UPDATE `資料表名` SET `欄位名` = 新的值``

用來更新特定欄位的資料，搭配WHERE可以指定要更新哪一筆資料。

```sql
UPDATE `drinks`
SET `price` = 90
WHERE `name` = '冰咖啡';
```

## ``DELETE FROM `資料表名` + WHERE `欄位名` = 對應值``

搭配WHERE刪除特定一筆資料。

```sql
DELETE FROM `drinks`
WHERE `name` = '冰咖啡';
```

## ``SELECT * FROM `資料表名` + WHERE `欄位名` = 對應值``

\*星號代表把資料表所有欄位都列出來。

```sql
SELECT * FROM `drinks`;
```

如果只要列出特定欄位，就要如下指定欄位。

```sql
SELECT `name`, `price` FROM `drinks`;
```

搭配WHERE可以再指定是哪一筆資料。

```sql
SELECT * FROM `orders`
WHERE `customer_id` = 2 
```

## `WHERE 欄位名 = '對應值'`

可以在更新資料、查詢資料時使用。

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

INSERT INTO `drinks` (`name`, `price`, `cost`)
VALUE ('熱咖啡',70,30);

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

