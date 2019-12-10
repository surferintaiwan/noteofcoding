# 登入時用來比對

## 修改config/passport.js

因為現在 To-do List 會把使用者註冊時輸入的密碼，透過密碼雜湊處理過，所以我們也需要修改 `passport` 檢查使用者密碼的部分，使用 `bcrypt.compare` 這個函式，來判斷使用者登入時輸入的密碼是否與資料庫的內容一致。

```javascript
// config/passport.js

const LocalStrategy = require('passport-local').Strategy
const mongoose = require('mongoose')
const bcrypt = require('bcryptjs')        // 載入 bcryptjs library

const User = require('../models/user')    // 載入 User model

module.exports = passport => {
  passport.use(
    new LocalStrategy({ usernameField: 'email' }, (email, password, done) => {
      User.findOne({
        email: email
      }).then(user => {
        if (!user) {
          return done(null, false, { message: 'That email is not registered' })
        }

        /用 bcrypt 來比較「使用者輸入的密碼」跟在使用者資料庫的密碼是否是同一組字串
        bcrypt.compare(password, user.password, (err, isMatch) => {
          if (err) throw err
          if (isMatch) {
            return done(null, user)
          } else {
            return done(null, false, { message: 'Email and Password incorrect' })
          }
        })
      })
    })
  )
 
  // passport.serializeUser
  // ..

  // passport.deserializeUser
  // ..
}
```

