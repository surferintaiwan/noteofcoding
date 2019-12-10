# 開發筆記

建立基本的app.js

建立model

路由設計



記得body-parser的用法  
require之後要設定才能用



如果要使用publics裡的靜態資源  
記得要在app.js設定過才能用



在.find取得的物件，沒辦法插入新的key跟value?

在編輯跟刪除的路由，把所有資料的amount拿出來加總會重複，是否可以封裝成module



首頁路由不知道有沒有問題  
後面為了顯示totalAmount，有寫個判斷。  
  
要怎麼在.find外面設立一個變數，並且可以把find到的東西，存在那個變數裡呢?  
let x = ''  
Record.find\(\)

/user/register路由使用.exec跟.then的差別

/user/register路由使用.catch\(err\)跟直接寫err的差別

發現原來如果物件直接塞一個已經有帶值的變數，會直接變成key跟value  
`let x = {}  
y = '123'  
x = {y}  
console.log(x) // 會是{ y: '123'}`

