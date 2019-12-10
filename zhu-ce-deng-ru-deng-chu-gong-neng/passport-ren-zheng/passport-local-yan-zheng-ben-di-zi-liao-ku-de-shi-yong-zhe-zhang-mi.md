# passport local驗證登入

## 安裝 passport 與 passport-local

```text
npm install passport passport-local
```

## 新增config/passport.js來設定passport

### 載入passport-local

其實下面這些可以放在app.js裡，但為了維護方便開一個config/passport.js

```javascript
// config/passport.js
 
const LocalStrategy = require('passport-local').Strategy
const mongoose = require('mongoose')
// 載入 User model
const User = require('../models/user')
module.exports = passport => {
  // 使用 Passport 的官方文件上的語法
  // 定義 usernameField 為 email 
}
```

### 設定passport-local

```javascript
// passport.js
 
const LocalStrategy = require('passport-local').Strategy
const mongoose = require('mongoose')
// 載入 User model
const User = require('../models/user')
module.exports = passport => {
  passport.use(
    new LocalStrategy({ usernameField: 'email' }, (email, password, done) => {
      User.findOne({
        email: email
      }).then(user => {
        if (!user) {
          return done(null, false, { message: 'That email is not registered' })
        }
        if (user.password != password) {
          return done(null, false, { message: 'Email or Password incorrect' })
        }
        return done(null, user)
      })
    })
  )
}
```

最後，為了要支援 session 功能，我們要貼上 Passport 提供的 serialize 與 deserialize 的範例程式碼：

```javascript
// config/passport.js
 
const LocalStrategy = require('passport-local').Strategy
const mongoose = require('mongoose')
// 載入 User model
const User = require('../models/user')
module.exports = passport => {
  passport.use(
    // use LocalStrategy
    // ...
    // ...
  )
  passport.serializeUser((user, done) => {
      done(null, user.id)
  })
  passport.deserializeUser((id, done) => {
    User.findById(id, (err, user) => {
      done(err, user)
    })
  })
}
```

## 修改app.js

### 載入passport

```javascript
// app.js
 
// 載入 express-handlebars, body-parser, method-override, express-session  等
// ...
// 載入 passport
const passport = require('passport')
// 使用 express-handlebars, body-parser, method-override 等
// ...
// 載入 User model
// ...
```

### 使用passport

```javascript
// app.js
 
// 載入 express-handlebars, body-parser, method-override 等
// ...
// 載入 express-session 與 passport
// ...
// 使用 express-handlebars, body-parser, method-override, express session  等
// ...
// 使用 Passport 
app.use(passport.initialize())
app.use(passport.session())
// 載入 User model
// ...
```

### 把拿到的req.user存起來給view用

把從請求中取得的用戶物件 `req.user`放到 res.local 裡，以便未來在 view 中可以取得當前登入的使用者資訊。

```javascript
// app.js
 
// 載入 express-handlebars, body-parser, method-override 等
// ...
// 載入 express-session 與 passport
// ...
// 使用 express-handlebars, body-parser, method-override 等
// ...
// 使用 express session 
// ...
// 使用 Passport 
// ...
// 載入 Passport config
require('./config/passport')(passport)
// 登入後可以取得使用者的資訊方便我們在 view 裡面直接使用
app.use((req, res, next) => {
  res.locals.user = req.user
  next()
})
// 載入 User model
// ...
```

{% hint style="info" %}
為什麼在 require\(...\) 之後我們要加上 `(passport)` 呢？

這裡的 `passport` 是一個 Passport 套件的 instance。仔細觀察我們在 `./config/passport` 裡的程式碼：

```javascript
// ./config/passport.js
module.exports = passport => {
  passport.use(
    new LocalStrategy({ usernameField: 'email' }, (email, password, done) => {
      // 使用 LocalStrategy 
    })
  )
  // ...
}
```

你會發現從`./config/passport.js` 中 export 出來的是一個函式，它需要接收一個 Passport instance。所以在`require('./config/passport')(passport)` 這行程式碼裡，我們將 `passport` 當作參數傳到 `./config/passport.js` 裡。
{% endhint %}

