# 使用passport-facebook

## passport-facebook介紹

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6575/ExportedContentImage_02.png)

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6576/ExportedContentImage_03.png)

A. 授權請求：客戶端向資源擁有者發送授權的請求。這個請求可以直接發送給資源擁有者，也可以間接經過授權伺服器。

B. 授權許可：客戶端取得資源擁有者給的「授權許可 \(authorization grant\)」。

C. Access token 請求：客戶端向授權伺服器請求一個 access token，這時客戶端要通過認證，並交出在上一個步驟取得，來自資源擁有者的授權許可。

D. Access token 簽發：授權伺服器認證客戶端並驗證授權許可正確無誤後，就會簽發給客戶端一個 access token。

E. 資料領取：客戶端向資源伺服器發出領取「受保護資料 \(protected resources\)」 的請求，同時交出 access token 來認證身分。

F. 回傳受保護資料：資源伺服器驗證來自客戶端的 access token，若驗證無誤，就回傳受保護資料到客戶端。

{% embed url="https://docs.google.com/presentation/d/e/2PACX-1vRKlGVGOSeS0FfhU1t5EPRDie6PA5BjB4iOs-JJ7HPxsPy\_X1c6DPtVagoUXBiQKRdpT1lVoCiN94sB/embed?start=false&amp;loop=false&amp;delayms=3000&amp;slide=id.p&amp;usp=embed\_facebook" %}

## **在 Facebook for developers 上設定一個應用程式**

 為了要建立 Facebook login 功能，我們需要先去新增一個 Facebook 的應用程式，藉此取得 Facebook 的 API Key。請到[ facebook for developers](https://developers.facebook.com/) 這個網站，在左上角「我的應用程式」裡找到「新增應用程式」的按鈕，為你的應用程式取個名字：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6581/ExportedContentImage_08.png)

在「選擇情境」的選項，請勾選「整合 Facebook 登入」：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6582/ExportedContentImage_09.png)

在左側導覽列點選「設定 &gt; 基本資料」，會看見以下畫面，請你在這裡取得「應用程式編號 \(App ID\)」和「應用程式密鑰 \(App Secret\)」兩個資訊。請把這兩個資料紀錄下來，我們之後會把它們寫入你的專案程式碼。

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6583/ExportedContentImage_10.png)

**設定轉址 redirect\_uri**

接著點選左側選單下面「Facebook 登入」的「設定」，在「有效的 OAuth 重新導向 URI」的地方進行設定 。這就是我們在 OAuth 流程裡提過的 redirect\_uri。

在開發者模式中，預設會開啟 `http://localhost`，所以現在不填寫任何東西也沒關係，正式上線後則強制需要有 https 的網址。

現在，請輸入以下網址：[`http://localhost:3000/auth/facebook/callback`](http://localhost:3000/auth/facebook/callback)

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6584/ExportedContentImage_11.png)

## 安裝 Passport-Facebook

```text
npm install passport-facebook
```

## 新增一個 '**/**auth' 路由

