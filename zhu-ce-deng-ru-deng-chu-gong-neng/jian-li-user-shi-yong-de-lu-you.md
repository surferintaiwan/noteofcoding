# 建立user使用的路由

### 建立user使用的路由

### 撰寫登入頁面handlebars

### 建立連線到資料庫的user model

新增一個models/user.js

```javascript
// models/user.js
const mongoose = require('mongoose')
const Schema = mongoose.Schema
const userSchema = new Schema({
  name: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true
  },
  password: {
    type: String,
    required: true
  },
  date: {
    type: Date,
    default: Date.now
  }
})
module.exports = mongoose.model('User', userSchema)
```

{% hint style="info" %}
最後面這個User會影響資料庫預設的命名，因為mongoose會自動偵測並給你資料庫裡的collection一個名字，這邊設為User，到時候collection的名字就會是users。  
沒錯，它就是把大寫變小寫並且幫你加上s

[https://mongoosejs.com/docs/api.html\#mongoose\_Mongoose-model](https://mongoosejs.com/docs/api.html#mongoose_Mongoose-model)
{% endhint %}



