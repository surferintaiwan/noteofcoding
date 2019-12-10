# connect-flash增加警示訊息

「密碼有誤，請重新輸入。」、「您已成功登出，網頁將在三秒後跳轉⋯⋯」這些彷彿是網頁在對你說話的語句，就是系統訊息。為了讓使用者操作網頁應用程式時更加得心應手，我們要在註冊跟登入畫面加上不同的系統訊息。在這個單元，我們會使用 connect-flash 這個 middleware 去打造我們的系統訊息功能。

## 安裝connect-flash

```javascript
npm install connect-flash
```

## 在app.js載入

```javascript
// app.js
const express = require('express')
// ...
// ...

const flash = require('connect-flash')             // 載入 connect-flash   

// 使用 Express session
// ...

app.use(flash())                                                  // 使用 Connect flash

// 設定路由
// 載入路由器
// …
```

## `res.locals`儲存變數供view使用

1. `res.locals.success_msg`：展示「成功」訊息
2. `res.locals.warning_msg`：展示「警告」訊息

```javascript
// app.js
const express = require('express')
// ...
// ...

const flash = require('connect-flash')             // 載入 connect-flash   

// 使用 Express session
// …

app.use(flash())                                                  // 使用 Connect flash

// 建立 local variables
app.use((req, res, next) => {
  res.locals.user = req.user
  res.locals.isAuthenticated = req.isAuthenticated()    // 辨識使用者是否已經登入的變數，讓 view 可以使用

  // 新增兩個 flash message 變數 
  res.locals.success_msg = req.flash('success_msg')
  res.locals.warning_msg = req.flash('warning_msg')
  next()
})

// 設定路由
// 載入路由器
// …
// ...
```

## 新增`/views/partials/messages.handlebars`

要加入判斷式，依據不同情況顯示出不一樣的警示訊息。

```javascript
<!-- ./views/partials/messages.handlebars -->
{{#if errors}}
  {{#each errors}}
  <div class="alert alert-danger alert-dismissible fade show" role="alert">
    {{{this.message}}}
    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
      <span aria-hidden="true">×</span>
    </button>
  </div>
  {{/each}}
{{/if}}
{{#if success_msg}}
  <div class="alert alert-success alert-dismissible fade show" role="alert">
    {{{success_msg}}}
    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
      <span aria-hidden="true">×</span>
    </button>
  </div>
{{/if}}
{{#if warning_msg}}
  <div class="alert alert-warning alert-dismissible fade show" role="alert">
    {{{warning_msg}}}
    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
      <span aria-hidden="true">×</span>
    </button>
  </div>
{{/if}}
```

##  `{{> messages}}` 載入`messages.handlebars` 

### 在登入頁使用{{&gt; messages}}

```javascript
<!-- ./views/login.handlebars -->
<div class="row mt-5">
  <div class="col-md-6 m-auto">
    <div class="card card-body">
      <h1 class="text-center mb-3"><i class="fas fa-sign-in-alt"></i> Login</h1>
      <!-- 引用 messages 這個 partial view --> 
      {{> messages}}
      <form action="/users/login" method="POST">
        <div class="form-group">
          <label for="email">Email</label>
          <input type="email" id="email" name="email" class="form-control" placeholder="Enter Email" />
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input type="password" id="password" name="password" class="form-control" placeholder="Enter Password" />
        </div>
        <button type="submit" class="btn btn-primary btn-block">Login</button>
        <a href="/auth/facebook" class="btn btn-primary btn-block">Facebook Login</a>
      </form>
      <p class="lead mt-4">
        No Account? <a href="/users/register">Register</a>
      </p>
    </div>
  </div>
</div>
```

### 在註冊頁使用{{&gt; messages}}

```javascript
<!-- ./views/register.handlebars →
<div class="row mt-5">
  <div class="col-md-6 m-auto">
    <div class="card card-body">
      <h1 class="text-center mb-3">
        <i class="fas fa-user-plus"></i> Register
      </h1>
      <!-- 引用 messages 這個 partial view -->
      {{> messages}}
      <form action="/users/register" method="POST">
        <div class="form-group">
          <label for="name">Name</label>
          <input type="name" id="name" name="name" class="form-control" placeholder="Enter Name" value="{{{name}}}" />
        </div>
        <div class="form-group">
          <label for="email">Email</label>
          <input type="email" id="email" name="email" class="form-control" placeholder="Enter Email" value="{{{email}}}" />
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input type="password" id="password" name="password" class="form-control" placeholder="Create Password" value="{{{password}}}" />
        </div>
        <div class="form-group">
          <label for="password2">Confirm Password</label>
          <input type="password" id="password2" name="password2" class="form-control" placeholder="Confirm Password"
            value="{{{password2}}}" />
        </div>
        <button type="submit" class="btn btn-primary btn-block">
          Register
        </button>
      </form>
      <p class="lead mt-4">Have An Account? <a href="/users/login">Login</a></p>
    </div>
  </div>
</div>
```

## 依據不同狀況提供訊息

### 使用者權限的錯誤訊息

```javascript
// config/auth.js
module.exports = {
  authenticated: (req, res, next) => {
    if (req.isAuthenticated()) {
      return next()
    }  
    req.flash('warning_msg', '請先登入才能使用')          // 加上 warning_msg
    res.redirect('/users/login')
  },
}
```



![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6569/ExportedContentImage_03.png)

### 註冊流程的錯誤訊息

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6570/ExportedContentImage_04.png)

現在我們打開 `routes/user.js` 並在「註冊檢查」的路由的部分，建立處理錯誤訊息的邏輯。因為可能會同時產生多則訊息，所以我們會先建立一個 `errors[]` 陣列，當碰到錯誤時，就使用 `errors.push` 把要顯示的訊息推進這個陣列裡。最後我們就可以根據這個 `errors[]` 陣列的長度跟內容，輸出適當的系統訊息，是不是很周全呢？

```javascript
// routes/user.js

// 載入 passport
// 載入 User model

// 登入頁面
// ...

// 登入檢查
// ...

// 註冊檢查
router.post('/register', (req, res) => {
  const { name, email, password, password2 } = req.body
  
  // 加入錯誤訊息提示
  let errors = []

  if (!name || !email || !password || !password2) {
    errors.push({ message: '所有欄位都是必填' })
  }

  if (password !== password2) {
    errors.push({ message: '密碼輸入錯誤' })
  }

  if (errors.length > 0) {
    res.render('register', {
      errors,
      name,
      email,
      password,
      password2
    })
  } else {
    User.findOne({ email: email }).then(user => {
      if (user) {
        // 加入訊息提示
        errors.push({ message: '這個 Email 已經註冊過了' })
        res.render('register', {
          errors,
          name,
          email,
          password,
          password2
        })
      } else {
        const newUser = new User({
          name,
          email,
          password
        })
        bcrypt.genSalt(10, (err, salt) =>
          bcrypt.hash(newUser.password, salt, (err, hash) => {
            if (err) throw err
            newUser.password = hash

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
  }
})
```

### 登出的訊息

```javascript
// routes/user.js
// 載入 passport
// 載入 User model
// 登入頁面
// ...
// 登入檢查
// ...
// 註冊檢查
// ...
/ 登出
router.get('/logout', (req, res) => {
  req.logout()
  // 加入訊息提示
  req.flash('success_msg', '你已經成功登出')
  res.redirect('/users/login')
})
```

