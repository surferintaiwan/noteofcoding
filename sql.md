# SQL

## ``CREATE TABLE `資料表名```

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

```sql
INSERT INTO `drinks` (`name`,`price`,`cost`)
VALUE ('阿華田',65,20);
```

## ``UPDATE `資料表名` SET `欄位名` = 新的值``

```sql
UPDATE `drinks`
SET `price` = 90
WHERE `name` = '冰咖啡';
```

## ``DELETE FROM `資料表名` + WHERE `欄位名` = 對應值``

```sql
DELETE FROM `drinks`
WHERE `name` = '冰咖啡';
```