## 修改routes/user.js

### 載入passport

```javascript
const passport = require('passport')  
```

### 登入檢查

其實有兩種寫法，  
1.路由後面看是用一個`(req, res, next)=>{}`把`passport.authenticate()(req, res, next)`包起來，  
2.路由後面可以直接寫`passport.authenticate()`就好，  
兩者差別在於在`passport.authenticate()`前面可以透過req或res做一些事情。

```javascript
// routes/user.js
const express = require('express')
const router = express.Router()
const passport = require('passport')               // 載入 passport
const User = require('../models/user')
// 登入頁面
router.get('/login', (req, res) => {
  res.render('login')
})
// 登入檢查第一種寫法
router.post('/login', (req, res, next) => {
 // 這邊就可以console.log(req)一開始的東西  
  passport.authenticate('local', {                        // 使用 passport 認證
    successRedirect: '/',                                         // 登入成功會回到根目錄
    failureRedirect: '/users/login'                        // 失敗會留在登入頁面
  })(req, res, next)
 // 這邊也可以console.log(req)的變化
})

// 登入檢查第二種寫法
router.post('/login', passport.authenticate('local', {                        // 使用 passport 認證
    successRedirect: '/',                                         // 登入成功會回到根目錄
    failureRedirect: '/users/login'                        // 失敗會留在登入頁面
  })
})

// 登入檢查
// ...
// 註冊頁面
// ...
// 註冊檢查
// ...
module.exports = router
```

\*\*\*\*

{% hint style="info" %}
**為何要帶\(req, res, next\)?**

首先，我們載入 `passport`，然後更新「登入檢查」這個 action，讓它使用 Passport 提供的 `authenticate` 方法來執行認證。 Passport authenticate 的使用方法看來比較複雜，我們用以下的投影片來一步步解釋：

[https://docs.google.com/presentation/d/e/2PACX-1vSNmnBVoCnsJdfXLX1or5\_mLd8ERuzsu24kSPLOOGQnY9Qkk5kCTxJs5P0nE3dh2Ym7AJmu76mRJ5iJ/](https://docs.google.com/presentation/d/e/2PACX-1vSNmnBVoCnsJdfXLX1or5_mLd8ERuzsu24kSPLOOGQnY9Qkk5kCTxJs5P0nE3dh2Ym7AJmu76mRJ5iJ/embed?start=false&amp;loop=false&amp;delayms=3000&amp;slide=id.g5cfebaf157_0_6&amp;usp=embed_facebook&slide=id.g5cfebaf157_0_6)

自己總結如下:

1.在路由的後面絕對是要放上帶有\(req, res, next\)這些參數的函式。  
[![](https://assets-lighthouse.s3.amazonaws.com/uploads/attachment/attachment/6045/thumb_Image_1.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/attachment/attachment/6045/Image_1.png)  
2.另一種寫法，因為return回來會是一個函式，所以authMiddleware就是一個函式，而這個函式本身又帶有了\(req, res, next \)，因此能夠讓路由正常運行。

```javascript
const authMiddleware = passport.authenticate('local', {  
    successRedirect: '/',                                         
    failureRedirect: '/users/login'
  })
router.post('/login', authMiddleware})
```
{% endhint %}

{% hint style="info" %}
* 這個單元我們使用了好幾個工具，以下是它們的官方文件。我們建議你在實作後，花些時間去閱讀。雖然一開始不是所有內容都能看得懂，但你需要知道官方文件提供了什麼資訊。等到你累積了更多經驗後，就會有能力自己從官方文件提供的資訊去解決問題。
* [Express-session 的官方文件](https://www.npmjs.com/package/express-session)
* [如何設定 Passport](http://www.passportjs.org/docs/configure/)
* Session: 有關 [secret](https://www.npmjs.com/package/express-session#secret) 的官方介紹
* 也可以參考一些 PJ 老師的部落格文章：[Passport 學習筆記](https://pjchender.github.io/2017/09/26/node-passport-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98%EF%BC%88learn-to-use-passport-js%EF%BC%89/)
{% endhint %}

