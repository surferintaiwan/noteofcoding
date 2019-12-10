# 註冊時把密碼加密



![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6551/ExportedContentImage_01.png)

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6552/ExportedContentImage_02.png)

## 安裝bcyyptjs

```text
npm install bcryptjs
```

## 修改'/users'路由

```javascript
// routes/user.js

const express = require('express')
const passport = require('passport')
const router = express.Router()

const passport = require('passport')    // 載入 passport
const bcrypt = require('bcryptjs')                   // 載入 bcryptjs library

const User = require('../models/user')  // 載入 User model

// 登入頁面
// …

// 登入檢查
// ...

// 註冊頁面
// ...

//  註冊檢查 

router.post('/register', (req, res) => {
  const { name, email, password, password2 } = req.body
  User.findOne({ email: email }).then(user => {
    if (user) {                               // 檢查 email 是否存在
      console.log('User already exists')      
      res.render('register', {                // 使用者已經註冊過
        name,
        email,
        password,
        password2
      })
    } else {
      const newUser = new User({              // 如果不存在就直接新增
        name,
        email,
        password
      })
      
      // 先用 genSalt 產生「鹽」，第一個參數是複雜度係數，預設值是 10
      bcrypt.genSalt(10, (err, salt) =>
        // 再用 hash 把鹽跟使用者的密碼配再一起，然後產生雜湊處理後的 hash
        bcrypt.hash(newUser.password, salt, (err, hash) => {
          if (err) throw err
          newUser.password = hash

          // 用 bcrypt 處理密碼後，再把它儲存起來
          newUser
            .save()
            .then(user => {
              res.redirect('/')
            })
            .catch(err => console.log(err))
        })
      )
    }
  })
})

// 登出
// ...
```

## 檢查功能

看資料庫就會發現密碼是亂碼了。

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6554/ExportedContentImage_04.png)

