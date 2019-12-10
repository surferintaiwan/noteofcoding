# 打造註冊功能

### **處理從註冊頁面傳送來的請求**

```javascript
// routes/user.js

// 登入檢查
// ...

// 註冊檢查
router.post('/register', (req, res) => {
  const { name, email, password, password2 } = req.body
})

// 登出
// ...
```

這樣子寫，跟下面的程式碼在程式邏輯上是同樣的意思：

```javascript
const name = req.body.name
const email = req.body.email
const password = req.body.password
const password2 = req.body.password2
```

### 檢查使用者是否已經註冊

```javascript
// routes/user.js

// 登入檢查
// ...

// 註冊檢查
router.post('/register', (req, res) => {
  const { name, email, password, password2 } = req.body
  User.findOne({ email: email }).then(user => {
        if (user) {                           // 檢查 email 是否存在
      console.log('User already exists')
      res.render('register', {                // 使用者已經註冊過
        name,
        email,
        password,
        password2
      })
    } else {
      // 如果 email 不存在就新增使用者
      // 新增完成後導回首頁
    }
  })
})

// 登出
// ...
```

### 新增使用者

```javascript
// routes/user.js

// 登入檢查
// ...

// 註冊檢查
router.post('/register', (req, res) => {
  const { name, email, password, password2 } = req.body
  User.findOne({ email: email }).then(user => {
    if (user) {                                       // 檢查 email 是否存在
      console.log('User already exists')      
      res.render('register', {                // 使用者已經註冊過
        name,
        email,
        password,
        password2
      })
    } else {
      const newUser = new User({    // 如果 email 不存在就直接新增
        name,
        email,
        password
      })
      newUser
        .save()
        .then(user => {
          res.redirect('/')                         // 新增完成導回首頁
        })
        .catch(err => console.log(err))      
    }
  })
})

// 登出
// ...
```

