# 新增config/auth.js驗證每一頁的權限

## 新增config/auth.js

```javascript
// config/auth.js
module.exports = {
  authenticated: (req, res, next) => {
    if (req.isAuthenticated()) {
      return next()
    }
    res.redirect('/users/login')
  }
}
```

我們新增了一個名為 `auth` 的 middleware，並在裡面撰寫了一個名叫 `authenticated` 的方法。這個方法的功能就是去檢查 `req` 物件裡 `isAuthenticated` 的值。如果 `isAuthenticated` 是 TRUE，則我們執行下一個 middleware。如果是 FALSE，那就回到 login 頁面。

那 `isAuthenticated` 是什麼呢？其實它是 Passport 提供的方法，幫助我們檢查使用者是否已經登入。有興趣可以去參考 [Passport 的原始碼](https://github.com/jaredhanson/passport/blob/master/lib/http/request.js#L83)。

## 驗證各頁面的權限

都是在各個路由裡帶上前面寫的authenticated這個函式。

### 驗證首頁'/'

```javascript
// routes/home.js
const express = require('express')
const router = express.Router()
const Todo = require('../models/todo')

// 載入 auth middleware 裡的 authenticated 方法
const { authenticated } = require('../config/auth')

// 加入 authenticated 驗證
router.get('/', authenticated, (req, res) => {
  Todo.find({})
    .sort({ name: 'asc' })
    .exec((err, todos) => {
      if (err) return console.error(err);
      return res.render('index', { todos: todos });
    })
})
module.exports = router
```

### 驗證待辦事項相關頁面'/todos/'

```javascript
// routes/todo.js
const express = require('express')
const router = express.Router()
const Todo = require('../models/todo')

// 載入 auth middleware
const { authenticated } = require('../config/auth')

// 列出全部 Todo
router.get('/', authenticated, (req, res) => {
  return res.send('列出所有Todo')
})
// 新增一筆 Todo 頁面
router.get('/new', authenticated, (req, res) => {
  return res.render('new')
})
// 新增一筆  Todo
router.post('/', authenticated, (req, res) => {
  const todo = Todo({
    name: req.body.name,
  })
  todo.save(err => {
    if (err) return console.error(err)
    return res.redirect('/')
  })
})
// 修改 Todo 頁面
router.get('/:id', authenticated, (req, res) => {
  Todo.findById(req.params.id, (err, todo) => {
    return res.render('detail', { todo: todo })
  })
})
// 修改 Todo
router.put('/:id', authenticated, (req, res) => {
  Todo.findById(req.params.id, (err, todo) => {
    if (err) return console.error(err)
      todo.name = req.body.name
      if (req.body.done === 'on') {
      todo.done = true
    } else {
      todo.done = false
    }
    todo.save(err => {
      if (err) return console.error(err)
      return res.redirect(`/todos/${req.params.id}`)
    })
  })
})
// 刪除 Todo
router.delete('/:id/delete', authenticated, (req, res) => {
  Todo.findById(req.params.id, (err, todo) => {
    if (err) return console.error(err)
    todo.remove(err => {
      if (err) return console.error(err)
      return res.redirect('/')
    })
  })
})
module.exports = router
```

#### 

