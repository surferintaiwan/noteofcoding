# 建立mongoDB資料表關聯\(將user跟todo關聯\)

目前不同帳號登入都會看到同一份餐廳清單，現在要把user跟todo資料表關聯起來，這樣彼此就只會看到自己的餐廳資料。

![](../.gitbook/assets/image%20%2813%29.png)

## 增加userId到Todo model的Schema

```javascript
// model/todo.js

const mongoose = require('mongoose')
const Schema = mongoose.Schema

const todoSchema = new Schema({
  name: {
    type: String,
    required: true
  },
  done: {
    type: Boolean,
    default: false
  },
  // 加入 userId，建立跟 User 的關聯
  userId: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    index: true,
    required: true
  }
})

module.exports = mongoose.model('Todo', todoSchema)
```

在這裡，我們使用了 Mongoose 提供的 [Populate](https://mongoosejs.com/docs/populate.html) 功能。Populate 讓我們建立不同 collections 之間的關聯。在操作一份文件時，它的內容可以是從另一個 collection 的文件來的。

透過上面的程式碼，我們對 todo 裡 `userId` 這個屬性作了以下的設定：

* `type`（資料型別）：定義為一個 Mongoose 的 ObjectId
* `ref` （參考）：定義這個屬性是從 `User` 這個 model 裡取得
* `index`（索引）： 把 `userId` 設定成「索引 \(index\)」。MongoDB 支援 index 功能，使用 index 來查詢資料能夠增加讀取效能。

這個 `index` 的意思是：使 `userId` 能夠使用 MongoDB 提供的 index 功能，進而提升讀取資料的效能。（我們稍後會在延伸閱讀中提供有關 MongoDB 的 Index 功能的參考資料。）

用到 index 功能後，會建議在 mongoose 啟動連線時，加入 `useCreateIndex: true` 設定項：

```text
// app.js
// mongoDB 連線
mongoose.connect('mongodb://localhost/todo', {  useNewUrlParser: true,  useCreateIndex: true })
```

這個設定項沒有做也可以正常運行，只是在 terminal 可能會出現系統的貼心提醒：[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8800/ExportedContentImage_00.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8800/ExportedContentImage_00.png)

## 修改路由

### 修改 '/' 首頁路由

接下來，我們就要修改路由。先從首頁的路由開始。我們只需要把 `Todo.find({})` 修改為 `Todo.find({ userId: req.user._id })`，就可以了：

```javascript
// routes/home.js
// 載入 auth middleware
// ..
// 設定首頁路由器
router.get('/', authenticated, (req, res) => {
  Todo.find({ userId: req.user._id })            // 只會列出登入使用者的 todo
    .sort({ name: 'asc' })
    .exec((err, todos) => {
      if (err) return console.error(err)
      return res.render('index', { todos: todos })
    })
})
module.exports = router
```

我們在這裡使用 Mongoose 提供的 `.find` 並再加上參數，就可以限定查詢專屬於登入使用者的 todo。

[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6508/ExportedContentImage_03.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6508/ExportedContentImage_03.png)

### 修改 '/todos' 路由

接下來，我們會以類似的方法，來修改 todo 的路由。在 todo 路由的部分，我們要作這兩個修改：

1. 在 create action 加上 userId，它能夠把登入使用者的 ID \(`res.user._id`\) 儲存到 todo 裡
2. 在 detail, edit, delete 這幾個 actions 中把 `Todo.findById`修改為 `Todo.findOne({ _id: req.params.id, userId: req.user._id }`。因為我們不只是使用 todo 的 \_id 來查詢，更要找出同時符合 todo ID 以及 user ID 的那筆資料。

[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6509/ExportedContentImage_04.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6509/ExportedContentImage_04.png)

修改後，我們的路由會長成這樣 \(這裡的更新比較瑣碎，建議你可以參看示範的 commit 程式碼\)：

```javascript
// routes/todo.js

const express = require('express')
const router = express.Router()
const Todo = require('../models/todo')

// 載入 auth middleware
const { authenticated } = require('../config/auth')

// 設定 /todos 路由
// 列出全部 Todo
router.get('/', authenticated, (req, res) => {
  res.send('列出所有 Todo')
})
 
// 新增一筆 Todo 頁面
router.get('/new', authenticated, (req, res) => {
  return res.render('new')
})
 
// 顯示一筆 Todo 的詳細內容
router.get('/:id', authenticated, (req, res) => {
  // 要判斷是否為當前使用者的 todo
  Todo.findOne({ _id: req.params.id, userId: req.user._id }, (err, todo) => {
    if (err) return console.error(err)
    return res.render('detail', { todo: todo })
  })
})

// 新增一筆  Todo
router.post('/', authenticated, (req, res) => {
  const todo = Todo({                        
    name: req.body.name,   
    // 儲存 userId
    userId: req.user._id            
  })
  todo.save(err => {
    if (err) return console.error(err)
    return res.redirect('/')               
  })
})

// 修改 Todo 頁面
router.get('/:id/edit', authenticated, (req, res) => {
  // 要判斷是否為當前使用者的 todo
  Todo.findOne({ _id: req.params.id, userId: req.user._id }, (err, todo) => {
    return res.render('edit', { todo: todo })
  })
})
 
// 修改 Todo
router.put('/:id', authenticated, (req, res) => {
  // 要判斷是否為當前使用者的 todo
  Todo.findOne({ _id: req.params.id, userId: req.user._id }, (err, todo) => {
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
  // 要判斷是否為當前使用者的 todo
  Todo.findOne({ _id: req.params.id, userId: req.user._id }, (err, todo) => {
    if (err) return console.error(err)
    todo.remove(err => {
      if (err) return console.error(err)
      return res.redirect('/')
     })
  })
})

module.exports = router
```

## 檢查功能

完成後，請嘗試操作你的 To-do List，確定功能是完整的。請你新增一些 todos，然後打開 MongoDB 的 GUI 工具 \(Robo 3T 或是 Compass\)，你應該可以看到剛剛新增的 todo，都有 `userId` 這個屬性，且裡面的值也會對應到一個使用者的 `_id`。[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6510/ExportedContentImage_05.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6510/ExportedContentImage_05.png)



{% hint style="info" %}
#### 請問關於\_id這個值的取用方式

這個單元教我們取用\_id是用**\_id，**這個我理解，因為它資料裡就是用\_id去紀錄的。

```text
// routes/todos.js
// 瀏覽所有todo
router.get('/', authenticated, (req, res) => {
    Todo.find({userId: req.user._id})
    .sort({name: 'desc'})
    .exec((err, todos) => {
        if (err) return console.error(err)
        return res.render('index', {todos: todos})
    })
}) 
```

  
但我們之前在做index.handles要取用 \_id，**其實是用 this.id ，並不是用 this.\_id  
都一樣是取用物件中 \_id 的值，為什麼如下卻要用id??**

```text
// views/index.handlebars
<a href="/todos/new" class="btn">Create</a>

<ul class="list-group">
    {{#each todos}}
    <li class="list-group-item">
        <div class="row">
          <div class="col-auto mr-auto">
            <input type="checkbox" name="done" {{#if this.done}}checked{{/if}} disabled style="vertical-align: middle">
            <h4 style="display: inline; vertical-align: middle">{{this.name}}</h4>
          </div>
          <div class="col-auto ">
            <a href="/todos/{{this.id}}" class="btn btn-secondary">detail</a>
            <a href="/todos/{{this.id}}/edit" class="btn btn-success">edit</a>
            <form action="/todos/{{this.id}}?_method=DELETE" method="POST" style="display: inline">
                <button type="submit" class="btn btn-danger">delete</button>
            </form>
        </div>
    </li>
    {{/each}}
</ul> 
```

Answer:

用 \_id 是因為資料庫裡面儲存id的key就是 \_id  
req.user =&gt; 這是因為在登入的時候我們已經把資料庫中目前登入user的資料存到了這個變數之中  
你可以把req.user印出來看看長什麼樣子  
  
至於為什麼用沒有底線的id也可以：  
[https://mongoosejs.com/docs/guide.html\#id](https://mongoosejs.com/docs/guide.html#id)

```text
Mongoose assigns each of your schemas an id virtual getter by default which returns the documents _id field cast to a string, or in the case of ObjectIds, its hexString. If you don't want an id getter added to your schema, you may disable it passing this option at schema construction time.
```

是因為mongoose幫我們產生了一個虛擬屬性id，實際上跟\_id是一樣的東西
{% endhint %}

