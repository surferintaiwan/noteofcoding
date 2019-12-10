# express-session儲存session

## 安裝express-session

### 安裝express-session

這邊使用express-session這個套件，讓express能夠擁有session的功能。  
這個 middlewave 可以幫你截取 cookie 資訊、生成 session 並把 session 資訊存放在伺服器端。

```text
npm install express-session
```

## 載入套件

```javascript
// app.js
const session = require('express-session')

app.use(session({
  secret: 'your secret key',   // secret: 定義一組屬於你的字串做為私鑰
  resave: false,
  saveUninitialized: true,
}))
```

* `secret`：在這串設定中最需要認識的是 `secret`，並是最必要的選項。這個參數就是 session 用來驗證 session id 的字串。它可以是隨便一個字串，或是一個字串陣列裡面包括幾組 `secret`。 上面的程式碼我們設定為 `'your secret key'`，但你可以隨機輸入一個字串。
* `resave`：當設定為 `true` 時，會在每一次與使用者互動後，強制把 session 更新到 session store 裡。
* `saveUninitialized`：強制將未初始化的 session 存回 session store。未初始化表示這個 session 是新的而且沒有被修改過，例如未登入的使用者的 session。

## 查看session資訊是否成功帶入

只要每次有跟使用者互動，就會把session資訊塞進使用者瀏覽器的cookie裡面。

![](../.gitbook/assets/image%20%284%29.png)

{% hint style="info" %}
express-session能夠將session id儲存在瀏覽器的cookie上，同時也會將session儲存在伺服器上。

 [https://www.npmjs.com/package/express-session](https://www.npmjs.com/package/express-session)  
文件中有寫到：  
”**Note** Session data is _not_ saved in the cookie itself, just the session ID. Session data is stored server-side.“  
“**Warning** The default server-side session storage, MemoryStore, is _purposely_ not designed for a production environment. It will leak memory under most conditions, does not scale past a single process, and is meant for debugging and developing.”  
  
session是存在server上沒錯，在我們沒有選擇儲存session的方式時，預設是儲存在記憶體中，透過設定Session Store，可以選擇我們要如何去儲存session，像是存在mongoDB, redis or MySQL  
  
將session存到mongoDB的方式：[https://www.npmjs.com/package/connect-mongo](https://www.npmjs.com/package/connect-mongo)
{% endhint %}

