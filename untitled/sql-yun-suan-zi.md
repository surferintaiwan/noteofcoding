# SQL運算子

## 計算運算子

加上``AS `discount_price```，資料表被印出來的時候，新的那一欄會叫做discount\_price，  
如果不加，就會price \* 0.8

```sql
SELECT `name`, `price`, (`price` * 0.8) AS `discount_price`
FROM `dinrks`
```

## 比較運算子

* `=`
* `<>` 這比較特別，是不等於的意思
* `>`
* `<`
* `>=`
* `<=`

```sql
SELECT `name`, `price` 
FROM `dinrks`
WHERE `price` > 30
```

## 邏輯運算子

* AND
* OR
* BETWEEN
* LIKE

以下這兩個針對特定區間查詢資料的例子其實是一樣的結果，只是一個用計算運算子搭配邏輯運算子，另一個則直接使用邏輯運算子。

```sql
SELECT `name`, `price`
FROM `dinrks`
WHERE `price` > 30 AND `price` < 40
```

```sql
SELECT `name`, `price`
FROM `dinrks`
WHERE `price` BETWEEN 30 AND 40
```

## 關鍵字搜尋

邏輯運算子`LIKE`搭配`%`，就可以用關鍵字查詢。

* `'%茶'` 代表甚麼甚麼茶
* `'茶%'` 代表茶甚麼甚麼
* `'%茶%'` 代表甚麼甚麼茶甚麼甚麼

```sql
SELECT `name`, `price`
FROM `dinrks
WHERE `name` LIKE '%茶%'
```

