# 佈署至heroku

## 申請Heroku帳號及新增應用程式

## [安裝heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)

## 修改文件檔案

### 新增`Procfile`

```text
web: node app.js
```

### 修改`mongoose.connect()`連線路徑

```javascript
// app.js
// 建立 mongoose 連線
mongoose.connect('mongodb://localhost/todo',{
  useNewUrlParser: true, 
  useCreateIndex: true 
})
```

修改成這樣：

```javascript
// app.js
// 建立 mongoose 連線
mongoose.connect(process.env.MONGODB_URI || 'mongodb://localhost/todo', {
  useNewUrlParser: true,
  useCreateIndex: true
})
```

### 修改`app.listen()`監聽器的`port`

```javascript
// app.js
// 設定 express port
app.listen(process.env.PORT || 3000, () => {
  console.log('App is running')
})
```

{% hint style="warning" %}
 **注意**：如果你希望用 seed files 來建立種子資料，那我們要在 `todoSeeder.js` 這個檔案中作同樣的修改。
{% endhint %}

### 記得要`git commit`

上傳到heroku的檔案，其實機制跟git一樣，都是看你上傳什麼版本上去，所以像前面檔但我們有更新了，就要記得commit，這樣推上去heroku的檔案才是最新的。

## 開始部署到heroku

### `heroku create`創建應用程式

如果在最一開始沒有在heroku官網上新增應用程式，那在這邊直接輸入`heroku create`也是可以的，heroku會自動幫你產生應用程式名稱。

接著會把你遠端上傳路徑做綁定，等一下就可以直接把專案推上去了。

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/7544/S6U118-01.png)

### `git push heroku master`把專案推上去

### `heroku config:set`設定`.env`裡變數的值

還記得我們有使用Facebook登入功能，所以必須在檔案中輸入申請下來的Facebook APP帳號、密碼、路徑，但是為了防止上傳到github時被人家看到，我們使用.env儲存這些資訊，並且用.gitignore擋掉.env的上傳。

```text
heroku config:set FACEBOOK_ID=1968097570150738 FACEBOOK_SECRET=3b625ce3109cc7ba125f7ce48c272b11 FACEBOOK_CALLBACK=https://damp-crag-84244.herokuapp.com/auth/facebook/callback
```

### 到facebook developer修改callback路徑

延續前面，我們已經把callback路徑修改為heroku網址的路徑，那就要一起改掉，不然facebook那邊會過不去。

### `heroku addons:create` 安裝MongoDB資料庫

我們在本地端用的資料庫是MongoDB，但是上了Heroku若要使用MongoDB就要安裝MongoLab這個套件才能用。

如果沒有綁定信用卡，它會教你先去綁定信用卡。

```text
heroku addons:create mongolab
```

### `heroku open`打開網頁

其實也可以直接輸入網址就能打開網頁了。

### `heroku log --tail`查看log是哪裡出狀況