接下來，我們要新增一個提供 passport 的路由器能夠來處理 Facebook 的路徑。這個路由的程式碼可以參考 [Passport 的官方文件](https://github.com/jaredhanson/passport-facebook#authenticate-requests)：

![](../../.gitbook/assets/image%20%2810%29.png)

 從這個範例看來，我們會用 `passport.authenticate` 這個方法去認證從 Facebook 登入傳來的請求。現在請新增一個 `routes/auths.js` 檔案：

```javascript
// routes/auths.js
const express = require('express')
const router = express.Router()
const passport = require('passport')
router.get(
  '/facebook',
  passport.authenticate('facebook', { scope: ['email', 'public_profile'] })
)
router.get(
  '/facebook/callback',
  passport.authenticate('facebook', {
    successRedirect: '/',
    failureRedirect: '/users/login',
  })
)
module.exports = router
```

這裡我們建立了兩個路由，一個是使用者要透過 Facebook 登入時的網址 `/facebook`，另一個則是當我們取得「授權許可」後 redirect 的網址 `/facebook/callback`。  


![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6615/pasted_image_0.png)

在 Facebook 回傳的 callback 裡，我們要針對回傳的結果，執行不同的下一步。跟我們剛剛建立的 `passport-local` 的認證系統一樣，我們需要一併設定認證成功（`successRedirect`） 與認證失敗 （`failureRedirect`）時 passport 要執行的指令。

##  在 `app.js`載入並使用 auth 路由器

接下來，我們修改 `app.js` 把 auth 路由器載進來：

```javascript
// app.js
// 載入 todo model
// ...
// 設定路由
// 載入路由器
app.use('/', require('./routes/home'))
app.use('/todos', require('./routes/todo'))
app.use('/users', require('./routes/user'))     
app.use('/auth', require('./routes/auths'))    // 把 auth route 加進來
// 設定 express port 3000
// ...
```

## 建立 FacebookStrategy

然後就要在我們建立的 Passport middleware 裡加上 FacebookStrategy。

首先我們載入 `passport-facebook`：

```javascript
// config/passport.js
const LocalStrategy = require('passport-local').Strategy             // 載入 passport-local
const FacebookStrategy = require('passport-facebook').Strategy    // 載入 passport-facebook
```

在 `passport-facebook` [官方文件中](https://github.com/jaredhanson/passport-facebook#configure-strategy)，有提供這麼一段範例：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6587/ExportedContentImage_14.png)

有了這個範例，我們就可以在 LocalStartegy 下面新增一個 `FacebookStrategy`：

```javascript
// config/passport.js

const LocalStrategy = require('passport-local').Strategy           // 載入 passport-local
const FacebookStrategy = require('passport-facebook').Strategy    // 載入 passport-facebook

// …

const bcrypt = require('bcryptjs')        // 載入 bcryptjs library
const User = require('../models/user')    // 載入 User model

module.exports = passport => {
  passport.use(
    new LocalStrategy({ usernameField: 'email' }, (email, password, done) => {
      // …
      // …
    })
  )

  passport.use(
    new FacebookStrategy({
      clientID: 'xxxxxxxx',
      clientSecret: 'xxxxxxxx',
      callbackURL: 'http://localhost:3000/auth/facebook/callback',
      profileFields: ['email', 'displayName']
    }, (accessToken, refreshToken, profile, done) => {
      // find and create user
      User.findOne({
        email: profile._json.email
      }).then(user => {
        // 如果 email 不存在就建立新的使用者
        if (!user) {
           // 因為密碼是必填欄位，所以我們可以幫使用者隨機產生一組密碼，然後用 bcrypt 處理，再儲存起來
          var randomPassword = Math.random().toString(36).slice(-8)
          bcrypt.genSalt(10, (err, salt) =>
            bcrypt.hash(randomPassword, salt, (err, hash) => {
              var newUser = User({
                name: profile._json.name,
                email: profile._json.email,
                password: hash
              })
              newUser.save().then(user => {
                return done(null, user)
              }).catch(err => {
                console.log(err)
              })
            })
          )
        } else {
          return done(null, user)
        }
      })
    })
  )

  // passport serialiseUser & deserializeUser
  // ...

}
```

{% hint style="info" %}
這裡我們要注意幾件事：

1. 在建立 `FaceStrategy` 後，我們要輸入四個參數，而他們的值就是我們一開始從 facebook for developers 網址抄下來的資料：

   * `clientID`：應用程式編號
   * `clientSecret`：應用程式密鑰
   * `callbackURL`：在用戶端 OAuth 設定的重新導向 URI，我們剛剛設定為 `'`[`http://localhost:3000/auth/facebook/callback`](http://localhost:3000/auth/facebook/callback)`'`
   * `profileFields`：因為我們的 user schema 需要使用者的 email，而這就要到 Facebook 回傳的 `profile` 物件中去取。我們建議用 `console.log` 把 `profile._json` 印出來，去分析看看 Facebook 所提供的使用者資料有哪些。這是 Rex 老師在影片裡讀取 `profile_json` 時的資料。注意這些資料，他們之後有機會幫助你打造更客製化的產品！

     ![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6588/ExportedContentImage_15.png)

2. 把範例程式碼使用的 `cb`改為我們使用的 `done`
3. 在我們的 `user` schema 裡，除了 `email` 之外，另一個屬性 `password` 也是必填的，但當使用者選擇以 Facebook 註冊時，我們不會要求他再設定一個密碼。所以，我們要幫使用 Facebook 註冊的使用者，隨機產生一組密碼，然後用 bcrypt 處理，再儲存起來。
4. 這裡我們運用了進位制的數字變化來產生隨機密碼，乍看之下沒有那麼直覺，但這是蠻實用的小技巧：

   ```text
       var randomPassword = Math.random().toString(36).slice(-8)
   ```

   最後的目的是要取得 8 位數的隨機亂碼，讓我們一步步解釋：

   * `Math.random()` - 先用產生一個 0-1 的隨機小數，例如 0.3767988078359976
   * `.toString(36)` - 運用進位轉換將 0.3767988078359976 變成英數參雜的亂碼。這裡選用 36 進位，是因為 36 是 10 個數字 \(0, 1, 2, ... 9\) 加上 26 個英文字母 \(a, b, c, ... , x, y, z\) 的總數，在 36 進位裡剛好可以取得所有的英數字母。此時的回傳結果可能是 `'0.dkbxb14fqq4'`
   * `slice(-8)` - 最後，截切字串的最後一段，得到八個字母，例如 `'xb14fqq4'`。

   如果你還不熟悉 `toString()` 的應用，可以在終端機啟動一個 Node server 試試看。
{% endhint %}

## 檢查功能

 修改完成後，請到瀏覽器連上 `http://localhost:3000/auth/facebook` 看看是否可以使用 Facebook login。如果一切正常，你會看到下面這個畫面，代表 To-do List 在跟使用者索取 Facebook 授權才能登入。

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6590/ExportedContentImage_17.png)

登入成功之後，你應該就會被導引到 To-do List 的首頁。登入成功之後，你應該就會被導引到 To-do List 的首頁。

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6591/ExportedContentImage_18.png)

然後看一下資料庫，你應該能看到有新增的使用者，也就是我們 Facebook profile 物件上看到的 email 跟使用者名

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6592/ExportedContentImage_19.png)

稱。



