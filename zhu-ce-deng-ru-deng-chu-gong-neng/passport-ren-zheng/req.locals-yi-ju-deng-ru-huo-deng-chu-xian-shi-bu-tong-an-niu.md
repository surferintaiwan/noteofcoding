# req.locals依據登入或登出顯示不同按鈕

## 使用req.locals變數讓views使用

```text
// app.js
// 使用 Passport
// ...
// 載入 Passport config
// ...
// 登入後可以取得使用者的資訊方便我們在 view 裡面直接使用
app.use((req, res, next) => {
  res.locals.user = req.user
  res.locals.isAuthenticated = req.isAuthenticated()      // 辨識使用者是否已經登入的變數，讓 view 可以使用
  next()
})
```

## 修改頁面

主要就是把原本在版頭的Create刪除掉，換成Login或Logout，接著把Create移到版身。

### 修改版頭

```text
<!-- views/layouts/main.handlebars -->
<header>
  <nav class="navbar navbar-dark bg-primary justify-content-between">
    <a class="navbar-brand" href="/">Todo List</a>
    {{#if isAuthenticated}}
    <a href="/users/logout" class="btn btn-outline-light">Logout</a>
    {{else}}
    <a href="/users/login" class="btn btn-outline-light">Login</a>
    {{/if}}
  </nav>
</header>
```

### 修改版身

```text
<!-- views/index.handlebars -->
<a href="/todos/new" class="btn btn-primary mb-3">Create</a>
<!-- ...  -->
```

![](../../.gitbook/assets/image%20%2811%29.png)

![](../../.gitbook/assets/image%20%282%29.png)

{% hint style="info" %}
關於這段程式碼，有兩個疑問

```text
res.locals.user = req.user 
```

  
1. res.locals可以把它想成就只是express提供給我暫時儲存東西的地方嗎?  
至於res.locals後面其實我想要.user或是.newUser，可以看想怎麼命名都行。  
  
2. req.user的user，是根據之前在哪裡有命名過，所以才會叫做user?  
是不是有辦法改名，改成req.oneuser之類的來使用呢?



1. res.locals是拿來儲存給view用的物件，你想在裡面存什麼都可以  
2. 可以改的唷，像我比較習慣用currentUser  
[https://stackoverflow.com/questions/40577662/passportjs-rename-user-object/44261945](https://stackoverflow.com/questions/40577662/passportjs-rename-user-object/44261945) 
{% endhint %}

